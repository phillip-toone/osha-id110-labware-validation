# OSHA ID-110 QCSM Processing

## Stocks

-   `QCSM035` --- 1280 particulate fluoride / MCEF
-   `QCSM036` --- 1460 HF / Na2CO3 backup pad

A complete ID-110 run requires QC coverage for both pathways.

## Spiking standard

Validated inventory: `LAB_SOL00265-0006-001`

Concentration: `9026.2476717 µg/mL`

NaF-to-fluoride relationship: `X_MOLE_FRACTION = 0.4525`

## Spike sequence

10 / 20 / 40 / 0 µL

Theoretical fluoride:

-   90.26247617 µg
-   180.52495343 µg
-   361.04990687 µg
-   0.0 µg

## Verified sets

1280: `QCSM035-0005`, samples 65715-65718.

1460: `QCSM036-0001`, samples 65241-65244.

## Creation pattern

Create QCSM inventory in Stock Inventory Manager, create four samples,
assign common spiking solution and physical media, enter 10/20/40/0 µL
INGREDIENTS quantities, complete INGREDIENTS tests, then activate all
four entries.

## Current investigation

The working 1280 recovery path uses `CALC_QC_CONC`. The 1460 F/T
implementation appears incomplete. Fully trace the 1280 implementation
and subroutine before repairing 1460.
