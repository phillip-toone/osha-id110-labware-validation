# OSHA ID-110 LabWare Validation — Project Context

**Context established:** 2026-11-09
**Project repository:** `osha-id110-labware-validation`
**Project tracking reference:** `OSHA_LABS-3609` (reference only; Jira has **not** been kept updated)
**Environment:** `OSHA_LIMS_DEV`
**Status:** In progress; automatic F/T recovery calculation remains unvalidated.
**Document role:** Durable, high-level project state for Director and successor conversations. Detailed evidence remains in the referenced checkpoints and run files.

## 1. Project objective and acceptance boundary

Validate OSHA ID-110 ion-selective electrode (ISE) analysis configuration and workflows in LabWare DEV, including relevant 1280 fluoride and 1460 hydrogen fluoride results, QCSM recovery, batch components, and variations. Confirm that applicable analytical results calculate correctly in the intended laboratory workflow, with correct units, persistence, and reporting behavior.

The specific open defect is that an F/T (Found/Theoretical) recovery result can display **`Theoretical UNITS and actual results UNITS are not same category.`** during initial automatic calculation. Later double-click/manual recalculation has produced numeric F/T values. **Manual success is not evidence that automatic calculation passes.** Whether manual recalculation is an acceptable production workflow requires an explicit laboratory decision; Tyler's November 9 response instead directed configuration changes for components and variations.

Do not equate diagnostic calculations with scientifically accepted QC recoveries or completed method validation.

## 2. Working roles and continuity rules

- **User:** Controls priorities, consequential decisions, LabWare operations, SQL execution, and commits.
- **Director conversation:** Maintains overall status, decisions, evidence boundaries, delegation, and this context file.
- **Specialist conversation:** Performs a bounded technical investigation and returns a concise Director Brief plus a detailed Investigation Report for review, when warranted.
- **Context stewardship:** Propose checkpoints at important decisions, difficult-to-reconstruct findings, phase completions, or context transitions. Preserve important facts and explicitly mark superseded conclusions.

Working guidance: `PROJECT_DIRECTOR_PROTOCOL.md` and `CONTEXT_STEWARDSHIP_PROTOCOL.md` (supplied in conversation; their repository location has not been established). A new Director should read those protocols, this file, and then the referenced technical checkpoints as needed. Do not assume separate conversations can communicate automatically.

## 3. Confirmed investigation history

### October 14 — initial DEV F/T investigation

- Request `485932`, batch `OSHA_ID-110-261014-2`.
- QCSM035 sample `66260`, ISE test `74908`; QCSM036 sample `66261`, ISE test `74909`.
- For sample `66260`, the INGREDIENTS theoretical fluoride result was `90.262476717 UG` (`MASS`); the diagnostic `1280 (Final)` and `1460 (Final)` results were `451 UG` and `500 UG` (`MASS`).
- Both F/T results initially displayed the unit-category error. Later individual double-clicks recalculated the two F/T components independently to approximately `4.997` and `5.5394004041` respectively. These are diagnostic ratios, **not** acceptable recovery evidence.
- A subsequent batch calculation was also associated with successful numeric results, but the exact trigger was not established at that stage.

### October 16 — debugger-confirmed immediate failure mechanism

- Successful manual `1280 F/T` recalculation on sample `66260` resolved `foundResultUnit = UG`, `foundResultUnitCat = MASS`, `theoryUnit = UG`, and `theoryUnitCat = MASS`.
- Automatic calculation on diagnostic QCSM036 sample `66261` (using 1280 inputs for an execution-order test, **not** scientific QCSM036 recovery validation) proceeded through `1280 Concentration → 1280 Mass → 1280 (Final) → 1280 F/T`.
- `1280 (Final)` called `SetOrCreateResult()` with `UG` and returned `true`; F/T received the numeric found value `451.0`.
- At the automatic F/T lookup, `foundResultArray`, `foundResultUnit`, and `foundResultUnitCat` were empty, while `theoryUnit = UG` and `theoryUnitCat = MASS`. This is the **confirmed immediate mechanism** of the error, not proof of the underlying engine cause.
- Before saving, an independent read-only SQL session found no RESULT records for ISE test `74909`. Closing Result Entry without saving returned the interface to an unentered state.
- The LabWare 8 Technical Manual documents `SetOrCreateResult()` argument 8 as `commitFlag` and argument 10 as `triggerCalcsFlag`; both were passed `"F"` in the observed Final calculation. Deferred persistence is a **plausible contributing explanation**, not a proven sole root cause.
- A later experimental Debugging Assistant conditional expression calling `ResultFld()` was followed by a fatal LabWare client crash. **Do not repeat that expression-evaluation experiment.** The client was subsequently reopened; read-only SQL showed test `74909` incomplete with no RESULT rows, and the Debugging Assistant list was empty.

