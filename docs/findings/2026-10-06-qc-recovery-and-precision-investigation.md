# OSHA ID-110 `CALC_QC_CONC` Investigation Findings

## 1. Executive conclusion

Individual OSHA ID-110 QCSM F/T recovery was **not working correctly in the pre-change configuration**. The shared `CALC_QC_CONC` routine requires the current recovery component's `COMPONENT.ALIAS_NAME` (`targetElement`) to match the QCSM theoretical `INGREDIENTS : Concentration` result's `RESULT.ATTRIBUTE_1`. For the controlled QCSM trace, sample `65977`, the usable theoretical result was `90.262476717 UG` with `ATTRIBUTE_1 = Sodium Fluoride`, while `1280 F/T` had `ALIAS_NAME = Fluoride (F)` and `1460 F/T` had a null alias. Neither component could select the valid theoretical row. Observed pre-change F/T results were consequently wrong: `1280 F/T = 96.0` and `1460 F/T = 0` rather than approximately `1.063565` and `0.974934`.

The investigation did **not** demonstrate a defect requiring modification of shared `CALC_QC_CONC`. Instead, it demonstrated an OSHA ID-110 configuration mismatch. `CALC_INGRED_CONC` already supports `STOCK.X_QCSM_TARGET` as an explicit target identifier and applies the configured `X_MOLE_FRACTION = 0.4525` for the Sodium Fluoride reagent/analyte conversion. The implemented correction was to set `LAB_SOL00265.X_QCSM_TARGET = Fluoride (F)` and set `1460 F/T.ALIAS_NAME = Fluoride (F)`; `1280 F/T` already had that alias.

A separate batch-precision defect was also confirmed. The OSHA_ID-110 batch components had been renamed for fluoride but retained copied OSHA_5003 calculations: `F (1280) QCSM Precision` searched for `As F/T`, and `HF (1460) QCSM Precision` searched for `Cd F/T`. This directly explains the earlier "No QCSM results have been entered" message. Those target strings were changed to `1280 F/T` and `1460 F/T`, respectively.

The four agreed configuration corrections have now been implemented and configuration-verified. **Analytical regression/recalculation after the changes has intentionally been deferred to the main testing/verification phase.** Therefore the configuration defects and corrections are confirmed, but the corrected runtime F/T and batch-precision outputs remain to be validated.

## 2. Evidence examined

- Complete current source of shared subroutine `CALC_QC_CONC`.
- Complete current source of shared subroutine `CALC_INGRED_CONC`.
- Complete current source of shared subroutine `CALC_BATCH_QC_REC_PRECISION`.
- OSHA implementation guidance: `Sample Testing Configuration.docx` and `Batch and Analysis Configuration Requirements.docx`.
- ISE component configuration for `1280 F/T` and `1460 F/T`, including aliases, wrapper calculations, and calculation variables `found`, `theory`, and `theoryElement`.
- QCSM samples `65977`-`65980` from `QCSM036-0003`, especially controlled trace sample `65977`.
- `RESULT` data for sample `65977`, including ISE Final/F/T results and INGREDIENTS results.
- INGREDIENTS tests `74265` and `74266` for sample `65977`.
- Stock/inventory configuration for `STD02189`, `LAB_SOL00265`, and `QCSM036`, including `T_STOCK_INGREDIENTS.X_MOLE_FRACTION = 0.4525` for `LAB_SOL00265` / `STD02189`.
- Historical QCSM035/QCSM036 theoretical Concentration results showing systematic `ATTRIBUTE_1 = Sodium Fluoride`.
- Known-good comparator `ICP_OES : Be F/T`, whose alias and QCSM theoretical attribute both equal `Beryllium (Be)`.
- Additional `X_QCSM_TARGET` comparators for Quartz, Cristobalite, and Stoddard Solvent, where configured targets match corresponding F/T aliases.
- Current batch `OSHA_ID-110-261005-1`, including `BATCH_OBJECTS`, current F/T result selection, batch precision components, and their calculations.
- OSHA_5003 `As QCSM Precision` / `Cd QCSM Precision` and OSHA_ID-101 fluoride-named precision components, used to establish copied-configuration provenance.
- Before/after configuration verification for `LAB_SOL00265.X_QCSM_TARGET` and ISE F/T aliases.

## 3. Confirmed data flow

For the 1460 path, the verified intended flow is:

```text
Entered 1460 Concentration
        |
        v
1460 Mass
        |
        v
1460 (Final) [QCSM mass in UG]
        |
        v
found[1] ------------------------------------+
                                             |
QCSM INGREDIENTS Concentration.ENTRY --------+--> theory[i]
QCSM INGREDIENTS Concentration.ATTRIBUTE_1 --+--> theoryElement[i]
1460 F/T COMPONENT.ALIAS_NAME ---------------> targetElement
                                             |
                                             v
                                      CALC_QC_CONC
                                             |
                                             v
                                      1460 F/T recovery
```

