# Run 485926 — Batch Record

## Batch identity

- **Batch:** `OSHA_ID-110-260924-1`
- **Batch Link:** `ISE`
- **Type:** `TEST`
- **Owner:** `PTOONE`
- **Status:** Incomplete
- **State:** Open
- **Sample count:** 11

The batch was created through:

`Batch Creation → ISE / OSHA_ID-110 → Create Batch by Barcode Scan`

The barcode scanner behaves as keyboard input; sample numbers were typed manually.

## Equipment traceability

Confirmed from the actual equipment used:

| Batch field | Asset | Description |
|---|---|---|
| ISE Instrument Used | `FAA7703` | Patty, pH/ISE Meter, Thermo Scientific, Orion Dual Star |
| Diluter Used | `FAA7736` | Spider-Pig, Diluter, Hamilton ML600 |
| Balance Used | `FAD0417` | Mettler Toledo XPR225DR; 220 g/121 g; 0.1 mg/0.01 mg readability |

## Supporting reagents and buffers selected

| Batch field | Inventory | Supporting detail |
|---|---|---|
| 5N NaOH | `LAB_SOL00252-0001-001` | sample 65267; 1000 mL; expires 07/23/2027 |
| T-T Buffer Concentrated | `LAB_SOL00253-0001-001` | sample 65268; 1000 mL; expires 07/27/2027 |
| T-T Buffer Analytical | `LAB_SOL00254-0001-001` | sample 65269; 2000 mL; expires 07/27/2027 |
| Buffer pH 4 | `REAG00003-0005-001` | validation inventory |
| Buffer pH 7 | `REAG00004-0005-001` | validation inventory |
| Buffer pH 10 | `REAG00002-0005-001` | validation inventory |
| Hydrochloric Acid (HCl) | `REAG00021-0011-018` | sample 8823; 2500 mL; expires 04/03/2028 |

`REAG00021-0011-018` is the HCl inventory selected for this validation run; it should not be represented as necessarily being the historical bottle used in the legacy analysis.

## Standards selected

| Batch field | Inventory | Sample / detail |
|---|---|---|
| ICV Standard | `LAB_SOL00264-0001-001` | sample 65714; 1000 mL; expires 09/02/2027 |
| STD A (RLV) | `LAB_SOL00260-0002-001` | sample 65319; 1000 mL; expires 08/07/2027 |
| STD B | `LAB_SOL00261-0003-001` | sample 65709; 1000 mL; expires 09/02/2027 |
| STD C (CCV) | `LAB_SOL00262-0002-001` | sample 65710; 1000 mL; expires 09/02/2027 |
| STD D | `LAB_SOL00263-0002-001` | sample 65711; 1000 mL; expires 09/02/2027 |

## Supporting validated standard chains

### Primary calibration chain

- NaF source: `STD02189-0006-001`
- Stock STD: `LAB_SOL00256-0003-001`
  - 1.10563 g source material
  - 500 mL final volume
  - observed concentration 1010.6011015 µg/mL
- Intermediate STD: `LAB_SOL00258-0003-001`
  - 100 mL parent
  - 1000 mL final volume
  - observed concentration 101.06011015 µg/mL

The direct NaF-derived configuration uses `X_MOLE_FRACTION = 0.4525` to express fluoride rather than total sodium fluoride.

### Independent ICV chain

- Independent NaF source: `STD02190-0003-001`
  - purity 99.1
  - molar mass 41.9882 g/mol
- Stock ICV: `LAB_SOL00257-0001-001`
  - 2.2113 g
  - observed concentration 991.60773075 µg/mL
- Intermediate ICV: `LAB_SOL00259-0001-001`
  - 100 mL parent
  - observed concentration 99.160773075 µg/mL
- Final ICV: `LAB_SOL00264-0001-001`
  - 100 mL parent
  - observed preparation concentration 9.9160773075 µg/mL

Preserve observed LabWare values; do not silently force them to nominal labels.

## QCSM spiking standard

`LAB_SOL00265-0006-001`

- Parent: `STD02189-0006-001`
- Quantity: 301.93 mg
- Calculated concentration: 9026.2476717 µg/mL

This is the verified spiking standard used by the QCSMs associated with this validation work.

## Batch Results status at current stopping point

Entered:

- ISE Instrument Used
- Diluter Used
- Balance Used
- 5N NaOH
- T-T Buffer Concentrated
- T-T Buffer Analytical
- Buffer pH 4
- Buffer pH 7
- Buffer pH 10
- Hydrochloric Acid (HCl)
- ICV Standard
- STD A (RLV)
- STD B
- STD C (CCV)
- STD D

Visible but not entered:

- Batch Attachment — optional
- Method Modified — optional/reportable
- Modified Method Notes — optional/reportable
- MCE Filter Lot (for wiping cassettes) — optional
- `F (1280) QCSM Precision` — reportable
- `HF (1460) QCSM Precision` — reportable

Do not invent or manually populate the QCSM precision values unless the configured analytical workflow explicitly requires it.

## Next batch action

Open the **Run** menu and continue the configured OSHA ID-110 workflow.

Do not authorize Batch Results yet.
