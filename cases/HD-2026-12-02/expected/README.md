# Expected Validation Targets — HD-2026-12-02

This directory is for curated expectations derived from the historical reference records in `../reference/`.

The original PDFs remain the authoritative source evidence. Files here should make the validation targets easier to compare against LabWare without duplicating the PDFs.

## Established targets

### Historical sample population

Historical samples: `F58027-F58034`.

Both OSHA ID-110 result types are relevant to the historical case:

- `1280` — Fluorides (as F)
- `1460` — Hydrogen Fluoride (as F)

### Reporting limits

The historical reporting-limit record shows a reporting limit of **25 µg** for both 1280 and 1460.

### QCSM spike pattern

The legacy QC preparation records establish the repeating QCSM spike sequence:

- 10 µL
- 20 µL
- 40 µL
- 0 µL blank

The exact theoretical µg amount depends on the actual concentration of the QCSM spiking standard used in a particular LabWare run. Therefore, do not hard-code the historical theoretical amounts when a newly prepared/verified spiking solution has a different calculated concentration.

For the currently verified LabWare spiking standard `LAB_SOL00265-0006-001`, the observed LabWare QCSM values are:

- 10 µL → `90.26247617 µg F`
- 20 µL → `180.52495343 µg F`
- 40 µL → `361.04990687 µg F`
- 0 µL → `0.0 µg F`

These are run/configuration-derived validation checkpoints, not edits to the historical source records.

### Working-standard interpretation

The historical stock-standard documentation describes working concentrations after final T-T buffer preparation. Consequently, LabWare inventory concentrations before that final preparation can be approximately twice the nominal analytical working-standard concentration. This relationship was observed and should not automatically be treated as a defect.

## Future files

As validation proceeds, consider adding concise derived records such as:

- `results.md` — historical/reference result targets
- `qc.md` — QC/QCSM targets and acceptance behavior
- `calculations.md` — expected 1280/1460 calculations
- `acceptance-criteria.md` — explicit validation pass/fail criteria

Avoid copying source PDFs or duplicating full source tables here.
