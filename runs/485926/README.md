# LabWare Validation Run 485926

**Reference case:** [`HD-2026-12-02`](../../cases/HD-2026-12-02/)  
**Current LabWare batch:** `OSHA_ID-110-260924-1`  
**Environment:** OSHA_LIMS_DEV / LabWare 8  
**Status:** In progress

## Purpose

This is the first documented LabWare execution against historical reference case `HD-2026-12-02`.

The run uses a new XML request and newly generated LabWare field samples while comparing LabWare behavior and results with the historical OSHA ID-110 reference case.

A future rerun of the same historical case should receive its own directory under `runs/` and point back to the same case rather than duplicating the historical PDFs.

## Input request

Request number: `485926`

Important XML metadata:

- Reporting ID: `0522300`
- Inspection number: `1860678`
- Sampling date: `2026-01-12`
- Establishment: Pursuit Aerospace Cleveland
- Seven Personal samples plus one separately represented blank
- Requested analyte on the seven Personal samples: `1460 — Hydrogen Fluoride (as F)`

The XML itself should be stored under `input/` only if repository policy permits retaining that source material in Git.

## Current run state

The seven field samples were imported and batch-created through **Create Batch by Barcode Scan**.

Four verified `QCSM036-0001` samples were added when LabWare prompted for QC samples.

Batch `OSHA_ID-110-260924-1` contains 11 samples total:

- 4 QCSM036 samples
- 7 request-485926 field samples

Batch-level equipment, reagent, buffer, ICV, and calibration-standard traceability has been entered.

The batch remains open and incomplete.

## Important observation under investigation

Although the XML explicitly requests `1460`, expanding the imported field samples in the batch tree currently shows `1280 (Final)` beneath the ISE analysis. No manual correction has been made.

This is intentionally being carried forward through the configured workflow to determine whether later OSHA ID-110 logic generates/derives the 1460 result or whether the behavior represents a configuration defect.

Do not manually add analyses solely to make the display match expectations.

## Immediate next step

Open the batch **Run** menu and continue the configured OSHA ID-110 analytical workflow one controlled step at a time.

Do not authorize batch Results yet and do not manually enter QCSM Precision values unless the configured workflow explicitly requires it.

See:

- [`samples.md`](samples.md) for sample/QCSM mapping
- [`batch.md`](batch.md) for exact batch traceability
- [`../../docs/handoff/CURRENT.md`](../../docs/handoff/CURRENT.md) for the comprehensive project handoff
