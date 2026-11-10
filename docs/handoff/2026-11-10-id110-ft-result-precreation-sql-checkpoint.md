# OSHA ID-110 — November 10 F/T RESULT precreation investigation

**Environment:** OSHA_LIMS_DEV
**Request:** 485935
**Batch:** OSHA_ID-110-261110-1
**QCSM sample:** 66484 (QCSM035)
**ISE test:** 75286, version 1
**Status:** Read-only SQL findings confirmed; proposed F/T optionality experiment NOT YET IMPLEMENTED.

## Purpose and context

Determine why the November 10 interim regression did not yield an automatic `1280 F/T` result after the variation-level `Optional` setting of both Final components was changed from `T` to `F` (changes ISE-QCSM-001 and ISE-QCSM-002). This is a diagnostic investigation, not scientific acceptance testing.

## Observed regression

With unsaved diagnostic inputs (1280 pH 1.11, Solution Volume 50 mL, Dilution Factor 1, E0 1.11 mV, EF- 2.22 mV, Concentration 9.02 ppm), `1280 Mass` displayed 451.0 UG and `1280 (Final)` displayed 451.0000 UG. `1280 F/T` remained blank / Not Entered on initial automatic execution. Double-clicking `1280 F/T` produced the familiar theoretical-versus-actual unit-category error. The operator exited Result Entry without saving; reopening showed the analytical values as Not Entered. The 1460 path was not tested.

## Verified read-only database evidence

The following observations were reported from DEV SQL queries after the diagnostic entries were discarded:

| Test | Result number | Result name | ENTRY | UNITS |
|---|---:|---|---|---|
| 75286 | 589777 | `1280 (Final)` | NULL | UG |
| 75286 | 589778 | `1460 (Final)` | NULL | UG |

No `1280 F/T` or `1460 F/T` RESULT rows appeared in the direct RESULT-existence query for test 75286. `UNITS` lookup returned `UG` -> `MASS`. `CALC_VARIABLES` for ISE version 1 returned `1280 F/T` / `found` -> `1280 (Final)` and `1460 F/T` / `found` -> `1460 (Final)`.

The direct reproduction of the non-ad-hoc `CALC_QC_CONC` unit-lookup SQL, scoped to test 75286, returned **no rows**. That SQL starts from `RESULT final_result` (the F/T result under calculation), joins `CALC_VARIABLES` on the F/T component name, and then joins to the source Final RESULT. With no persisted F/T RESULT row, the join cannot return the Final units for this test. This is a demonstrated SQL prerequisite failure for the queried persisted state; it does **not** independently prove that the same query ran with the same database visibility during the unsaved GUI calculation.

## Interpretation and limits

- Making the two Final components non-optional is associated with pre-existing Final RESULT rows containing `UG`; this is consistent with Tyler Erickson's November 9 explanation of RESULT precreation.
- The F/T components remain `Optional=T`, `Displayed=T`, `Reportable=F`, `Units=NONE` at the QCSM variation level, and no F/T RESULT rows were found for this test.
- Missing F/T rows explain why the **reproduced persisted-state SQL join** returned no rows. Whether making F/T non-optional fixes automatic execution remains an **untested hypothesis**.
- `Optional=F` for F/T is permitted by Tyler's instruction **only if F/T is mandatory for test completion**. This laboratory requirement has not been independently established. A diagnostic change must not be represented as a finalized approved mandatory-status decision.
- No modification to `CALC_QC_CONC`, parent components, stock, existing RESULT rows, or calculation timing is authorized by this checkpoint.

## Proposed bounded experiment — awaiting explicit operator approval

| Proposed ID | Variation | Component | Current Optional | Experimental Optional | Other properties |
|---|---|---|---|---|---|
| ISE-QCSM-003 | OSHA_ID-110_QCSM | `1280 F/T` | T | F | Preserve Displayed=T, Reportable=F, Units=NONE, formatting |
| ISE-QCSM-004 | OSHA_ID-110_QCSM | `1460 F/T` | T | F | Preserve Displayed=T, Reportable=F, Units=NONE, formatting |

**Before editing:** Confirm the proposed changes are acceptable as a DEV-only diagnostic experiment despite the unresolved mandatory-status question. Record the current analysis version/configuration and verify the before-state in the GUI.

**If approved:** Change one variation property at a time in the LabWare GUI; save and reopen to verify each persisted value. Do not use SQL writes. Record any newly generated analysis version. For rollback, restore each F/T variation Optional flag to `T` using the GUI, then save and verify; note that versioned changes do not retroactively modify existing assigned tests.

**After editing:** Create a *new* disposable QCSM sample and assign a *new* ISE test after the configuration save; existing test 75286 cannot serve as the post-change test. Before analytical entry, query RESULT existence, names, numbers, ENTRY and UNITS. Then perform an explicitly authorized, unsaved automatic F/T regression, capturing initial automatic behavior before any double-click. Keep 1280 and 1460 observations separate. Do not save diagnostic analytical results without a distinct approval.

## Documentation follow-through

Preserve this checkpoint under `docs/handoff/2026-11-10-id110-ft-result-precreation-sql-checkpoint.md` and cross-reference it from `docs/implementation/2026-11-09-ise-configuration-implementation.md` at the next controlled update. The last reported implementation-log commit before this investigation was `1170b4f`; the later Specialist handoff commit was `d512cce`. The November 10 regression report and SQL evidence should be separately committed when available. Do not stage unrelated untracked method references or request files by default.