### November 6 — fresh regression

- Request `485934`, batch `OSHA_ID-110-261106-1`; user reported four QCSM samples in this batch (individual QCSM identities not yet all recorded here).
- Fresh ISE sample `66454` was used for a 1280 diagnostic. Inputs included pH `1.23`, solution volume `50 mL`, dilution factor `1`, E0 `1.1 mV`, EF- `2.2 mV`, concentration `9.02 ppm`.
- `1280 Mass = 451.0 UG` and `1280 (Final) = 451.0000 UG` appeared, but **automatic `1280 F/T` still displayed the same unit-category error**. No manual F/T recalculation was used for that observation. The new 1460 automatic path had not yet been tested at this checkpoint.
- The diagnostic entries were intended to remain **unsaved** to preserve a repeatable clean sample. Repository documentation preserves the failure; unsaved UI values are not a durable database record. Verify the current sample state before any new test.
- Tyler described a change to a salt/stock-table record and demonstrated successful double-click recalculation of existing results. The precise stock change was not recorded; his demonstration did **not** establish successful automatic calculation on fresh results.

## 4. November 9 — new direction from Tyler Erickson

**Source:** Tyler's email of 2026-11-09, supplied directly by the user. This is an explicit configuration instruction and the current highest-priority next workstream.

Tyler explained that when a result is optional, its RESULT row is not created until a value is entered; when **Optional = F**, the RESULT row is created when the test is created. He suggested the variation setup may therefore explain missing result metadata during initial automatic F/T calculation.

**Component-level defaults for all components:**

| Setting | Value |
|---|---|
| Optional | `T` |
| Displayed | `F` |
| Reportable | `F` |

**Variation-level overrides for components applicable to each variation:**

| Setting | Value / decision rule |
|---|---|
| Optional | `F` if mandatory for test completion; do not mark irrelevant components mandatory |
| Displayed | `T` for applicable components |
| Reportable | `T` only where reportable, typically `(Final)` and F/T results |

Each variation must include the components needed for that test. Inapplicable components should not be displayed or required (e.g., Air Volume for a bulk sample). Tyler requested that components and variations be updated accordingly.

**Interpretation / unproven hypothesis:** Pre-creating mandatory RESULT records may make Final result units available during the automatic F/T calculation. This aligns with the October debugger observations, but **has not been tested**. Do not label the defect fixed until a fresh post-change test passes.

## 5. Immediate next phase — configuration inventory and controlled implementation

1. **Inventory existing configuration** for ISE version 1: component-level Optional/Displayed/Reportable and variation-level component inclusion and overrides. Record actual variations, including `OSHA_ID-110_QCSM`, and discover other relevant variations rather than assuming their names or requirements.
2. **Prepare a proposed configuration matrix** comparing actual settings with Tyler's instructions. Identify applicability and mandatory/reportable decisions that need laboratory confirmation; avoid blanket variation-level `Optional = F` for every component.
3. **Review and approve changes before editing DEV.** Preserve a before-state export or read-only SQL evidence and document the exact modifications.
4. **Implement approved DEV configuration changes** through supported LabWare interfaces, not direct SQL writes.
5. **Create fresh tests after the change.** Verify via read-only SQL that required RESULT records exist at test creation, before analytical entry; existing tests may reflect old variation settings.
6. **Repeat automatic 1280 and 1460 F/T tests** on the appropriate QCSM samples using documented diagnostic or approved analytical inputs. Do not double-click F/T or use `Batch → Calculate` before recording initial automatic behavior.
7. **Check persistence and scientific acceptance separately.** Save/reopen only when an approved test plan permits it; distinguish arithmetic execution, correct units, correct QCSM mapping, and acceptable recovery.
8. **Document outcomes** in a dated technical checkpoint and run record, then review and commit only intended files.

