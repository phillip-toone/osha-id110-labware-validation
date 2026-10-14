# OSHA ID-110 — October 14, 2026 validation checkpoint

**Repository destination:** `osha-id110-labware-validation` (proposed technical checkpoint; not committed).

## Scope and test artifacts

- DEV request/sampling number: `485932`; field sample `66253` (Sample 1) received for this focused test; QCSM035 sample `66260` and QCSM036 sample `66261` prepared and included in the batch.
- Batch: `OSHA_ID-110-261014-2`; ISE test for QCSM035 sample `66260`: `74908`, analysis version 1, variation `OSHA_ID-110_QCSM`.
- QCSM035 ingredient records on sample `66260`: fluoride solution `LAB_SOL00265-0006-001`, quantity `10 UL`, calculated theoretical fluoride `90.262476717 UG` (result `586059`, alias `Fluoride (F)`); filter ingredient `FES0000028-0005-011`, quantity `1 EACH`, concentration `N/A` / `NONE`.

## Observations and diagnostics

- Initially, Result Entry showed `Theoretical UNITS and actual results UNITS are not same category.` for both `1280 F/T` and `1460 F/T`.
- Before saving, the `TEST` record existed for ISE test `74908` but no matching ISE `RESULT` rows were found. Saving Result Entry persisted the ISE result records.
- SQL confirmed `1280 (Final) = 451 UG` and `1460 (Final) = 500 UG`; both mapped to unit category `MASS`, as did theoretical ingredient `90.262476717 UG`. SQL also confirmed `found` mappings for both F/T components and `theory` / `theoryElement` mappings to INGREDIENTS Concentration in ISE version 1.
- The batch `Process Batch Results` action reported prerequisites not met: batch instrument missing, instrument-group mismatch, and required batch results missing (equipment, buffers, standards). This action was not shown to complete successfully.
- An attempted `Batch > Calculate` action displayed `Unable to perform this action while child windows are open`. A later batch screenshot showed ISE test `74908` as **Complete** and numeric recoveries: `1280 F/T = 4.997`; `1460 F/T = 5.5394004041`. The exact action that caused successful recalculation was not independently established; do not attribute success definitively to Batch > Calculate.
- These correspond to approximately 499.7% and 553.94% recovery. The entered diagnostic concentrations (`9.02` and `10`, shown as `PPM` in the result records) and 50 mL solution volume yield measured masses of 451 and 500 µg. The high recoveries are not proof of a scaling defect: diagnostic inputs do not match the theoretical spike mass.

## Configuration and follow-up

- During troubleshooting, concentration and Final component units were adjusted/reviewed; the last observed persisted ISE results for sample `66260` showed Concentration `PPM`, Mass `UG`, and Final `UG` for both 1280 and 1460. Distinguish stored result units from current analysis configuration; final saved component settings need reconfirmation before another run.
- Shared `CALC_QC_CONC` was inspected, not changed. Potential calculation timing/context issue remains a hypothesis, not a confirmed root cause.
- Follow up on whether batch `Slope`, `Beginning Temperature`, `Ending Temperature`, and `Pipettor Used` should be reportable and/or required, and whether one or two pipettor fields are needed. Group clarification email sent October 14; response pending.
- Next session: preserve a clean diagnostic case; verify current component configuration and concentration unit behavior, determine which recalculation action produced the numeric F/T results, and validate recovery with analytically appropriate independent input values. Do not treat the method as accepted on the basis of this test.

## Evidence

- October 14 LabWare DEV screenshots and read-only SQL extracts from `RESULT`, `TEST`, `CALC_VARIABLES`, and `UNITS` for sample `66260` and test `74908`.
- October 14 email reply to ISE demonstration participants requesting decisions on new batch component settings.
- Jira tracking reference: `OSHA_LABS-3609` (as used in the ongoing validation record).
