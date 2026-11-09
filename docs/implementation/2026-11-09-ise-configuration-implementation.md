# ISE v1 Component and Variation Configuration — Controlled Implementation Log

**Initiated:** 2026-11-09
**Environment:** OSHA_LIMS_DEV (Oracle)
**Status:** PARTIAL DEV IMPLEMENTATION — TWO QCSM FINAL OPTIONAL FLAGS CHANGED AND GUI-VERIFIED; SEVEN-COMPONENT QCSM GUI INVENTORY COMPLETED; REGRESSION NOT STARTED
**Owner:** Project Director and LabWare operator
**Source directive:** Tyler Erickson, email 2026-11-09

## Objective

Implement Tyler's component and variation configuration instructions exactly, while recording before/after evidence and testing whether fresh QCSM tests automatically calculate F/T without manual intervention. Configuration compliance and resolution of the automatic-calculation failure are separate acceptance criteria.

## Governing configuration rules (Tyler)

1. At **parent component level**, set every component `Optional=T`, `Displayed=F`, `Reportable=F`.
2. At **variation component level**, include all components applicable to that variation; set `Displayed=T` for applicable components, `Optional=F` only when mandatory for test completion, and `Reportable=T` only for intended reportable results (typically Final and F/T).
3. Inapplicable components must not be required or shown merely because they exist in the shared analysis (example: Air Volume for bulk).
4. Where applicability, required status, or reportability cannot be established from approved method/workflow evidence, mark **UNRESOLVED** and stop that change rather than guess.

## Baseline from Specialist investigation (requires verification before edit)

- ISE version 1: 21 parent components, 11 variations, 30 explicit variation-component records across eight variations.
- Three variations have no explicit component rows; 13 variation-component references use five names missing from the parent component inventory.
- Parent: all 21 `Optional=T`; 19 `Displayed=T`; two `Reportable=T`.
- `OSHA_ID-110_QCSM`: seven explicit variation components. `1280 (Final)` and `1460 (Final)` are `Optional=T`, `Displayed=T`, `Reportable=T`, `Units=UG`. Both F/T components are optional, displayed, and nonreportable.
- Absence of an explicit variation row does not prove absence from Result Entry; previously the QCSM screen displayed many inherited components.
- These are *observations*, not authorization for blanket edits.

## Change control gates

| Gate | Requirement | Status |
|---|---|---|
| 0 | Preserve Specialist Director Brief and Investigation Report | Pending repository confirmation |
| 1 | Capture verified current GUI/SQL baseline and identify impacted variations | Partial: all seven existing QCSM variation components visually inspected; broader variation inventory and SQL verification pending |
| 2 | Produce component-by-variation change matrix; resolve conditional decisions | QCSM seven-component current-state matrix captured below; target decisions and broader variations pending |
| 3 | Review and explicitly approve a bounded GUI change set | Two QCSM Final Optional changes approved during interactive session; broader approval pending |
| 4 | Apply one approved change set in DEV; log each before/after setting | Two QCSM Final Optional changes applied |
| 5 | Verify applied configuration via GUI and read-only SQL | GUI reopen verification complete for both; SQL verification pending |
| 6 | Create fresh test and inspect pre-entry RESULT records/UNITS | Not started |
| 7 | Observe automatic 1280 and 1460 F/T independently | Not started |
| 8 | Document outcomes and commit evidence | Initial implementation log committed as `fc05794`; this inventory update pending commit |

**Ordering constraint:** Do not change parent `Displayed` or `Reportable` defaults before verifying explicit variation coverage for every affected workflow; otherwise applicable fields may disappear. Do not modify shared `CALC_QC_CONC`, stock records, or RESULT rows as part of this configuration exercise.

## Initial targeted checkpoint — QCSM Final components

| Variation | Component | Observed O/D/R | Units | Proposed decision | Change status |
|---|---|---|---|---|---|
| OSHA_ID-110_QCSM | 1280 (Final) | T/T/T | UG | `Optional=F`, `Displayed=T`, `Reportable=T` | **Applied and GUI-verified** (ISE-QCSM-001) |
| OSHA_ID-110_QCSM | 1460 (Final) | T/T/T | UG | `Optional=F`, `Displayed=T`, `Reportable=T` | **Applied and GUI-verified** (ISE-QCSM-002) |
| OSHA_ID-110_QCSM | 1280 F/T | T/T/F | NONE | Mandatory/reportable status unresolved | Not approved |
| OSHA_ID-110_QCSM | 1460 F/T | T/T/F | NONE | Mandatory/reportable status unresolved | Not approved |

