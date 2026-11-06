# 2026-11-06 — OSHA ID-110 fresh-batch automatic F/T regression checkpoint

**Environment:** `OSHA_LIMS_DEV` (LabWare web Result Entry)

**Status:** **FAILED — initial automatic 1280 F/T calculation.** This is an observed diagnostic regression, **not** acceptance evidence. No result-save operation was reported at the checkpoint. Confirm the Result Entry window was closed **without saving** and independently verify persistence before describing the sample as reset.

## Identification and scope

| Item | Observation |
| --- | --- |
| Date | 2026-11-06 |
| Batch | `OSHA_ID-110-261106-1` (visible in batch screenshot) |
| Request | **Not yet independently confirmed**; do not assume `485934` |
| Sample | `66454` |
| Analysis | `ISE`, replicate 1 |
| Test status in Result Entry | `Incomplete` |
| ISE test number | **Not captured** |
| Sample QC type / variation | **Not captured**; verify before attributing QCSM035/QCSM036 acceptance |
| Jira | `OSHA_LABS-3609` |

## Reason for the test

LabWare consultants reported that Tyler had fixed the F/T issue. In a subsequent discussion Tyler explained that he changed information associated with a salt in the stock tables, **not** the ISE calculation code, and demonstrated that double-clicking an existing F/T result could recalculate it. The October investigation had **already** demonstrated manual F/T recalculation. The open acceptance question is whether F/T calculates correctly **automatically on first entry**, without double-clicking F/T or invoking `Batch > Calculate`.

A November inspection found that the `1280 (Final)` QCSM branch still calls `SetOrCreateResult(..., "UG", , , "F", , "F", , , )`; `1280 F/T` still calls `CALC_QC_CONC`; and the relevant `CALC_QC_CONC` measured-unit lookup and category comparison appeared unchanged from the October debugger evidence. These observations do not identify Tyler's stock-table edit or prove the absence of other changes.

## November 6 observed sample-level entries

The following were entered in Result Entry for sample `66454` as a **historical diagnostic replay**, not new instrument measurements or approved analytical values.

| Component | Observed entry / display | Units | Status |
| --- | --- | --- | --- |
| Air Volume | `null` | L | Entered (displayed) |
| 1280 pH | `1.23` | — | Entered |
| 1280 Solution Volume | `50` | mL | Entered |
| 1280 Dilution Factor | `1` | — | Entered |
| 1280 E0 | `1.1` | mV | Entered |
| 1280 EF- | `2.2` | mV | Entered |
| 1280 Concentration | `9.02` | ppm | Entered |
| 1280 Mass | `451.0` | µg | Calculated / displayed as Entered |
| 1280 (Final) | `451.0000` | µg | Calculated / displayed as Entered |
| 1280 F/T | `Theoretical UNITS and actual results UNITS are not same category.` | — | Error displayed as Entered |
| 1460 components | No entries | — | Not Entered |

The 1280 F/T error appeared during normal entry of upstream values. **No manual F/T double-click or `Batch > Calculate` was reported for this observation.** The screenshot alone does not establish whether a save occurred, and no post-save/reopen persistence test was performed.

## Result and interpretation

**Observed:** On a fresh November sample, the upstream mass and Final calculations produced numeric values with `µg` displayed, while automatic `1280 F/T` again displayed the same unit-category error seen in October.

**Conclusion supported:** The specific **initial automatic 1280 F/T acceptance criterion has not passed** in this test. Tyler's demonstration of successful manual recalculation is not equivalent to successful first-pass automatic calculation.

**Not yet established:** Whether the internal November failure is caused by the same empty measured-unit lookup documented in October; whether Tyler's stock-table edit changed the theoretical ingredient; whether the correct QC type and theoretical mass are associated with sample `66454`; whether 1460 F/T works; and whether a save/reopen changes behavior.

## Preservation and safety instructions

1. **Do not save or authorize the diagnostic Result Entry values.** Close/cancel without saving when ready, then verify that the sample returns to the unentered state. Do not claim the database was unchanged until checked.
2. Preserve the screenshot or a faithful written transcription of the failure and the exact input values. The screenshot is evidence of an **unsaved UI state**, not a persisted RESULT record.
3. Do not double-click F/T or use `Batch > Calculate` before documenting the initial automatic failure; these actions may replace the visible error with a manually recalculated value.
4. Do not change shared `CALC_QC_CONC`, `SetOrCreateResult()` commit flags, or stock-table data as part of this observational checkpoint.
5. Avoid repeating the prior Debugging Assistant `ResultFld()` conditional-expression experiment, which preceded a fatal client crash.

## Next controlled actions

- Confirm the Result Entry window was exited **without saving**; perform a read-only SQL check of the new ISE test and RESULT rows once its test number is known.
- Confirm the new request ID, sample QC type, ISE test number, and theoretical INGREDIENTS concentration for sample `66454`.
- Record the precise stock-table record/field Tyler changed if available, distinguishing his claim from verified configuration evidence.
- Plan a separate first-pass 1460 F/T test on the scientifically appropriate QC sample without manual recalculation.
- Capture batch-level inputs and saved/unsaved state in the run directory; avoid inventing missing instrument, reagent, or temperature values.

## Prior evidence

- `docs/handoff/2026-10-14-id110-ft-validation-checkpoint.md`
- `docs/handoff/2026-10-16-id110-ft-debugger-findings.md`
- October diagnostic batch `OSHA_ID-110-261014-2`, request `485932`; manual F/T recalculation produced numeric ratios, but initial automatic F/T failed.

**Documentation classification:** `OBSERVED_UI` for November 6 screenshot and entries; `HISTORICAL` for October comparison; `UNRESOLVED` for root cause, persistence, and acceptance.
