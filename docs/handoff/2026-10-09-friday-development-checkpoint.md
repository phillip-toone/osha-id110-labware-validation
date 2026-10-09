# OSHA ID-110 — Friday October 9, 2026 development checkpoint

**Jira:** `OSHA_LABS-3609`  
**Repository baseline reported by user:** `e28feba` (`Document ISE demo follow-up and archive 485929/485930 runs`)  
**System:** `OSHA_LIMS_DEV`, LabWare 8  
**Status:** Configuration development substantially complete; **NOT runtime validated**. This checkpoint is based on screenshots, read-only SQL outputs, and subroutine source supplied during the October 9 session. It is not a database export, deployed change log, or proof of fresh-batch behavior.

## Friday summary

- **DEMO-01:** `Pipettor Used` added to `OSHA_ID-110` batch template, immediately after `Balance Used`, Calculated (`K`), alias `PIPETTOR`, uses `BC_INSTRUMENT_BROWSE`; `REPORTABLE=F`, `DISPLAYED=T`. Instrument `002617` demonstrated `INSTRUMENTS.INST_GROUP=PIPETTOR`. A possible **second pipettor field** is unresolved. The shared browse routine filters by `BATCH_COMPONENT.ALIAS_NAME`, requires batch owner/eligible status/verification, rejects out-of-service and out-of-calibration equipment; `pmFlag=FALSE` explicitly disables PM rejection. Do not change shared routine without separate review.
- **DEMO-02:** `Slope` saved as Numeric (`N`), units `MV_DEC` (`mV/dec`, new active `UNITS` record), min `-60`, max `-54`, `ALLOW_OUT=F`, `PLACES=1`. Limits supported by *MWI-ID 110*, v1.4, page 3. `AUTO_CALC=T` on Numeric remains untouched; operational significance not established.
- **DEMO-03/04:** `Beginning Temperature` and `Ending Temperature` saved as Numeric, units `DEG-F`, no configured limits, displayed and reportable. Final observed order numbers 23 and 24.
- **DEMO-05:** `MCE Filter Lot (for wiping cassettes)` removed from `OSHA_ID-110` batch template; absence observed in GUI and `BATCH_COMPONENT` SQL. Historical records not tested.
- **DEMO-06:** Five OSHA ID-110 ISE variations inventoried: `OSHA_ID-110_AIR`, `_SCREEN`, `_WIPE`, `_QC`, `_QCSM`. Initially only AIR had component overrides: `1280 (Final)` displayed/reportable in `MG_M3` and `1460 (Final)` displayed/reportable in `PPM`; QC/QCSM/SCREEN/WIPE override lists were empty. User subsequently reported **saving QCSM mapping** modeled on ICP_MS QCSM, using ISE `Concentration` as the Raw analogue. The final persisted QCSM override list and each override's properties were **not supplied**; verify before acceptance. Do not assume all components should be made visible indiscriminately.
- **DEMO-08:** Four chlorine components (`Chloride (Raw)`, `Chlorine Gas (Final)`, `Available Chlorine (Final)`, `Cl F/T`) absent from latest `COMPONENT WHERE ANALYSIS='ISE'` extract. **ISE is shared** with ID-101 and other variations; the effect on unrelated methods has not been assessed. An earlier ID-101 QCSM override screenshot showed chlorine entries. Do not claim cross-method safety until tested.
- **DEMO-09:** `1280 Aliquot Factor` and `1460 Aliquot Factor` renamed to `1280 Dilution Factor` and `1460 Dilution Factor`; persisted `COMPONENT` query verified. `1280 Mass` and `1460 Mass` calculation input variable `aliquotFactor` points to respective renamed component. Both calculations retain `RETURN solutionVolume*aliquotFactor*ppmFluoride`, with mass in `UG`. No runtime proof yet.
- **DEMO-10:** Both `1280 Concentration` and `1460 Concentration` changed from `PPM` (`FRACTION` category) to `MG_L` (`MASS_VOLUME` category), confirmed in saved `COMPONENT` extract. `1280 Mass`/`1460 Mass` still `UG`; Final defaults remain `MG_M3`/`PPM`. Category transition is a specific regression risk; mg/L and ppm must not be treated as universally interchangeable.
- **DEMO-11:** Existing 2026-09-25 `1280 (Final)` and `1460 (Final)` sample-type branches inspected and **left unchanged**. For `SAMPLE_TYPE='QCSM'`, each returns fluoride mass and calls `SetOrCreateResult(..., 'UG', ...)`; all other sample types follow original air conversion (`fluorideMass/airVolume` for 1280; `(24.45*fluorideMass)/(18.99840316*airVolume)` for 1460). The code does **not** separately branch for QC/SCREEN/WIPE. AIR overrides specify `MG_M3` and `PPM`. Tyler's HF vs F- reporting decision (DEMO-13) remains open; 1460 formula uses fluorine molar mass despite AIR description `Fluoride as HF`.

