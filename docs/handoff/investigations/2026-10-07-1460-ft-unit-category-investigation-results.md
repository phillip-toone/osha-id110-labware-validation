# OSHA ID-110 1460 F/T Unit-Category Investigation Results

**Prepared:** 2026-10-07  
**Environment:** OSHA_LIMS_DEV / LabWare 8  
**Primary tracking:** Jira `OSHA_LABS-3609`  
**Regression request:** `485928`  
**Regression batch:** `OSHA_ID-110-261007-1`

## 1. Executive conclusion

**Observed:** Fresh post-fix QCSM sample `66052` demonstrates that the shared theoretical-target correction is working. Its numeric `INGREDIENTS : Concentration` result is `90.262476717 UG`, has `ATTRIBUTE_1 = Fluoride (F)`, and resolves to unit category `MASS`. The working `1280 (Final)` result is stored as `96.0 UG / MASS`, and `1280 F/T` calculates `1.0635648776` (formatted `1.064`).

**Observed:** On the same sample, `1460 (Final)` is stored as `88.0 UG / MASS`, yet `1460 F/T` returns `Theoretical UNITS and actual results UNITS are not same category.` Direct reproduction of the found-unit lookup used by `CALC_QC_CONC` shows that the configured `1460 (Final)` dependency also resolves to `UG / MASS`. Therefore, the error is not explained by incompatible stored Final/theoretical units.

**Observed:** The only persisted calculation-variable difference between the working `1280 F/T` wrapper and failing `1460 F/T` wrapper is `found`. Working 1280 uses `ENTRY` with **Array** output. Failing 1460 uses `FORMATTED_ENTRY` with **Processed** output and function `AVE`. `theory` and `theoryElement` are otherwise identical.

**Conclusion (high confidence):** The smallest defensible correction/next diagnostic is to change only `1460 F/T -> found` so that it matches the working 1280 array contract: source `ISE : 1460 (Final)`, value `ENTRY`, output `Array`, with the existing trigger/scope retained. No evidence supports changing shared `CALC_QC_CONC`.

**Runtime proof is still required.** This configuration change had been recommended but had not yet been demonstrated as saved/retested at the time this handoff was prepared. The primary validation chat should apply/verify the configuration and prove the result on scientifically mapped QCSM036 sample `66058`. Expected recovery is approximately `88 / 90.262476717 = 0.974934`.

## 2. Evidence reviewed

The investigation reviewed the fresh regression request/batch, QCSM035/QCSM036 sets, diagnostic sample `66052`, mapped sample `66058`, current `CALC_QC_CONC` behavior, current ISE F/T configuration, GUI screenshots of both `found` variables, SQL result records, fresh theoretical INGREDIENTS results, SQL reproduction of the found-unit dependency lookup, and a complete persisted `CALC_VARIABLES` comparison.

Previously corrected configuration was treated as established context: `LAB_SOL00265.X_QCSM_TARGET = Fluoride (F)`, both F/T aliases are `Fluoride (F)`, and batch precision targets are `1280 F/T` and `1460 F/T`. No shared subroutine code was changed.

## 3. Working 1280 path

For diagnostic sample `66052`:

```text
1280 Mass    = 96.0 UG
1280 (Final) = 96.0 UG
```

Fresh theory:

```text
ENTRY       = 90.262476717
UNITS       = UG
ATTRIBUTE_1 = Fluoride (F)
CATEGORY    = MASS
```

Persisted `1280 F/T -> found` configuration:

```text
Source            = ISE : 1280 (Final)
Specific Analysis = True
Trigger           = Calculate when any rep entered
Scope             = All Results For Current Test
Value             = ENTRY
Output            = Array
Function          = [none]
```

SQL representation is `RETURN_VALUE=A`, `SCOPE=CT`, `FUNCTION=NULL`, `RESULT_FIELD=ENTRY`, `CALC_TRIGGER=1`.

Observed recovery:

```text
1280 F/T ENTRY           = 1.0635648776
1280 F/T FORMATTED_ENTRY = 1.064
UNITS                    = NONE
```

Arithmetic:

```text
96.0 / 90.262476717 = 1.0635648776...
```

The initial transient 1280 unit-category error followed by successful recalculation was observed but its exact evaluation-order mechanism was not established.

## 4. Failing 1460 path

For the controlled diagnostic comparison on sample `66052`:

```text
1460 Mass    = 88.0 UG
1460 (Final) = 88.0 UG
```

Expected recovery:

```text
88.0 / 90.262476717 = 0.974934...
```

Observed `1460 F/T`:

```text
Theoretical UNITS and actual results UNITS are not same category.
```