## Session log

### 2026-11-09 — Initiation

- User authorized beginning a careful implementation process, not an indiscriminate configuration rewrite.
- No DEV changes recorded in this log.
- **Next evidence request:** inspect the current `OSHA_ID-110_QCSM` variation-component configuration in the LabWare GUI, especially `1280 (Final)` and `1460 (Final)`, and confirm whether settings still match the Specialist's inventory. Do not edit or save.

### 2026-11-09 — Controlled DEV changes ISE-QCSM-001 and ISE-QCSM-002

**Scope:** ISE analysis, version 1; `OSHA_ID-110_QCSM` variation. The operator changed the variation-level **Optional** flag on two existing Final components. These were configuration edits, not RESULT data edits.

| Change ID | Component | Before (Optional / Displayed / Reportable) | After (Optional / Displayed / Reportable) | Variation units | Evidence and verification |
|---|---|---|---|---|---|
| ISE-QCSM-001 | `1280 (Final)` | T / T / T | **F / T / T** | `UG` | Operator changed Optional to False, saved analysis, reopened properties, and confirmed False persisted; `1460 (Final)` still True at that checkpoint. |
| ISE-QCSM-002 | `1460 (Final)` | T / T / T | **F / T / T** | `UG` | Operator changed Optional to False, saved ISE analysis, reopened properties, and confirmed False persisted. |

- Both variation property dialogs showed `Units=UG`, four decimal places, rounding `Decimal Places w/0.5 Up`, `Blanks Override=True`, and no populated detection/reporting limits. No changes to those properties were requested or reported.
- Earlier parent-level screenshots showed both Final components `Optional=T`, `Displayed=T`, `Reportable=T`; parent-level settings have **not** been changed as part of these two steps.
- The `1280 (Final)` calculated properties showed parent `Units=NONE`, `Auto-Calculate=True`; the QCSM calculation calls `SetOrCreateResult(..., "UG", ...)`. This discrepancy was observed, not changed or established as causal.
- Evidence source: interactive operator reports and LabWare GUI screenshots in the Director conversation. The screenshots have **not** been added to the repository; no independent SQL verification has been performed.
- **No fresh post-change test has been created or run.** No conclusion about RESULT-row precreation, unit availability, automatic F/T success, or scientific acceptance is warranted.
- The remaining QCSM components, other variations, and Tyler's parent-level defaults are **not yet implemented**. Do not treat this as completion of Tyler's directive.

**Next controlled checkpoint:** Preserve this updated log in Git. Then inspect remaining QCSM variation-component applicability and Optional/Displayed/Reportable settings, resolve requirements, and plan subsequent changes before creating a fresh regression test.

## 2026-11-09 — QCSM variation inventory and calculation dependency checkpoint

**Evidence:** Operator-provided LabWare DEV GUI screenshots and calculation text in the Director conversation. The seven-row inventory below is the **current observed state**, incorporating the two previously saved and reopened Final-component edits; it is **not** an approved end-state matrix. No additional configuration edits were reported during this inspection.

### Existing `OSHA_ID-110_QCSM` variation components

| Component | Optional | Displayed | Reportable | Units | Other observed properties / status |
|---|---|---|---|---|---|
| `Prep Volume` | T | F | F | `ML` | 1 place; rounding None; Blanks Override F; applicability/mandatory status unresolved |
| `1280 Concentration` | T | T | F | `PPM` | 1 place; rounding None; Blanks Override T; numeric/manual input |
| `1280 (Final)` | **F** | T | T | `UG` | 4 places; rounding Decimal Places w/0.5 Up; Blanks Override T; change ISE-QCSM-001 verified |
| `1280 F/T` | T | T | F | `NONE` | 3 places; rounding Decimal Places w/Odd Up; Blanks Override T |
| `1460 Concentration` | T | T | F | `PPM` | 1 place; rounding None; Blanks Override T; numeric/manual input |
| `1460 (Final)` | **F** | T | T | `UG` | 4 places; rounding Decimal Places w/0.5 Up; Blanks Override T; change ISE-QCSM-002 verified |
| `1460 F/T` | T | T | F | `NONE` | 1 place; rounding None; Blanks Override T |