**Delegation candidate:** A Specialist can perform the configuration inventory/change-matrix investigation with the LabWare 8 Technical Manual and read-only SQL results supplied by the user. Return a Director Brief and a detailed report. The Director and user review the matrix before any DEV edits.

## 6. Data and safety constraints

- Work in `OSHA_LIMS_DEV`; do not modify production records.
- Prefer narrowly scoped, read-only SQL through LabWare's built-in SQL interface. The user executes queries; the Director/Specialist should not imply direct database access.
- Never infer a persisted RESULT row from an unsaved Result Entry display.
- Avoid direct SQL modifications, forced broad `Save()` calls inside calculations, or changing shared `CALC_QC_CONC` without approved evidence and impact assessment.
- Do not repeat the `ResultFld()` conditional Debugging Assistant expression associated with the client crash.
- Separate **historical diagnostic values** from approved analytical measurements and acceptance evidence.
- Preserve current sample state and make no unapproved result saves or authorizations.
- Do not invent missing batch component values, sample identities, instrument IDs, or stock records. Retrieve actual saved data using SQL when appropriate.
- Run documentation and method reference work may coexist with untracked files; stage explicit paths, not `git add .`, until unrelated files have been reviewed.

## 7. Repository map and evidence references

**Known repository locations:**

| Path | Purpose |
|---|---|
| `docs/handoff/2026-10-14-id110-ft-validation-checkpoint.md` | Initial unit-category investigation and handoff |
| `docs/handoff/2026-10-16-id110-ft-debugger-findings.md` | Automatic versus manual debugger traces |
| `docs/handoff/2026-11-06-id110-automatic-ft-regression.md` | Fresh November regression failure |
| `docs/handoff/CURRENT.md` | Existing historical handoff; not updated with every newer checkpoint at user preference |
| `docs/actions/2026-10-08-ise-demo-followup.md` | ISE demonstration action checklist |
| `runs/485931/Request_485931.xml` | Earlier request evidence |
| `runs/485932/Request_485932.xml` | October focused run request |
| `runs/485933/Request_485933.xml` | October debugger-period request |
| `runs/485934/Request_485934.xml` | November fresh regression request |
| `reference/method/` | Method-reference work, including files reported untracked as of November 6; review separately |

**Known commits on `main`:**

| Commit | Evidence |
|---|---|
| `f40cf75` | October 14 checkpoint, checklist, request XMLs |
| `2a1377d` | October 16 debugger findings and request XML |
| `aceca65` | November 6 automatic F/T regression checkpoint and request XML |

The November 6 push succeeded; four method-reference items were still untracked afterward. **Current working-tree status has not been rechecked for November 9.** Jira `OSHA_LABS-3609` has been referenced in documents but the user explicitly stated they have not been maintaining Jira updates.

**Separate performance documentation:** `OTC-FY2027-Performance` repository, `journal/2026-10.md`, with October 14 and 16 entries; last reported performance commit `c8f7403`. Future material updates can be documented separately after technical evidence is established.

## 8. Outstanding questions

- Which ISE variations exist in DEV, and what are their current component-level and variation-level settings?
- Which components are scientifically required, displayed, and reportable in each variation? Which decisions need Tyler's confirmation?
- Does Tyler's variation configuration change cause required RESULT rows, particularly Final results, to exist when the test is created?
- Does a new test automatically calculate both 1280 and 1460 F/T with correct units and no manual intervention?
- What exact salt/stock-table record did Tyler modify previously, and does it affect theoretical mass or only separate stock metadata?
- What are the current approved analytical inputs, reagent/instrument identities, batch requirements, and acceptance criteria for the November regression?
- What is the approved persistence/save workflow for diagnostic versus validation results?

## 9. Context maintenance

Update this file when a consequential decision is made, a major hypothesis is verified or rejected, a phase finishes, or a new handoff is needed. Keep the current state concise and link to detailed reports rather than copying them wholesale. Preserve superseded conclusions with an explanation. Record a date and reference for major updates. A draft is not a repository change until the user places it in the repository and commits it.