## F/T recovery investigation (DEMO-07) — preserve unresolved evidence

- `1280 F/T` and `1460 F/T` both call shared `CALC_QC_CONC`, returning `qcRecVal`; `found` points to corresponding `(Final)`; `theory` and `theoryElement` map to `INGREDIENTS : Concentration`. Both use alias `Fluoride (F)`.
- `1460 F/T -> found` GUI properties observed: `ENTRY`, `Array`, scope `All Results For Current Test`, trigger `Calculate when any rep entered`, specific-analysis True. This matches the October 7 correction; fresh mapped QCSM036 runtime proof remains absent.
- `CALC_QC_CONC` determines found-result units via `CALC_VARIABLES`/`RESULT`, compares `UNITS.CATEGORY` with theoretical units, converts via `ConvertUnits` when categories match, otherwise returns exactly `Theoretical UNITS and actual results UNITS are not same category.` This identifies the error path, **not the cause**. Do not modify shared routine speculatively.
- Historical request `485928`: mapped 1280 recovery `96 / 90.262476717 = ~1.063565` (formatted 1.064) succeeded. `485929`: QCSM035 sample `66077` again showed the category error; cross-analyte 1460 on the same QCSM035 does **not** prove QCSM036. Preserve 1460 EF- 22.0 vs 22.8 mV discrepancy. Request `485930` remains historical demo evidence, not acceptance.
- For sample `66077`, `INGREDIENTS` test `74455` contains fluoride theoretical `90.262476717 UG`, attribute `Fluoride (F)`; `74456` contains media ingredient. `TEST` includes ISE test `74471`, version 1, variation `OSHA_ID-110_QCSM`, batch `OSHA_ID-110-261007-2`, status `I`, but `SELECT ... FROM RESULT WHERE TEST_NUMBER=74471` returned **no rows**. No obvious archive-named table appeared in `USER_TABLES` result-table search. This missing historical ISE result state is unexplained; do not assert deletion, archival, or a causal link to today's edits.

## Remaining decisions and validation gates

1. **Confirm one versus two pipettor fields** (Tyler/analyst).
2. **Verify saved QCSM component-variation mapping** (including intended `Prep Volume`, 1280/1460 Concentration, Final, F/T, plus any necessary Mass/other components) and its display/reportable/unit overrides. ICP_MS QCSM is a reference pattern, not proof of ISE requirements.
3. **Confirm `1280 F/T -> found` input properties** if needed as a direct comparison with corrected 1460; screenshot showed mappings but not detailed variable properties.
4. **Resolve DEMO-13** reporting HF versus F- and whether gravimetric conversion is required before modifying 1460 science; DEMO-12 and DEMO-14 are Tyler-owned and have no verified completion.
5. **Fresh consolidated regression:** new request, correctly mapped QCSM035 (1280) and QCSM036 (1460), fresh ISE tests and batch; verify batch fields, pipettor browse, slope acceptance/rejection, temperature fields, removed MCE field, variation visibility, mg/L concentration to UG mass, both Final unit branches, F/T including category/evaluation order, precision, and field-sample reporting. Preserve request/sample/test/batch IDs and before/after results. Avoid altering shared routines merely to make a test pass.
6. **Cross-method impact:** because ISE component removal is global to the ISE analysis, verify OSHA ID-101 and other variations are not broken by chlorine cleanup.

## Repository / Jira state

The conversation did **not** update the repository or Jira automatically. These documents are prepared as a Friday checkpoint for manual copy, review, commit, push, and Jira summary. **Do not claim committed/pushed beyond `e28feba`.** Evidence consists of the screenshots/SQL/source shared in the October 9 session; original screenshots are not automatically included in the repository. Keep status labels separate: **saved**, **configuration reviewed**, **runtime validated**.

## Recommended first step next session

Inspect the **saved** `OSHA_ID-110_QCSM` Component Variations list and override properties. Do not create a fresh validation batch until that baseline is recorded and the remaining required configuration decisions are either resolved or explicitly scoped as pending. Then use consolidated regression rather than isolated full runs after each small configuration change.