SQL shows both `1460 (Final)` and theory are `UG / MASS`. A query reproducing the `CALC_QC_CONC` found dependency lookup also resolves `1460 (Final)` to `88.0 UG / MASS`.

The persisted `1460 F/T -> found` configuration is instead:

```text
Source   = ISE : 1460 (Final)
Value    = FORMATTED_ENTRY
Output   = Processed
Function = AVE
```

SQL representation is `RETURN_VALUE=P`, `SCOPE=CT`, `FUNCTION=AVE`, `RESULT_FIELD=FORMATTED_ENTRY`, `CALC_TRIGGER=1`.

## 5. Unit/category trace

| Item | 1280 path | 1460 path |
| --- | --- | --- |
| Final numeric value | `96.0` | `88.0` |
| Final stored unit | `UG` | `UG` |
| Final unit category | `MASS` | `MASS` |
| Theory value | `90.262476717` | `90.262476717` |
| Theory stored unit | `UG` | `UG` |
| Theory unit category | `MASS` | `MASS` |
| Theory target | `Fluoride (F)` | `Fluoride (F)` |
| Found dependency SQL unit | `UG / MASS` | `UG / MASS` |
| F/T outcome | `1.0635648776` | unit-category error |
| `found` value field | `ENTRY` | `FORMATTED_ENTRY` |
| `found` output | `Array` | `Processed` |
| `found` function | none | `AVE` |

The category error therefore cannot be attributed to the persisted `UG/MASS` metadata of either Final or theory.

## 6. Configuration comparison

### 6.1 F/T wrappers

| Property | `1280 F/T` | `1460 F/T` | Assessment |
| --- | --- | --- | --- |
| Alias | `Fluoride (F)` | `Fluoride (F)` | Correct/equivalent |
| Wrapper | `GOSUB CALC_QC_CONC` | same | Correct/equivalent |
| `found` source | `1280 (Final)` | `1460 (Final)` | Expected difference |
| `found` trigger | any rep entered | any rep entered | Equivalent |
| `found` scope | current test | current test | Equivalent |
| `found` value | `ENTRY` | `FORMATTED_ENTRY` | **Suspicious difference** |
| `found` output | `Array` | `Processed` | **Suspicious difference** |
| `found` function | none | `AVE` | **Suspicious difference** |
| `theory` | INGREDIENTS Concentration / ENTRY / Array | same | Equivalent |
| `theoryElement` | INGREDIENTS Concentration / ATTRIBUTE_1 / Array | same | Equivalent |

The SQL comparison established that `found` is the only calculation-variable difference.

### 6.2 Final results

| Property | `1280 (Final)` | `1460 (Final)` |
| --- | ---: | ---: |
| ENTRY | `96.0` | `88.0` |
| FORMATTED_ENTRY | `96.0000` | `88.0000` |
| NUMERIC_ENTRY | `96` | `88` |
| UNITS | `UG` | `UG` |
| Alias | `1280` | `1460` |

Both Finals carry the unit metadata needed for a MASS-to-MASS recovery calculation. A visually blank Units cell in Result Entry is not evidence that the database result lacks units.

## 7. Role of recalculation/order

The fresh 1280 path initially displayed the same unit-category message and later recalculated successfully to `1.064`. The exact transient mechanism was not proven. Evaluation order or dependent-result state may be involved, but current evidence is insufficient to label that a defect.

For 1460, the failure is persistent despite compatible stored Final/theory units. Its persisted `found` variable differs from the working 1280 configuration, making that the stronger and directly observed difference to correct/test first.

If 1460 still fails after its `found` configuration is made identical in structure to 1280, runtime instrumentation/context tracing would then be justified.

## 8. Recommended correction / next diagnostic

Change only `ISE : 1460 F/T -> found`:

```text
Current:
Source   = ISE : 1460 (Final)
Value    = FORMATTED_ENTRY
Output   = Processed
Function = AVE

Recommended:
Source   = ISE : 1460 (Final)   [unchanged]
Value    = ENTRY
Output   = Array
Function = [not applicable]
```

Retain:

```text
For Specific Analysis = True
Trigger = Calculate when any rep entered
Scope   = All Results For Current Test
```

**Confidence:** High confidence as the smallest correct next diagnostic/configuration correction. This is not yet a claim of runtime resolution.

**Shared code:** Do not change `CALC_QC_CONC`. Current evidence does not demonstrate a shared-routine defect.

## 9. Regression instructions

The primary validation chat should:

