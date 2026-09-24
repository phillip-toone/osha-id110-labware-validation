# Run 485926 — Samples and QCSMs

## Field-sample mapping

The XML request produced seven Personal LabWare samples. Barcode entry confirmed the mapping:

| LabWare sample | Text ID | XML air volume (L) |
|---:|---|---:|
| `65719` | `485926-SAMPLE 1` | 79.04 |
| `65720` | `485926-SAMPLE 2` | 83.6 |
| `65721` | `485926-SAMPLE 3` | 88.16 |
| `65722` | `485926-SAMPLE 4` | 98.8 |
| `65723` | `485926-SAMPLE 5` | 91.2 |
| `65724` | `485926-SAMPLE 6` | 88.16 |
| `65725` | `485926-SAMPLE 7` | 98.8 |

All seven were scanned into the batch as `PERSONAL` samples under `ISE / OSHA_ID-110`.

## QCSMs included in the batch

The batch-creation workflow prompted separately for Quality Control Samples. The following verified `QCSM036-0001` samples were scanned:

| Sample | QCSM | Spike | Calculated F |
|---:|---|---:|---:|
| `65241` | `QCSM036-0001-001` | 10 µL | 90.26247617 µg |
| `65242` | `QCSM036-0001-002` | 20 µL | 180.52495343 µg |
| `65243` | `QCSM036-0001-003` | 40 µL | 361.04990687 µg |
| `65244` | `QCSM036-0001-004` | 0 µL | 0.0 µg |

These QCSMs use:

- QCSM spiking standard: `LAB_SOL00265-0006-001`
- Physical medium: `FES0002281` Na2CO3-impregnated backup-pad media
- Media quantity: 1 Each / concentration N/A

All four QCSM samples were Complete and activated/opened before batch creation.

## Batch positions

| Position | Sample | Role |
|---:|---:|---|
| 1 | `65241` | QCSM036 10 µL |
| 2 | `65242` | QCSM036 20 µL |
| 3 | `65243` | QCSM036 40 µL |
| 4 | `65244` | QCSM036 blank |
| 5 | `65719` | Field SAMPLE 1 |
| 6 | `65720` | Field SAMPLE 2 |
| 7 | `65721` | Field SAMPLE 3 |
| 8 | `65722` | Field SAMPLE 4 |
| 9 | `65723` | Field SAMPLE 5 |
| 10 | `65724` | Field SAMPLE 6 |
| 11 | `65725` | Field SAMPLE 7 |

No unexpected samples were present in positions 1–11. Positions after 11 were empty when checked.

## Related QCSM035 set prepared during validation

A corrected 1280 QCSM set was also prepared and activated, but it is **not currently in this batch**:

| Sample | QCSM | Spike | Calculated F |
|---:|---|---:|---:|
| `65715` | `QCSM035-0005-001` | 10 µL | 90.26247617 µg |
| `65716` | `QCSM035-0005-002` | 20 µL | 180.52495343 µg |
| `65717` | `QCSM035-0005-003` | 40 µL | 361.04990687 µg |
| `65718` | `QCSM035-0005-004` | 0 µL | 0.0 µg |

Do not add these retroactively merely because the batch exposes both 1280 and 1460-related result fields. Continue the configured workflow and observe what LabWare does.

## Analysis-assignment observation

The request XML explicitly asks for `1460 — Hydrogen Fluoride (as F)`. However, after batch creation the visible sample tree showed `1280 (Final)` beneath the ISE analysis for the imported field samples.

This remains an observation under validation, not yet a confirmed defect. No manual correction has been made.
