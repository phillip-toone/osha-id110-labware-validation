# OSHA ID-110 — ISE Demo Follow-up Actions (2026-10-08)

**Source:** Email from Tyler J. Erickson, "Notes from ISE demo," sent October 8, 2026 at 8:49 AM, to Phillip Toone; cc Hanh Dinh, Jan Ottowicz, John Yost. **Primary tracking:** `OSHA_LABS-3609`. **Environment:** OSHA_LIMS_DEV / LabWare 8. **Status:** October 9 configuration checkpoint: many changes saved and SQL/GUI-inspected; no consolidated post-demo runtime acceptance yet. **Authoritative starting repository commit:** `e28feba` (per project handoff). 

## Scope and evidence conventions

The fourteen items below transcribe the email's five batch-template requests, six ISE-analysis requests, and three Tyler-owned follow-ups. Original phrases are retained in quotation marks. **Requested** does not mean **implemented** or **defective**. Owner is taken from the email's headings: Phillip for "Phillip's development action," Tyler for "my action." Validation criteria below are *proposed* tests, not assertions that LabWare already meets them. Confirm open scientific decisions and configuration details before changing them. Preserve screenshots, before/after configuration, sample/test version, and runtime outcomes in the repository and Jira.

**Evidence key:** [EMAIL] October 8 email quoted above (save original email in the official record as appropriate); [CURRENT] `docs/handoff/CURRENT.md`, especially October 7 update; [FINDING] `docs/findings/2026-10-06-qc-recovery-and-precision-investigation.md`; [EXTRACT] `2026-10-07-analytical-values-extraction-results.md` (place under its actual repository location if different); [RUN] observed 485929 run in continuation chat, including QCSM035-0007 / QCSM036-0005 and F/T error. References are pointers; no additional screenshots or SQL are claimed to have been captured by this document.

## A. OSHA ID-110 batch template — Phillip

| ID | Status | Original request | Evidence | Proposed acceptance criterion |
| --- | --- | --- | --- | --- |
| DEMO-01 | [~] Saved: one pipettor; second pending | "Pipettor Used (potentially 2 of these)" — ADD | [EMAIL]; [CURRENT] existing equipment traceability | Determine whether one or two separate fields are required with lab stakeholders; configured field(s) accept valid pipettor equipment ID(s), persist, and appear in fresh batch results. |
| DEMO-02 | [~] Saved / config verified | "Slope value (check range and add limits to the value)" — ADD | [EMAIL] | Confirm slope definition, units, allowable range and enforcement with method owner; verify in-range entry and out-of-range warning/rejection on fresh batch. Do not invent limits. |
| DEMO-03 | [~] Saved / config verified | "Beginning temperature (F)" — ADD | [EMAIL] | Batch component accepts/persists a beginning temperature in degrees Fahrenheit and displays the intended units. |
| DEMO-04 | [~] Saved / config verified | "Ending temperature (F)" — ADD | [EMAIL] | Batch component accepts/persists an ending temperature in degrees Fahrenheit and displays the intended units. |
| DEMO-05 | [~] Removed / config verified | "MCE Filter Lot (for wiping cassettes)" — REMOVE | [EMAIL]; [CURRENT] existing optional field | Field is absent from new OSHA ID-110 batch template/results while unrelated analyses remain unaffected; preserve historical batch evidence. |

## October 14 fresh-batch configuration observation — DEMO-01 through DEMO-04

**Evidence:** Operator screenshot of the batch-level **Results** grid in `OSHA_LIMS_DEV`, batch `OSHA_ID-110-261014-1`, October 14, 2026. **Classification:** `OBSERVED_UI`; laboratory requirements and enforcement behavior remain `UNRESOLVED`. This observation supplements, but does not close, DEMO-01 through DEMO-04.

| Action | Batch component | Reportable (UI) | Opt. (UI) | Observed result status | Open confirmation |
| --- | --- | --- | --- | --- | --- |
| DEMO-01 | Pipettor Used | No | No | Not Entered | Confirm whether mandatory, whether one or two pipettor fields are needed, and intended reportability. |
| DEMO-02 | Slope | Yes | No | Entered: `-59.0 mV/dec`; UI limits `-60` to `-54` | Confirm whether reportable and mandatory, and whether limits are enforced. |
| DEMO-03 | Beginning Temperature | Yes | No | Not Entered; `°F` displayed | Confirm whether reportable and mandatory. |
| DEMO-04 | Ending Temperature | Yes | No | Not Entered; `°F` displayed | Confirm whether reportable and mandatory. |

**Evidence boundary:** `Opt. = No` is the displayed configuration flag, **not** proof that Save, processing, or authorization enforces entry. `Reportable = Yes` is likewise not proof of appearance on an issued report. The operator reported **File > Save** while these three fields were blank; the screenshot shows their `Not Entered` status, but does not by itself prove successful authorization or persistence after reopening. The entered slope is a historical-reference replay value, not a newly measured instrument slope.