1. Save/verify `1460 F/T -> found` as `ENTRY / Array`, retaining its source, current-test scope, and calculate-when-any-rep-entered trigger.
2. Verify persisted `CALC_VARIABLES` shows approximately:
   ```text
   COMPONENT    = 1460 F/T
   NAME         = found
   ATTRIBUTE_1  = 1460 (Final)
   RETURN_VALUE = A
   SCOPE        = CT
   FUNCTION     = NULL
   RESULT_FIELD = ENTRY
   ```
3. Use scientifically mapped fresh QCSM036 sample `66058 / QCSM036-0004-001`.
4. Use the historical analog values: pH `7.321`, solution volume `50 mL`, aliquot factor `1`, E0 `70.6 mV`, EF- `22.8 mV`, concentration `1.76 ppm`.
5. Confirm `1460 Mass = 88 UG` and `1460 (Final) = 88 UG`.
6. Confirm theory `90.262476717 UG`, `ATTRIBUTE_1 = Fluoride (F)`, category `MASS`.
7. Recalculate `1460 F/T`.
8. Expected recovery: `88 / 90.262476717 = approximately 0.974934` (display subject to component formatting).
9. Preserve screenshots/SQL evidence of Final units, theoretical units/attribute, persisted `found` configuration, and corrected F/T result.
10. After the first nonzero 1460 case passes, continue the planned QCSM036 series and zero-spike validation. Evaluate batch precision only after individual recoveries are established.

Sample `66052` should remain identified as a diagnostic cross-path comparison; `66058` is the scientifically mapped 1460 10-uL regression member.

## 10. Remaining uncertainties

- The exact reason the working 1280 path initially emitted the unit-category message and later recalculated successfully was not established.
- The exact internal runtime representation produced by `Processed / AVE / FORMATTED_ENTRY` was not instrumented directly. Its persisted difference and runtime divergence make it the strongest next target, but post-change runtime proof remains required.
- If 1460 continues to fail after changing `found` to `ENTRY / Array`, the next diagnostic should expose runtime `foundResultUnit`, `foundResultUnitCat`, `theoryUnit`, and `theoryUnitCat` inside `CALC_QC_CONC` rather than changing shared logic speculatively.
- Broader QCSM035/QCSM036 regression and batch precision validation remain with the primary validation chat.

## 11. Repository-ready finding summary

Fresh regression request `485928` demonstrates that the prior QCSM target/alias correction is working: sample 66052 has theoretical `INGREDIENTS : Concentration = 90.262476717 UG`, `ATTRIBUTE_1 = Fluoride (F)`, category `MASS`, and `1280 F/T = 1.0635648776` (`1.064` displayed). The 1460 diagnostic path on the same sample has `1460 (Final) = 88.0 UG`, also category `MASS`, but `1460 F/T` reports a theoretical-versus-actual unit-category mismatch. SQL reproduction of `CALC_QC_CONC`'s found dependency lookup confirms that `1460 (Final)` resolves to `UG/MASS`, so the error is not explained by stored Final/theoretical unit metadata. A complete `CALC_VARIABLES` comparison shows that `theory` and `theoryElement` are identical; the only variable difference is `found`: working 1280 uses `ENTRY / Array`, while failing 1460 persists as `FORMATTED_ENTRY / Processed / AVE`. This contradicts the intended post-fix state recorded in the incoming handoff. The recommended smallest correction is therefore to configure `1460 F/T -> found` as `ENTRY / Array` while retaining existing source/trigger/scope. No shared `CALC_QC_CONC` change is recommended. Runtime resolution must be proven on scientifically mapped QCSM036 sample 66058, where expected recovery is approximately `0.974934`.

## 12. Jira-ready note for OSHA_LABS-3609

Investigated fresh-runtime `1460 F/T` unit-category failure in request `485928` / batch `OSHA_ID-110-261007-1`. Fresh sample 66052 proves the prior theoretical target correction is effective: theoretical fluoride is `90.262476717 UG`, `ATTRIBUTE_1 = Fluoride (F)`, and `1280 F/T` now calculates correctly at `1.0635648776` (`1.064` displayed). `1460 (Final)` is also correctly stored as `88.0 UG`; both actual and theoretical units resolve to category `MASS`, including when reproducing the found dependency lookup used by `CALC_QC_CONC`. The first material divergence is the `1460 F/T` `found` calculation variable: it remains `FORMATTED_ENTRY / Processed / AVE`, whereas working 1280 is `ENTRY / Array`; all `theory`/`theoryElement` settings are identical. Recommend changing only `1460 F/T -> found` to `ENTRY / Array` and retaining existing source/trigger/scope. No shared subroutine change recommended. Post-change regression proof remains required on QCSM036 sample 66058; expected F/T is approximately `0.974934`.
