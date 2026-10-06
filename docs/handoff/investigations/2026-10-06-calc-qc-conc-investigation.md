# OSHA ID-110 / `CALC_QC_CONC` Investigation Handoff

## Purpose of this handoff

This is a focused technical investigation spun off from the main OSHA ID-110 (ISE) LabWare validation effort. The main validation thread has already established that individual result entry works and that the entered concentration -> Mass -> `(Final)` calculations are producing the expected values from the validation data packet. Do **not** reopen those questions unless new evidence demonstrates that they are involved in the remaining problem.

The purpose of this investigation is to determine exactly how the shared LabWare subroutine `CALC_QC_CONC` calculates QCSM recovery for the OSHA ID-110 components `1280 F/T` and `1460 F/T`, determine whether the current LabWare configuration is sufficient/correct, identify any defect if one exists, and return a concise, evidence-based finding to the main validation thread.

This is an investigation, not an authorization to modify production/configuration. Do not recommend or make a code/configuration change until the existing behavior and dependency chain have been demonstrated.

---

## Main validation context

We are validating OSHA ID-110 Ion Selective Electrode (ISE) analysis in LabWare. The regression batch is `OSHA_ID-110-261005-1` and is associated with sampling/request number `485927`.

The batch contains seven field samples (`65970` through `65976`) and four QCSM samples (`65977` through `65980`) from `QCSM036-0003`. The individual result-entry workflow has already been figured out and is working. Analysts are manually entering the relevant ISE concentration results. Do **not** spend time rediscovering how to enter individual results.

The batch-level instruments, reagents, buffers, and standards have also been entered. That setup is not the focus of this investigation.

A separate validation concern exists because the planned regression originally contemplated both QCSM035 and QCSM036, while the current batch contains QCSM036 only. That may matter to overall validation coverage, but it is **not** the primary question for this investigation unless it directly affects the behavior of `CALC_QC_CONC`.

---

## What is already known to work

### Manual analytical input

`ISE : 1460 Concentration` is manually entered. The analogous 1280 concentration is also part of the manual analytical result-entry path.

Do not search for a missing upstream instrument-import mechanism unless evidence contradicts this established behavior.

### `1460 Mass`

The `1460 Mass` component is calculated as:

```text
RETURN solutionVolume*aliquotFactor*ppmFluoride
```

with inputs:

```text
solutionVolume  -> ISE : 1460 Solution Volume
aliquotFactor   -> ISE : 1460 Aliquot Factor
ppmFluoride     -> ISE : 1460 Concentration
```

The calculated Mass has been observed to agree with the validation data packet.

### `1460 (Final)` QCSM handling

During OSHA ID-110 validation, QCSM-specific handling was added to `1460 (Final)` so a QCSM retains fluoride mass in UG for F/T recovery instead of applying the ordinary HF PPM calculation used for non-QCSM samples.

Current calculation:

```text
' PTOONE 25-Sep-2026
' Added QCSM handling during OSHA ID-110 validation.
' QCSM Final retains fluoride mass in UG for F/T recovery calculation.
' Non-QCSM samples retain the existing HF PPM calculation.

molarVolume = 24.45'l/mole
molarMass = 18.99840316'gram/mole
sampleType = SELECT SAMPLE.SAMPLE_TYPE
sampleNumber = SELECT SAMPLE.SAMPLE_NUMBER
testNumber = SELECT TEST.TEST_NUMBER
resultName = SELECT RESULT.NAME
IF (sampleType = "QCSM") THEN
    resultValue = fluorideMass
    status = SetOrCreateResult(sampleNumber, testNumber, resultName, resultValue, "UG", , , "F", , "F", , , )
    RETURN resultValue
ELSE
    RETURN (molarVolume*fluorideMass)/(molarMass*airVolume)
ENDIF
```

Inputs shown for this calculation:

```text
fluorideMass -> ISE : 1460 Mass
airVolume    -> ISE : Air Volume
```

For QCSMs, the resulting Mass/Final values have been observed to agree with the data packet. Treat this as working unless a specific recovery failure can be traced back to its stored value, units, or metadata.

There is analogous QCSM handling for the 1280 path. Investigate it only as needed to compare behavior with 1460.

---

## Recovery wrapper components

There are two relevant recovery components:

- `F (1280) QCSM Precision` at the batch level ultimately depends on QCSM 1280 recovery results.
- `HF (1460) QCSM Precision` at the batch level ultimately depends on QCSM 1460 recovery results.

At the individual QCSM/test level, the important recovery components are `1280 F/T` and `1460 F/T`.

The Component Calculations for the F/T recovery wrappers call the shared subroutine `CALC_QC_CONC`. The observed pattern is:

```text
found          -> ISE : XXXX (Final)
theory         -> INGREDIENTS : Concentration
theoryElement  -> INGREDIENTS : Concentration

GOSUB CALC_QC_CONC
RETURN qcRecVal
```

where `XXXX` is `1280` or `1460`.

The exact calculation-variable properties (Output type, scope, source field, etc.) should be verified from LabWare if they become material. Previous validation work configured these as arrays and used the corresponding Final result as `found`.

---

## Current conceptual model to verify

The intended recovery appears to be:

```text
Recovery = measured Final / matching theoretical amount
```

After unit conversion, the shared routine contains the key line:

```text
qcRecVal = Val(foundConvertedtoTheoryUnitsAmt) / Val(theory[i])
```

Therefore, for a nonzero theoretical QCSM, an exact match between measured and theoretical amounts should return `1.0` (dimensionless), corresponding to 100% recovery. Do not assume a stored value of `100` unless another formatter/component multiplies or displays the ratio as a percentage.

The key unresolved question is **how the routine determines which theoretical value belongs to the current analyte and QCSM**, and whether the required metadata/configuration exists correctly for OSHA ID-110.

---

## Important behavior already identified in `CALC_QC_CONC`

You will be given the complete current source of `CALC_QC_CONC`; treat that source as authoritative over general LabWare assumptions.

The following behaviors have already been observed and should be independently confirmed from the source.

### `targetElement`

The routine obtains:

```text
targetElement = SELECT COMPONENT.ALIAS_NAME
```

For the QCSM branch, this appears to be used to identify the analyte/theoretical result that belongs to the F/T component currently being calculated.

### QCSM theoretical-result lookup

For QCSM/RLSM samples, the routine queries `RESULT` for the current sample's `Concentration` result(s), retrieving fields equivalent to:

```text
ENTRY
ATTRIBUTE_1
UNITS
```

When `targetElement` is populated, the query includes a condition equivalent to:

```text
ATTRIBUTE_1 = targetElement
```

When `targetElement` is null, the routine has a different branch involving null `ATTRIBUTE_1` and excluding `N/A`.

The routine then clears/rebuilds `theory` and `theoryElement` from the query results. This means the QCSM branch does **not necessarily use the caller-supplied `theory` and `theoryElement` arrays unchanged**.

The apparent relationship is:

```text
theory[i]        = queried RESULT.ENTRY
theoryElement[i] = queried RESULT.ATTRIBUTE_1
resultArr[i,3]   = queried RESULT.UNITS
```

Confirm this precisely from the supplied source.

### Matching loop

The routine later loops through `theoryElement` and skips entries whose element does not match `targetElement`, approximately:

```text
FOR i = 1 TO UBound(theoryElement, 1) STEP 1
    IF (theoryElement[i] <> targetElement) THEN
        CONTINUE
    ENDIF
    ...
NEXT
```

Confirm the exact behavior and implications.

### Units

The routine independently determines the units associated with `found` and the theoretical result, checks unit categories, converts the found amount to the theoretical units, and only then performs the division.

This means a numerically correct Final result may still fail recovery if its stored units/metadata are wrong or incompatible with the theoretical result's units.

### Zero-theory behavior

The four current QCSM036 samples include a zero-spike member. The routine has special handling when theoretical value is zero. Do not use the zero-spike sample as the first diagnostic case; begin with a nonzero QCSM so the normal Final/Theoretical path can be established first.

---

## Current QCSM diagnostic samples

Current QCSM samples in the batch:

```text
65977  QCSM036-0003-001
65978  QCSM036-0003-002
65979  QCSM036-0003-003
65980  QCSM036-0003-004
```

Previous preparation/result evidence showed theoretical/spike-related values approximately:

```text
90.26248
180.52495
361.04991
0.0
```

Do not assume which exact database result/units/attribute corresponds to each number without inspecting the relevant LabWare result. Use sample `65977` as the preferred first nonzero diagnostic case unless another sample is more convenient.

---

## The batch-level precision message

Before QCSM analytical results were entered, clicking the batch-level QCSM precision result produced a message:

```text
No QCSM results have been entered. Recovery Precision cannot be calculated.
```

Do **not** assume this message comes from `CALC_QC_CONC`; it was not observed in the supplied source of that routine. It likely belongs to a separate batch-precision calculation or subroutine.

Also, do not use this earlier message as evidence that QCSM results are still missing now. It was observed earlier in the workflow, before subsequent analytical result entry.

The batch precision calculation should be investigated **only after** individual F/T recovery is demonstrated or disproved. Keep the layers separate:

1. manual concentration entry;
2. Mass calculation;
3. `(Final)` calculation;
4. individual `1280 F/T` / `1460 F/T` recovery through `CALC_QC_CONC`;
5. batch `F (1280) QCSM Precision` / `HF (1460) QCSM Precision`.

---

## Primary investigation questions