**Follow-up / acceptance evidence needed:** Obtain laboratory/method-owner decisions for each field's intended `Reportable` and `Opt.` flags; inspect saved component configuration; test entry, save/reopen, and appropriate completion gates in DEV without authorizing incomplete analytical data; inspect a generated report or approved preview for reportability. Preserve observed behavior and screenshots. **Do not change flags or shared calculations before requirements are confirmed.**

## B. ISE analysis and variations — Phillip

| ID | Status | Original request | Evidence | Proposed acceptance criterion |
| --- | --- | --- | --- | --- |
| DEMO-06 | [~] QCSM mapping reportedly saved; inspect persisted overrides | "Check variations - make all necessary components visible and all components" | [EMAIL]; [CURRENT] ISE component workflow | Inventory ISE/OSHA_ID-110 variations and component visibility, agree necessary set with analyst, and verify expected components are visible in fresh test(s); record before/after variation configuration. Wording of "and all components" needs confirmation. |
| DEMO-07 | [~] Open: unit-category error unresolved | "1280 F/T - not calculating" | [EMAIL]; [CURRENT]; [FINDING]; [RUN] | Fresh correctly mapped 10-µL QCSM035 produces calculated 1280 Mass 96 µg, Final 96 µg and F/T about 1.063565 (`96 / 90.262476717`), with no unit-category error or manual forcing. Test repeatability and evaluation order; separately test 1460 recovery. **Observed:** 485928 eventually reached 1.064; 485929 again displayed unit-category error. Root cause of recurrence not established. |
| DEMO-08 | [~] Main ISE removal verified; shared impact untested | "Remove Chlorine related components from the OSHA ID-110 variations" | [EMAIL]; [RUN] visible chlorine rows | Fresh OSHA_ID-110 ISE result-entry variations no longer display chloride/chlorine-related components (`Chloride (Raw)`, `Chlorine Gas (Final)`, `Available Chlorine (Final)`, `Cl F/T` as applicable); ensure no unintended effect on other methods. |
| DEMO-09 | [~] Saved / references inspected | "Change 'Aliquot Factor' to 'Dilution Factor'" | [EMAIL]; [EXTRACT] historical aliquot factor 1 | Display labels for 1280/1460 use "Dilution Factor" where requested; confirm semantic equivalence or calculation changes with method owner; calculation variables, historical inputs, and computed Mass/Final remain correct. Do not assume a simple rename is scientifically sufficient. |
| DEMO-10 | [~] Saved / config verified | "Change 1280 Concentration and 1460 Concentration to mg/L" | [EMAIL]; [EXTRACT] instrument readings recorded in mg/L; [RUN] UI currently ppm | Both input components show mg/L; confirm configured unit category/conversion and calculations with measured values (e.g. 1.92 and 1.76 mg/L in QC reference), with Mass in µg. Do not equate ppm and mg/L across all contexts without verifying conversion semantics. |
| DEMO-11 | [~] Existing branches reviewed; runtime pending | "Update final result calc to be based on sample type (QCSM calc to ug, air samples calc to mg/m3 and ppm)" | [EMAIL]; [CURRENT]; [FINDING]; [EXTRACT] | Verify QCSM 1280/1460 Final retains fluoride mass in µg; non-QCSM 1280 Final reports mg/m³ and 1460 Final reports ppm, with correct units/metadata and expected reference values. **Existing sample-type branches were previously implemented; inspect/validate before editing.** HF versus F⁻ reporting decision (DEMO-13) may affect 1460. |

## C. Tyler's follow-ups — Tyler

| ID | Status | Original request | Evidence | Proposed acceptance criterion |
| --- | --- | --- | --- | --- |
| DEMO-12 | [ ] Open | "Looking into adding instrument to standard/reagent prep" | [EMAIL]; [CURRENT] inventory preparation and traceability | Tyler determines whether/how instrument selection belongs in standard/reagent preparation, documents recommendation and any required configuration/testing handoff. |
| DEMO-13 | [ ] Open | "Reporting HF or F- for 1460. Should we use grav factor for HF in calculation?" | [EMAIL]; [CURRENT] existing 1460 Final calculation uses F mass / HF-related constants; [EXTRACT] historical 1460 reporting reference | Tyler/method stakeholders document reportable chemical species, units, and whether a gravimetric conversion factor is required; approve the scientifically correct formula before changes; compare results against reference worksheets. **Decision pending; do not silently change factors.** |
| DEMO-14 | [ ] Open | "Creating label that shows concentration of the solution to put on bottle/flask" | [EMAIL]; [CURRENT] stock/standard inventory | Tyler provides a usable label showing solution identity and concentration with appropriate units, validates against an actual prepared solution, and records deployment or next implementation handoff. |

## October 9 implementation and evidence checkpoint

