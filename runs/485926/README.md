# Validation Run 485926

Reference case: `HD-2026-12-02`\
Batch: `OSHA_ID-110-260924-1`\
Intentional endpoint: **Needs Reviewer**

This is the first thoroughly documented recycled execution of the known
historical dataset. Future attempts should reuse the same case data with
a new incremented request number.

## What this run demonstrated

-   Batch equipment, reagent, buffer, ICV, and STD A-D inventory
    traceability can be assigned.
-   Manual 1280 and 1460 result entry works.
-   1280 Mass and Final calculations reproduced the historical reference
    values.
-   1460 Mass calculations reproduced the historical reference values.
-   1460 Final calculates for field samples with valid Air Volume.
-   LabWare correctly required 1280 results before allowing Ready for
    Review, consistent with the internal work instruction requiring
    MCEF/1280 even when only HF is requested.
-   The batch successfully reached **Needs Reviewer**.
    Reviewer/authorization was intentionally not tested because that
    generic workflow has already been validated elsewhere.

## Findings requiring investigation

1.  `1460 F/T` appears incompletely implemented: unlike `1280 F/T`, it
    has no calculation source code and no calculation variables.
2.  1460 QCSMs calculate the expected Mass but invoke the
    air-volume-dependent `1460 (Final)` calculation and generate
    divide-by-zero errors.
3.  `HF (1460) QCSM Precision` cannot calculate because LabWare reports
    no QCSM recovery results.
4.  `Batch -> Calculate` generated roughly 120 repeated
    component-replacement prompts, repeated divide-by-zero errors, and
    cleared some previously entered batch traceability values.
5.  A complete ID-110 run should contain both 1280 (`QCSM035`) and 1460
    (`QCSM036`) QCSM sets. Run 485926 contained only QCSM036.

## Run artifact

Sample `65719` / `485926-SAMPLE 1` had its ISE test cancelled during
interactive testing. Audit history recorded `CancelTest` with reason
`Calculation Update`. The analyst believes this resulted from an
accidental UI action while creating screenshots. Do not treat it as a
confirmed LabWare defect unless independently reproduced.

## Next iteration

Before recycling this case again, investigate `CALC_QC_CONC`, fully map
the working 1280 QCSM recovery implementation, repair/complete the
analogous 1460 recovery path, ensure both QCSM sets are included, and
investigate `Batch -> Calculate`. Then increment the request number and
create a new `runs/<request-number>/` record.