The analogous path applies to 1280.

The F/T wrapper initially supplies three arrays/variables. `found` points to the applicable Final component. `theory` is configured as an array of `INGREDIENTS : Concentration` `ENTRY` values, and `theoryElement` is configured as the corresponding array of `ATTRIBUTE_1` values. For QCSM/RLSM samples, however, `CALC_QC_CONC` clears and rebuilds `theory` and `theoryElement` from a query against the current sample's `Concentration` results.

For a populated `targetElement`, that query requires `RESULT.ATTRIBUTE_1 = targetElement`. The rebuilt arrays correspond to the query columns: theoretical amount (`ENTRY`), theoretical identifier (`ATTRIBUTE_1`), and units (`UNITS`). The subsequent loop skips any `theoryElement[i]` that does not equal `targetElement`.

The normal nonzero recovery branch uses `found[1]` as the numerator, determines the found and theoretical unit categories, converts `found[1]` into the theoretical units with `ConvertUnits`, and divides the converted amount by `theory[i]`. The result units are then set to `NONE`.

`found` can legitimately be configured differently between analyses/components. `1280 F/T` uses an Array/`ENTRY` pattern matching the known-good `ICP_OES : Be F/T`. `1460 F/T` uses Processed/`AVE`/`FORMATTED_ENTRY`; that exact pattern also exists on several GC F/T components, so it was not treated as defective merely because it differs from 1280.

## 4. `targetElement` / `theoryElement` findings

Pre-change observed values were:

```text
1280 F/T COMPONENT.ALIAS_NAME = Fluoride (F)
1460 F/T COMPONENT.ALIAS_NAME = NULL
65977 theoretical RESULT.ATTRIBUTE_1 = Sodium Fluoride
```

Thus neither recovery component could match the only usable numeric theoretical result on sample `65977`.

The known-good comparator established the expected matching convention:

```text
ICP_OES Be F/T COMPONENT.ALIAS_NAME = Beryllium (Be)
Be QCSM theoretical RESULT.ATTRIBUTE_1 = Beryllium (Be)
```

Multiple actual Beryllium QCSMs followed this pattern.

The theoretical identifier `Sodium Fluoride` was not a one-off result. It appeared systematically across multiple QCSM035 and QCSM036 lots. Tracing `CALC_INGRED_CONC` showed that it derives a target identifier and writes it to the calculated Concentration result's `ATTRIBUTE_1`. `STOCK.X_QCSM_TARGET`, when populated, takes precedence over inherited target naming. Other configured examples (Quartz, Cristobalite, Stoddard Solvent) demonstrate that `X_QCSM_TARGET` is used to create identifiers that match F/T aliases.

Because the ID-110 intermediate stock uses `X_MOLE_FRACTION = 0.4525` to convert the Sodium Fluoride reagent-derived amount to the analyte amount, the selected target identity for ID-110 was `Fluoride (F)`, rather than relabeling the F/T components as `Sodium Fluoride`.

Post-change configuration verification shows:

```text
COMPONENT  1280 F/T      Fluoride (F)
COMPONENT  1460 F/T      Fluoride (F)
STOCK      LAB_SOL00265   Fluoride (F)
```

## 5. Unit-handling findings

For sample `65977`:

```text
1280 (Final) = 96.0 UG
1460 (Final) = 88.0 UG
Theoretical  = 90.262476717 UG
```

The Final and theoretical results are all mass values in `UG`, so the normal `CALC_QC_CONC` conversion is an identity conversion (`UG` to `UG`) before division. The routine explicitly requires found and theoretical unit categories to match; otherwise it returns the message `Theoretical UNITS and actual results UNITS are not same category.`

No unit incompatibility was demonstrated for OSHA ID-110. The observed failure was the target lookup/configuration mismatch, not units.

## 6. Worked QCSM example

```text
Sample: 65977 / QCSM036-0003-001
Analyte/path: 1280 F/T
Final value: 96.0
Final units: UG
Theoretical value: 90.262476717
Theoretical units: UG
Converted Final value: 96.0 UG
Expected F/T ratio: 96.0 / 90.262476717 = approximately 1.063565
Actual pre-change LabWare F/T result: 96.0 NONE
Pass/fail: FAIL (pre-change)
```

For the 1460 path on the same sample:

```text
Sample: 65977 / QCSM036-0003-001
Analyte/path: 1460 F/T
Final value: 88.0
Final units: UG
Theoretical value: 90.262476717
Theoretical units: UG
Converted Final value: 88.0 UG
Expected F/T ratio: 88.0 / 90.262476717 = approximately 0.974934
Actual pre-change LabWare F/T result: 0 NONE
Pass/fail: FAIL (pre-change)
```

The same failure signature reproduced on sample `65978`: `1280 (Final) = 183.0 UG` with `1280 F/T = 183.0`, and `1460 (Final) = 167.0 UG` with `1460 F/T = 0`. Its theoretical amount is `180.52495343 UG`, so the expected ratios are approximately `1.01371` and `0.92508`, respectively.

The exact internal reason the failed no-match path left 1280 equal to Final while 1460 became zero was not runtime-debugged. That detail is not required to establish the causal lookup defect: both components were proven unable to select the valid theoretical row.

## 7. Defects or configuration issues

### 7.1 Confirmed: ID-110 theoretical-target / F/T alias mismatch

**Defect:** The generated theoretical Concentration target and F/T aliases did not satisfy the exact-match contract required by `CALC_QC_CONC`.

**Evidence:** Pre-change theory `ATTRIBUTE_1 = Sodium Fluoride`; `1280 F/T.ALIAS_NAME = Fluoride (F)`; `1460 F/T.ALIAS_NAME = NULL`. Known-good Be configuration demonstrated exact alias/attribute matching. `CALC_INGRED_CONC` demonstrated the supported `X_QCSM_TARGET` mechanism.

**Consequence:** Normal nonzero QCSM recovery could not retrieve the theoretical denominator. Observed F/T outputs were incorrect.

**Implemented correction:** `LAB_SOL00265.X_QCSM_TARGET` was set to `Fluoride (F)`, and `1460 F/T.ALIAS_NAME` was set to `Fluoride (F)`. `1280 F/T` already had the correct alias.

**Risk/impact:** This is ID-110-specific stock/component configuration. No shared `CALC_QC_CONC` code change was made or recommended.

**Implementation note:** `LAB_SOL00265.X_QCSM_TARGET` was updated directly by SQL because the field could not be located in the available Stock GUI. The direct SQL update changed the value but did not update `STOCK.CHANGED_BY` or `STOCK.CHANGED_ON`; those remained `PTOONE` / `07/14/2026 06:49:08 AM`. This should be preserved in the change record.

### 7.2 Confirmed: copied batch-precision targets

**Defect:** The OSHA_ID-110 batch precision wrappers retained unrelated metals targets:

```text
F (1280) QCSM Precision:  targetElement = "As F/T"
HF (1460) QCSM Precision: targetElement = "Cd F/T"
```

**Evidence:** The exact `As F/T` and `Cd F/T` calculations were observed in OSHA_5003 `As QCSM Precision` and `Cd QCSM Precision`. OSHA_ID-110 components occupy corresponding order positions 23/24 and retained those embedded target strings after being renamed. OSHA_ID-101 fluoride-named precision components also contain the same stale As/Cd targets, showing the inherited problem predates ID-110.

**Consequence:** `CALC_BATCH_QC_REC_PRECISION` searched the ID-110 batch for `As F/T` / `Cd F/T`, found zero rows, and displayed `No QCSM results have been entered. Recovery Precision cannot be calculated.`

**Implemented correction:** The wrappers were changed to:

```text
F (1280) QCSM Precision:
    targetElement = "1280 F/T"
    GOSUB CALC_BATCH_QC_REC_PRECISION

HF (1460) QCSM Precision:
    targetElement = "1460 F/T"
    GOSUB CALC_BATCH_QC_REC_PRECISION
```

**Risk/impact:** These are OSHA_ID-110 batch-template corrections only. No shared precision subroutine change was made.

### 7.3 Unresolved source concern: incomplete-result check

`CALC_BATCH_QC_REC_PRECISION` checks:

```text
UBound(valArr, 1) < UBound(valArr, 2)
```

although `valArr` is populated by a two-column query (`ENTRY, UNITS`). This appears suspicious as a completeness test because it may compare row count with column count rather than entered QCSMs with expected QCSMs. LabWare runtime array semantics were not debugged sufficiently to classify this as a confirmed defect. It is not the cause of the demonstrated ID-110 failures and should be handled separately if needed.

## 8. Batch QCSM precision findings

`CALC_BATCH_QC_REC_PRECISION` queries current-batch sample numbers from `BATCH_OBJECTS`, then selects `RESULT.ENTRY, RESULT.UNITS` for rows whose `NAME LIKE targetElement` and `UNITS = 'NONE'`. It calculates precision as:

```text
precisionRange = max(recovery values) - min(recovery values)
```

