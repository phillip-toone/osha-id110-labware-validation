# OSHA ID-110 — F/T Automatic Calculation Debugger Findings

**Date:** October 16, 2026
**Environment:** OSHA_LIMS_DEV
**Batch:** `OSHA_ID-110-261014-2`
**Request:** `485932`
**Issue:** Initial automatic F/T calculation returns a unit-category mismatch
**Jira:** `OSHA_LABS-3609`
**Status:** Immediate failure mechanism confirmed; underlying cause and corrective action unresolved.

## 1. Objective

Investigate why the OSHA ID-110 QCSM Found/Theoretical (F/T) calculations initially return:

`Theoretical UNITS and actual results UNITS are not same category.`

Previous testing established that manually recalculating either F/T component by double-clicking its result field produces a numeric value without changing analytical inputs.

The objective of this session was to use the LabWare debugger to compare successful manual recalculation with failing automatic calculation.

## 2. Debugging Method

Used **LIMS Basic → Always Open the Debugger** in LabWare DEV.

The debugger was used to inspect calculation execution, set breakpoints, and examine runtime variables without modifying the shared calculation code.

Two execution paths were investigated:

* **Successful manual recalculation:** QCSM035 sample `66260`, ISE test `74908`, component `1280 F/T`.
* **Failing automatic calculation:** QCSM036 sample `66261`, ISE test `74909`, component `1280 F/T`, using diagnostic inputs.

The second test was specifically a calculation-engine diagnostic, not a scientifically appropriate QCSM036 recovery validation.

## 3. Successful Manual Recalculation

Double-clicking `1280 F/T` on sample `66260` invoked:

`CALCULATIONRECORD~ISE → CALC_QC_CONC`

The debugger confirmed:

| Variable             | Observed value       |
| -------------------- | -------------------- |
| `found[1]`           | `451.0`              |
| `foundResultArray`   | `(UG, 1280 (Final))` |
| `rawResultName`      | `1280 (Final)`       |
| `foundResultUnit`    | `UG`                 |
| `foundResultUnitCat` | `MASS`               |
| `theory[1]`          | `90.262476717`       |
| `theoryUnit`         | `UG`                 |
| `theoryUnitCat`      | `MASS`               |

The calculation returned approximately `4.997`.

Both measured and theoretical unit categories resolved correctly to `MASS`.

## 4. Automatic Calculation Investigation

Opened the previously unentered ISE test for QCSM036 sample `66261`.

Entered the following diagnostic values:

| Component              | Value      |
| ---------------------- | ---------- |
| `1280 Solution Volume` | `50 mL`    |
| `1280 Dilution Factor` | `1`        |
| `1280 Concentration`   | `9.02 ppm` |

The debugger intercepted the automatic calculation sequence.

### Observed execution order

1. Entering the solution volume invoked the ISE Air Volume calculation and shared `CALC_AIR_VOL` routine.
2. Entering the dilution factor did not visibly invoke another calculation.
3. Entering concentration invoked the `1280 Mass` calculation.
4. `1280 Mass` evaluated `50 × 1 × 9.02 = 451.0`.
5. LabWare invoked `1280 (Final)`.
6. The QCSM branch called `SetOrCreateResult()` to assign `451.0` with units `UG`.
7. `SetOrCreateResult()` returned `true`.
8. LabWare automatically invoked `1280 F/T`, which entered `CALC_QC_CONC`.

This established the observed automatic calculation sequence:

`1280 Concentration → 1280 Mass → 1280 (Final) → 1280 F/T`

### Failure observed inside CALC_QC_CONC

Breakpoints were used at lines 84, 90, and 243.

At line 84, the SQL-derived `foundResultArray` was empty. The active result-context lookup also failed to identify the Final result and its units.

At line 90, both `foundResultUnit` and `foundResultUnitCat` remained empty.

At line 243, the debugger confirmed:

| Variable             | Observed value |
| -------------------- | -------------- |
| `found[1]`           | `451.0`        |
| `foundResultArray`   | Empty          |
| `rawResultName`      | `EMPTY`        |
| `foundResultUnit`    | `EMPTY`        |
| `foundResultUnitCat` | `EMPTY`        |
| `theory[1]`          | `90.262476717` |
| `theoryElement[1]`   | `Fluoride (F)` |
| `theoryUnit`         | `UG`           |
| `theoryUnitCat`      | `MASS`         |

The unit-category comparison was therefore:

`EMPTY` versus `MASS`

This caused execution to enter the error branch in `CALC_QC_CONC`.

## 5. Confirmed Findings

**The immediate cause of the F/T error is failure to retrieve the measured Final result's units during initial automatic calculation.**

The debugger established that:

* The measured mass value was available to the F/T calculation.
* The upstream Final calculation had executed and `SetOrCreateResult()` returned success.
* The SQL lookup for the Final result returned no rows during initial automatic calculation.
* The active result-context lookup also failed to provide the Final result's units.
* The theoretical fluoride units and category were retrieved correctly.
* Manual recalculation succeeded when the Final result was subsequently accessible.

The observed error does not demonstrate an inherent incompatibility between the persisted `UG` units of the measured and theoretical results.

## 6. Remaining Technical Questions

The debugger evidence does not yet establish why the Final result is unavailable during the immediate downstream lookup.

Potential explanations requiring further investigation include:

* Result creation versus result update behavior in `SetOrCreateResult()`.
* Timing of result-context updates.
* Database transaction or SQL visibility during automatic calculation.
* Dependency handling and automatic calculation sequencing.
* Whether the shared routine should handle temporarily unavailable Final result units differently.

These remain hypotheses, not confirmed root causes.

## 7. Reproducibility and Preservation

After completing the debugging session, Result Entry for sample `66261` was closed without saving.

The sample was reopened, and all ISE component values again displayed `Not Entered`. The test remained `Incomplete`.

This demonstrates that the diagnostic scenario can be reset at the Result Entry interface and provides a practical basis for further controlled testing.

A read-only SQL verification would provide additional assurance that no diagnostic results were persisted.

No shared calculation code or component configuration was intentionally modified during the debugging investigation.

## 8. Recommended Next Steps

1. Investigate whether `SetOrCreateResult()` is a built-in function or locally implemented routine and determine its documented result-creation and visibility behavior.
2. Determine whether the first-pass failure occurs only when the Final result is newly created.
3. Evaluate corrective approaches in DEV without changing shared calculations prematurely.
4. Repeat the controlled test after any proposed correction.
5. Separately validate QCSM035 and QCSM036 using scientifically appropriate analytical inputs.
6. Preserve debugger screenshots and update Jira `OSHA_LABS-3609` with the confirmed failure mechanism.

## 9. Acceptance Boundary

**The F/T calculation issue is not resolved.**

The debugger investigation established the immediate failure mechanism and a reproducible diagnostic workflow. It did not establish a validated correction, acceptable QC recovery, or successful end-to-end OSHA ID-110 method validation.
