# HD-2026-12-02 — Reference Case

This directory contains the historical OSHA ID-110 analytical case being used as the reference for LabWare validation.

## Identifiers

- **Case ID:** `HD-2026-12-02`
- **Original sampling number:** `485923`
- **Inspection / reporting identifier:** `522300`
- **Historical sample range:** `F58027-F58034`
- **Method:** OSHA ID-110 — Fluorides by Ion Selective Electrode
- **Analytes under validation:**
  - `1280` — Fluorides (as F)
  - `1460` — Hydrogen Fluoride (as F)

## Purpose

The files under `reference/` are the historical analytical records used to establish the behavior and results that the LabWare implementation is intended to reproduce or correctly calculate.

This case may be exercised through LabWare more than once. Individual LabWare attempts belong under `../../runs/` and should link back to this case rather than duplicating the historical reference material.

Current LabWare attempt:

- [`runs/485926`](../../runs/485926/) — first documented LabWare run against this case

## Directory roles

- `reference/` — original historical records. Preserve these as source evidence; do not edit them to reflect LabWare findings.
- `expected/` — curated validation targets derived from the reference records. This is where expected results, calculations, QC targets, and acceptance criteria should be documented without modifying the originals.

## Important distinction

The legacy records include LISA-era workflows and outputs. They are reference evidence, not instructions for operating LabWare. Validation should allow LabWare's configured OSHA ID-110 workflow to execute and then compare its behavior and results with the scientifically expected/reference outcome.