**Important:** The two F/T components differ in decimal places and rounding. No formatting changes are authorized. The parent `1280 Concentration` component was observed as Numeric with `Units=MG_L`, while the QCSM variation uses `PPM`; this distinction was observed, not investigated or changed.

### Verified calculation dependencies

- Parent `1280 Mass`: Calculated, `Units=UG`, `Auto-Calculate=True`, parent Optional T / Displayed T / Reportable F. Equation: `RETURN solutionVolume*aliquotFactor*ppmFluoride`. Inputs: `1280 Solution Volume`, `1280 Dilution Factor`, `1280 Concentration`.
- Parent `1460 Mass`: Calculated, `Units=UG`, `Auto-Calculate=True`, parent Optional T / Displayed T / Reportable F. Same equation; inputs: `1460 Solution Volume`, `1460 Dilution Factor`, `1460 Concentration`.
- Parent `1280 (Final)` QCSM branch returns fluoride mass, invoking `SetOrCreateResult(..., "UG", ...)`; its non-QCSM branch divides mass by Air Volume. The previously inspected `1280 (Final)` calculation does not directly reference `Prep Volume`.
- `1280 Concentration` is a Numeric component rather than a calculated component. No direct `Prep Volume` dependency was found in the inspected 1280 concentration/mass/Final chain. This **does not establish** that Prep Volume is unnecessary in preparation or other workflows.
- The November diagnostic input example `50 mL * 1 * 9.02 ppm = 451` reproduces the observed 1280 Mass numeric value; it is diagnostic evidence, not a validated analytical measurement.

### Six absent explicit variation components requiring applicability review

The seven-row QCSM variation list **does not explicitly include** these components, although the inspected calculations reference them:

| Candidate component | Role | Decision status |
|---|---|---|
| `1280 Solution Volume` | 1280 Mass input | Evaluate explicit inclusion and required/display settings |
| `1280 Dilution Factor` | 1280 Mass input | Evaluate explicit inclusion and required/display settings |
| `1280 Mass` | Calculated intermediate feeding 1280 Final | Evaluate explicit inclusion and required/display settings |
| `1460 Solution Volume` | 1460 Mass input | Evaluate explicit inclusion and required/display settings |
| `1460 Dilution Factor` | 1460 Mass input | Evaluate explicit inclusion and required/display settings |
| `1460 Mass` | Calculated intermediate feeding 1460 Final | Evaluate explicit inclusion and required/display settings |

**Do not infer** from missing explicit variation rows that these components are unavailable at runtime: historical Result Entry displayed additional inherited parent components. Conversely, changing parent `Displayed` to F without first resolving variation coverage could remove necessary inputs.

### Open decisions / next gate

1. Establish whether `Prep Volume` is required for QCSM preparation or recordkeeping; its absence from the inspected calculations is insufficient to decide.
2. Establish whether both 1280 and 1460 analyte chains must be mandatory for every QCSM, or whether analyte-specific applicability varies by stock/test.
3. Determine intended F/T mandatory and reportable settings from approved method/workflow evidence; Tyler said F/T is *typically* reportable, not that all F/T rows must be changed indiscriminately.
4. Confirm the six candidate components' variation applicability, required/display flags, and any inheritance behavior before changing parent defaults.
5. Preserve the remaining ISE variations' workflows before broad parent changes. No fresh post-change RESULT-row or automatic F/T regression has been performed.

**Change count at this checkpoint:** exactly two confirmed DEV configuration edits (ISE-QCSM-001 and -002), both Optional T to F on the respective Final variation components. All other inspections are read-only GUI observations.

## Regression boundaries

- Existing November diagnostic sample `66454` is historical failure evidence, not a post-change acceptance sample. Do not save its diagnostic results.
- New tests must be created after configuration changes, with pre-entry RESULT rows and UNITS verified by read-only SQL.
- Do not double-click F/T or use Batch Calculate before observing the initial automatic path.
- Separate successful numeric computation, correct units, correct QCSM theoretical assignment, persistence, reportability, and scientific acceptance.

## Source references

- `PROJECT_CONTEXT.md` (commit `71cef13`)
- `ISE_CONFIGURATION_DIRECTOR_BRIEF.md` and `ISE_CONFIGURATION_INVESTIGATION_REPORT.md` (Specialist deliverables; repository placement pending)
- `docs/handoff/2026-11-06-id110-automatic-ft-regression.md` (commit `aceca65`)
- Tyler Erickson email, 2026-11-09 (retain source separately if organizational policy permits)