Answer these with evidence from the LabWare source/configuration/results, not guesses.

### A. Explain the data flow

For both `1280 F/T` and `1460 F/T`, determine:

1. What exact value(s) enter `found`?
2. Why is `found` configured/handled as an array?
3. Does the routine use only `found[1]`, or can multiple values affect the result?
4. What initially enters `theory` and `theoryElement` from the component calculation configuration?
5. For QCSMs, exactly how and when does `CALC_QC_CONC` replace/rebuild those arrays?
6. What does `theoryElement` semantically represent?
7. What exact value is used as `targetElement`?
8. How does `targetElement` relate to `RESULT.ATTRIBUTE_1`?
9. What exact database/result value becomes the denominator in `qcRecVal = found/theory`?
10. How are the found and theoretical units determined and converted?
11. What happens when no matching theoretical result is found?
12. What happens when the theoretical amount is zero?

### B. Verify OSHA ID-110 configuration

For at least `1460 F/T`, and preferably both F/T components, inspect and report:

- `COMPONENT.ALIAS_NAME`.
- All relevant calculation-variable properties for `found`, `theory`, and `theoryElement`.
- The actual current QCSM sample's relevant `INGREDIENTS : Concentration` result(s), including at minimum:
  - `ENTRY`;
  - `ATTRIBUTE_1`;
  - `UNITS`;
  - sample number;
  - test/result name if useful.
- The actual `1460 (Final)` (and 1280 equivalent if investigated):
  - numeric value;
  - stored units;
  - any analyte attribute/metadata relevant to lookup.
- The actual `1460 F/T` result or exact error/status after calculation.

Determine whether the alias and attribute match exactly as the routine expects.

### C. Independently reproduce one recovery

For one nonzero QCSM (preferably `65977`):

1. Record the actual measured Final value and units.
2. Record the actual theoretical amount and units retrieved/expected by `CALC_QC_CONC`.
3. Apply the same unit conversion LabWare should perform.
4. Calculate the expected ratio manually.
5. Compare it with the LabWare `F/T` result.

State explicitly whether the individual recovery calculation is correct.

### D. Identify defects only if demonstrated

Potential hypotheses include, but are not limited to:

- F/T `ALIAS_NAME` does not match the QCSM theoretical result's `ATTRIBUTE_1`.
- `ATTRIBUTE_1` is missing or populated differently than expected.
- `theory` query returns zero rows or the wrong row.
- Final units are missing/wrong/incompatible with theoretical units.
- Theoretical result units are missing/wrong.
- The wrapper calculation-variable properties do not match what `CALC_QC_CONC` expects.
- The routine's no-result/fallback behavior is defective for QCSM.
- The individual recovery works correctly and the remaining problem is solely in the separate batch precision calculation.

Do not present any hypothesis as a finding until it is supported by observed configuration/source/result data.

### E. Decide whether another analysis is needed as a comparator

A known-good analysis that also calls `CALC_QC_CONC` may be useful, but do not begin with a broad comparison merely because one exists.

Use a comparator only if it helps resolve a specific uncertainty such as:

- expected `ALIAS_NAME` conventions;
- expected `ATTRIBUTE_1` conventions;
- calculation-variable properties;
- units/metadata;
- behavior when multiple theoretical results exist.

If you use another analysis, document exactly why it is comparable and what difference is material.

### F. Batch precision, only after individual recovery

If individual `1280 F/T` / `1460 F/T` is demonstrated to be correct, inspect the calculation behind:

```text
F (1280) QCSM Precision
HF (1460) QCSM Precision
```

Determine:

- what results it searches for;
- what sample types/QCSM names it includes;
- how it calculates recovery precision;
- why the earlier "No QCSM results have been entered" message occurred;
- whether it now calculates after individual QCSM recovery results exist;
- whether the current batch composition (QCSM036 only) affects the calculation or intended validation coverage.

Keep any batch-precision defect separate from `CALC_QC_CONC` unless evidence links them.

---

## Preferred investigation method

Use a single nonzero QCSM as a controlled trace. Suggested order:

1. Inspect `1460 F/T` component properties and record `ALIAS_NAME`.
2. Inspect its calculation-variable definitions.
3. Inspect QCSM `65977`'s relevant `INGREDIENTS : Concentration` result and record `ENTRY`, `ATTRIBUTE_1`, and `UNITS`.
4. Inspect `65977`'s `1460 (Final)` value and units.
5. Inspect/calculate `65977`'s `1460 F/T`.
6. Compare expected versus actual.
7. If correct, repeat enough of the 1280 path to establish whether it follows the same mechanism.
8. Only then investigate batch QCSM precision if it remains problematic.

At each step, ask for a screenshot/source/configuration view if the evidence is not available. **Do not fill gaps from general LabWare knowledge.**

---