See `docs/handoff/2026-10-09-friday-development-checkpoint.md` for detailed observed values, evidence boundaries, risks, and restart steps. **[~] means configuration work performed/reviewed, not acceptance complete.** No DEMO-01 through DEMO-11 item has passed the consolidated fresh-batch post-demo regression. Tyler-owned DEMO-12 through DEMO-14 remain open with no completion evidence.

## October 14, 2026 — Stakeholder clarification requested

**Status:** Awaiting stakeholder response.

On October 14, 2026, at 3:39 PM, Phillip sent a follow-up email
(RE: Notes from ISE demo) to Tyler Erickson, copying Hanh Dinh,
Jan Ottowicz, and John Yost.

The email requests confirmation of:

- Whether Pipettor Used, Slope, Beginning Temperature, and Ending
  Temperature should be reportable or non-reportable.
- Whether each of these four components should be required or optional.
- Whether one or two Pipettor Used fields are needed.

**Related actions:** DEMO-01, DEMO-02, DEMO-03, DEMO-04.

**Current observed configuration:** In DEV batch
`OSHA_ID-110-261014-1`, Slope, Beginning Temperature, and Ending
Temperature display Reportable = Yes; Pipettor Used displays
Reportable = No. All four display Opt. = No.

These are observed settings, not confirmed laboratory requirements.
The runtime enforcement of Opt. = No has not been established.

**Next:** Record stakeholder decisions and update the applicable
configuration and validation criteria. Do not change these settings
solely on the basis of the current observations.

## October 14, 2026 — F/T runtime investigation

**Status:** Partial runtime evidence; F/T acceptance remains open.

**Environment:** `OSHA_LIMS_DEV`
**Request:** `485932`
**Batch:** `OSHA_ID-110-261014-2`
**Diagnostic sample:** `66260` (QCSM035)
**ISE test:** `74908`, version 1, variation `OSHA_ID-110_QCSM`

Read-only SQL confirmed that the theoretical fluoride ingredient
result was `90.262476717 UG` (`MASS`). Both measured Final results
were also stored in `UG` (`MASS`): `1280 (Final) = 451` and
`1460 (Final) = 500`.

Both F/T calculations initially returned:

`Theoretical UNITS and actual results UNITS are not same category.`

The saved calculation-variable mappings were verified. After
subsequent batch activity, both F/T results became numeric:
`1280 F/T = 4.997` and `1460 F/T = 5.5394004041`.

**Evidence boundary:** The successful recalculation trigger and
original error mechanism remain unconfirmed. These values came from
diagnostic analytical inputs and do not establish acceptable QC
recovery. The 1460 result was obtained on QCSM035 sample `66260`,
not the scientifically mapped QCSM036 sample `66261`.

**Remaining validation:** Verify calculation execution order,
saved concentration/Final unit configuration, correct historical
analytical inputs, 1460 recovery on QCSM036, QCSM precision,
and field-sample results.

**Configuration control:** Do not modify shared `CALC_QC_CONC`
without evidence. Preserve the initial errors and subsequent
numeric results as separate observations.

**Detailed evidence:**
`docs/handoff/2026-10-14-id110-ft-validation-checkpoint.md`

## Validation dependencies and execution notes

- Prioritize documenting baseline configuration and obtaining scientific decisions (especially slope limits, dilution semantics, and HF versus F⁻) before changing calculations.
- **Known runtime issue:** 485929 fresh QCSM035 sample 66077 returned `Theoretical UNITS and actual results UNITS are not same category.` for 1280 F/T; the same diagnostic sample also returned the message for 1460 F/T after cross-analyte entry. This is *not* a mapped 1460 QCSM036 proof. In that diagnostic, 1460 EF- was entered/displayed as 22.0 mV instead of historical 22.8 mV; preserve that discrepancy. [RUN]
- The October 7 fix changed `1460 F/T -> found` to `ENTRY / Array` at the configuration level; fresh runtime proof on a scientifically mapped QCSM036 sample was not established in the evidence supplied here. [CURRENT]; [FINDING]
- Preserve the historical 485926/485927/485928/485929 runs. Use newly instantiated tests for regression when analysis configuration/version changes. [CURRENT]
- Attach test IDs, before/after screenshots or read-only queries, expected versus observed outputs, and acceptance decision to each completed item; summarize outcomes under Jira `OSHA_LABS-3609`.

## Change log

- 2026-10-09: Captured 14 action items from Tyler's October 8 email; initial intake only.
- 2026-10-09 (Friday checkpoint): Updated Phillip-owned statuses from demonstrated GUI and SQL configuration evidence; runtime validation and outstanding scientific decisions remain open. See Friday checkpoint handoff.
- 2026-10-14: Added fresh-batch `OSHA_ID-110-261014-1` UI evidence for DEMO-01 through DEMO-04: reportability and optional flags, entry statuses, and unresolved requirements/enforcement questions. No acceptance status changed.
