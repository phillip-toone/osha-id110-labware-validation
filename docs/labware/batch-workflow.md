# OSHA ID-110 Batch Workflow

## Creation

Use
`Batch Creation -> ISE / OSHA_ID-110 -> Create Batch by Barcode Scan`.

The scanner behaves as keyboard input.

A complete ID-110 batch should include field samples plus both QCSM035
(1280) and QCSM036 (1460).

## Batch traceability

Batch Results records instrument, diluter, balance, NaOH, T-T buffers,
pH buffers, HCl, ICV, STD A-D, and QCSM precision results.

Inventory selection uses the full inventory-entry ID.

## Batch menu

-   **Enter Results** --- manual analytical entry.
-   **Calculate** --- current behavior is problematic and under
    investigation.
-   **Batch Add Analyte** --- not required in the normal workflow
    currently understood.
-   **Batch Review Window** --- analyst completion/review handoff.
-   **Assign Batch Stock** --- stock assignment.
-   **Batch Process Results** --- instrument-file processing; later
    integration phase.

## Analyst endpoint

`Batch Review Window -> Mark Ready for Review`.

Run 485926 confirmed that LabWare blocks this transition when required
1280 results are missing. Once required results were present, the batch
transitioned to **Needs Reviewer**.

Reviewer/authorization is outside the current ID-110 analysis-validation
scope.