The current batch's `BATCH_OBJECTS` records contain both test-number `OBJECT_ID` and the associated `SAMPLE_NUMBER`, so the routine's batch membership lookup works in this implementation.

Before wrapper correction, the routine searched for `As F/T` and `Cd F/T`, explaining the zero-row message. With candidate correct names reproduced in SQL, the current batch would select only QCSM rows; no field-sample F/T rows were found. At the time examined, only samples `65977` and `65978` had F/T results:

```text
1280 F/T: 96.0, 183.0
1460 F/T: 0, 0
```

Thus the routine would mechanically have produced ranges `87.0` and `0` if pointed at those names before individual recovery was corrected, but those values would be analytically meaningless because the underlying F/T results were wrong.

The QCSM036-only batch composition did not itself cause the demonstrated precision failure. Overall validation coverage regarding QCSM035 versus QCSM036 remains a separate validation-thread question.

Post-change batch precision has not yet been executed; it belongs to the regression testing phase after individual F/T recovery is recalculated and verified.

## 9. Recommended next validation steps

1. Recalculate/regenerate the relevant INGREDIENTS Concentration result for a controlled nonzero QCSM (preferably `65977`) and confirm `ATTRIBUTE_1 = Fluoride (F)` while the expected theoretical amount remains `90.262476717 UG` (or explain any expected regeneration behavior if existing results do not automatically update).
2. Recalculate `1280 F/T` and `1460 F/T` for `65977`; verify approximately `1.063565` and `0.974934`, respectively, with units `NONE`.
3. Repeat on at least one additional nonzero QCSM (`65978` recommended) to demonstrate the correction is not sample-specific.
4. Exercise the zero-spike QCSM (`65980`) separately and verify the routine's zero-theory behavior and limits.
5. Once individual F/T results are correct for the required QCSMs, execute `F (1280) QCSM Precision` and `HF (1460) QCSM Precision` and verify that they select the corrected F/T rows and return the expected recovery range.
6. Preserve screenshots/SQL evidence of post-change theoretical `ATTRIBUTE_1`, Final values/units, F/T results/units, and batch precision results.
7. Decide in the main validation thread whether QCSM035 must also be included for planned regression coverage; this investigation did not resolve that separate coverage question.
8. Optionally open a separate technical review of the `UBound(valArr,1) < UBound(valArr,2)` completeness check in `CALC_BATCH_QC_REC_PRECISION`.

## 10. Jira-ready summary

Investigated OSHA ID-110 QCSM recovery through shared `CALC_QC_CONC` and the downstream batch QCSM precision calculation. Controlled trace of QCSM `65977` demonstrated that the valid theoretical Concentration (`90.262476717 UG`) was stored with `ATTRIBUTE_1 = Sodium Fluoride`, while `1280 F/T` targeted `Fluoride (F)` and `1460 F/T` had no alias, so neither recovery component could select the theoretical denominator required by `CALC_QC_CONC`. Pre-change results were incorrect (`1280 F/T = 96.0`; `1460 F/T = 0`). `CALC_INGRED_CONC` and known-good comparators established `X_QCSM_TARGET`/F/T alias matching as the supported configuration mechanism; ID-110 also applies `X_MOLE_FRACTION = 0.4525` for NaF-to-analyte conversion. Implemented `LAB_SOL00265.X_QCSM_TARGET = Fluoride (F)` and `1460 F/T.ALIAS_NAME = Fluoride (F)`; `1280 F/T` already matched. Separately, found that ID-110 batch precision wrappers were copied from OSHA_5003 and still targeted `As F/T` and `Cd F/T`, directly explaining the "No QCSM results have been entered" message. Updated those targets to `1280 F/T` and `1460 F/T`. No shared `CALC_QC_CONC` or `CALC_BATCH_QC_REC_PRECISION` code was changed. Configuration-only verification confirms both F/T aliases and `LAB_SOL00265.X_QCSM_TARGET` now equal `Fluoride (F)`. Post-change analytical recalculation/regression remains required, including nonzero F/T recovery, zero-spike behavior, and batch precision.

## 11. Evidence still needed / unresolved questions

- Post-change runtime evidence that regenerated/recalculated QCSM theoretical Concentration uses `ATTRIBUTE_1 = Fluoride (F)`.
- Post-change `1280 F/T` and `1460 F/T` results for at least `65977` and preferably `65978`.
- Post-change zero-theory behavior for `65980`.
- Post-change batch precision results after all required individual recoveries exist.
- Whether the suspicious `UBound(valArr,1) < UBound(valArr,2)` completeness check is defective under LabWare runtime array semantics.
- Overall validation coverage decision regarding QCSM035 versus the current QCSM036-only batch.
