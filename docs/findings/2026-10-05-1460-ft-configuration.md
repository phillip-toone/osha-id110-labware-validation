# Findings --- 1460 F/T Calculation Configuration --- 2026-10-05

## Purpose

Prepare the OSHA ID-110 ISE analysis for the scheduled end-to-end demonstration on 2026-10-07 and close the known `1460 F/T` configuration gap identified during the 2026-09-25 investigation.

## Starting condition

The last explicitly confirmed state of `ISE / 1460 F/T` had the recovery equation present but no calculation-variable inputs. The equation was:

```text
'THEY 01-Mar-2023
GOSUB CALC_QC_CONC
RETURN qcRecVal
```

The working `ISE / 1280 F/T` component was used as the reference configuration.

## Reference configuration confirmed for 1280 F/T

`1280 F/T` uses three Component inputs:

| Variable | Component source | Trigger | Scope | Value | Output |
| --- | --- | --- | --- | --- | --- |
| `found` | `ISE : 1280 (Final)` | Calculate when any rep entered | All Results For Current Test | `ENTRY` | Array |
| `theory` | `INGREDIENTS : Concentration` | Always Calculate | All Results For All Tests | `ENTRY` | Array |
| `theoryElement` | `INGREDIENTS : Concentration` | Always Calculate | All Results For All Tests | `ATTRIBUTE_1` | Array |

## DEV change made on 2026-10-05

The missing `1460 F/T` inputs were created to parallel the established `1280 F/T` configuration:

| Variable | Component source | Trigger | Scope | Value | Output |
| --- | --- | --- | --- | --- | --- |
| `found` | `ISE : 1460 (Final)` | Calculate when any rep entered | All Results For Current Test | `ENTRY` | Array |
| `theory` | `INGREDIENTS : Concentration` | Always Calculate | All Results For All Tests | `ENTRY` | Array |
| `theoryElement` | `INGREDIENTS : Concentration` | Always Calculate | All Results For All Tests | `ATTRIBUTE_1` | Array |

The calculation source itself was not changed:

```text
'THEY 01-Mar-2023
GOSUB CALC_QC_CONC
RETURN qcRecVal
```

## LabWare UI detail discovered

When adding a calculation variable of type **Component**, LabWare pre-populates the **Which Analysis?** browser with `ISE`. With that filter in place, `INGREDIENTS` is not visible in the resulting analysis list.

To create the `theory` and `theoryElement` references correctly:

1. Add the variable as type **Component**.
2. At **Which Analysis?**, clear the pre-populated `ISE` text so the Name field is blank.
3. Open the analysis browser/search.
4. Select `INGREDIENTS` from the full analysis list.
5. Select component `Concentration`.
6. Configure the variable properties as shown in the table above.

Using **Batch Sample** also exposes `INGREDIENTS`, but it creates a variable with Type `Batch Sample` and different properties. That is not equivalent to the established `1280 F/T` configuration and should not be used here.

## Current validation status

The `1460 F/T` configuration is now structurally parallel to the established `1280 F/T` recovery configuration. This change has **not yet been regression-tested at runtime** and must not yet be considered validated.

This change joins the three DEV changes from 2026-09-25 that are also awaiting regression testing:

1. ISE Air Volume changed to shared `CALC_AIR_VOL`.
2. QCSM handling restored in `1280 (Final)`.
3. Analogous QCSM handling added to `1460 (Final)`.

## Next action

Create a fresh regression run from reference case `HD-2026-12-02` using a new request number and include both QCSM sets:

- `QCSM035` for particulate fluoride / 1280
- `QCSM036` for HF / 1460

Use the historical analytical inputs and verify the checklist in `docs/findings/2026-09-25-configuration-investigation.md`, with particular attention to runtime calculation of `1460 F/T` and downstream HF QCSM precision.