## Things NOT to spend time on unless new evidence requires it

Do not re-investigate:

- how to create the batch;
- how to add/scan samples;
- how to enter individual analytical results;
- which ISE instrument/diluter/balance was used;
- batch reagent/standard barcode selection;
- whether `1460 Concentration` is manually entered;
- the basic `1460 Mass = volume * factor * concentration` formula;
- the reason QCSM `1460 (Final)` preserves mass rather than performing the ordinary non-QCSM HF air-volume conversion;
- upstream result-entry menus.

Those issues have already been worked through sufficiently for the present investigation.

---

## Cautions from the main validation thread

There were earlier moments where assumptions were mistakenly treated as established facts. Please use explicit confidence/evidence labels when useful:

- **Observed:** directly shown by source, LabWare screen, or data packet.
- **Derived:** follows mechanically from observed source/data.
- **Hypothesis:** plausible explanation requiring verification.

Do not silently convert a hypothesis into a finding.

The objective is the **smallest evidence-backed explanation** of whether QCSM recovery works and, if not, why.

---

## Requested deliverable from the investigation chat

Please create a Markdown file named:

```text
OSHA_ID110_CALC_QC_CONC_investigation_findings.md
```

The document should be sufficiently self-contained that it can be brought back into the main OSHA ID-110 validation chat without replaying the entire investigation.

Use the following structure.

# OSHA ID-110 `CALC_QC_CONC` Investigation Findings

## 1. Executive conclusion

In a few paragraphs state:

- whether individual QCSM F/T recovery is working correctly;
- whether `CALC_QC_CONC` itself is behaving correctly;
- whether any OSHA ID-110 configuration defect was found;
- whether a change is required;
- whether the remaining issue, if any, is in batch precision rather than individual recovery.

Clearly distinguish confirmed findings from unresolved items.

## 2. Evidence examined

List the relevant LabWare source/configuration/results/screenshots examined, including sample numbers and component names.

## 3. Confirmed data flow

Document the actual verified path, preferably as a compact diagram such as:

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
found[1] ---------------------+
                              |
QCSM INGREDIENTS Concentration| -> theory[i]
RESULT.ATTRIBUTE_1 -----------+ -> theoryElement[i]
COMPONENT.ALIAS_NAME ------------> targetElement
                              |
                              v
                     CALC_QC_CONC
                              |
                              v
                       1460 F/T recovery
```

Adjust this diagram to match what is actually observed.

Explain why `found`, `theory`, and `theoryElement` are arrays and how indexes correspond.

## 4. `targetElement` / `theoryElement` findings

Report the actual values observed for:

```text
1460 F/T COMPONENT.ALIAS_NAME = ...
1280 F/T COMPONENT.ALIAS_NAME = ... (if inspected)
QCSM theoretical RESULT.ATTRIBUTE_1 = ...
```

State whether they match and whether the query therefore selects the intended theoretical result.

## 5. Unit-handling findings

Report actual observed units for Final and theoretical values, how `ConvertUnits` applies, and whether the unit categories are compatible.

## 6. Worked QCSM example

For at least one nonzero QCSM, provide:

```text
Sample:
Analyte/path:
Final value:
Final units:
Theoretical value:
Theoretical units:
Converted Final value (if conversion occurs):
Expected F/T ratio:
Actual LabWare F/T result:
Pass/fail:
```

Show the arithmetic.

## 7. Defects or configuration issues

For each confirmed issue, state:

- exact defect;
- evidence;
- consequence;
- smallest proposed correction;
- risk/impact on other analyses using the shared routine;
- whether the change belongs in shared `CALC_QC_CONC` or only OSHA ID-110 configuration.

If no defect is found, explicitly say so.

## 8. Batch QCSM precision findings

If investigated, document separately:

- calculation/subroutine involved;
- inputs/results selected;
- expected calculation;
- observed behavior;
- whether QCSM036-only batch composition matters;
- whether the earlier "No QCSM results have been entered" message is now resolved/explained.

If not investigated because individual recovery remains unresolved, say so.

## 9. Recommended next validation steps

Provide a short ordered list of the minimum actions the main validation thread should take next. Avoid repeating already completed validation work.

## 10. Jira-ready summary

Provide a concise technical summary suitable for updating OSHA_LABS-3609. Include:

- what was investigated;
- what was found;
- configuration/code changes, if any;
- regression evidence;
- remaining open items.

## 11. Evidence still needed / unresolved questions

List only genuinely unresolved items.

---

## What to return to the main chat

When complete, return:

1. the Markdown findings file;
2. a 5-10 sentence summary of the most important conclusion;
3. any exact configuration/code change recommended, clearly marked **do not implement until reviewed** if it has not yet been tested;
4. any specific screenshots or LabWare values that the main validation thread should preserve as validation evidence.

