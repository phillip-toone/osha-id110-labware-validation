# LabWare OSHA ID-110 --- Analysis Model

## Shared ISE analysis

LabWare analysis `ISE` is shared infrastructure, not synonymous with
ID-110. Its optional component set includes ID-110 1280/1460 components
and future ID-101/chlorine components.

Seeing an optional component in the UI does not prove a `RESULT` row has
been instantiated. Query `RESULT` to determine persisted sample results.

## Both 1280 and 1460 are required

The internal work instruction requires the MCEF to be prepared and
analyzed even when only HF is requested. Therefore a normal ID-110
execution requires both particulate fluoride (1280) and HF (1460).

## Main components

1280: pH, Solution Volume, Aliquot Factor, E0, EF-, Concentration, Mass,
Final, F/T.

1460: pH, Solution Volume, Aliquot Factor, E0, EF-, Concentration, Mass,
Final, F/T.

Shared/future chlorine: Chloride (Raw), Chlorine Gas (Final), Available
Chlorine (Final), Cl F/T.

## Known calculations

-   1280 Mass: `solutionVolume * aliquotFactor * ppmFluoride`
-   1280 Final: `fluorideMass / airVolume`
-   1460 Mass: `solutionVolume * aliquotFactor * ppmFluoride`
-   1460 Final: `(molarVolume * fluorideMass) / (molarMass * airVolume)`

Air Volume is auto-calculated from OIS sampling information.

## QCSM recovery

`1280 F/T` uses `CALC_QC_CONC` and calculation variables `found`,
`theory`, and `theoryElement`.

As of run 485926, `1460 F/T` has blank source code and no calculation
variables. Investigate before changing.

## Manual workflow

Use `Batch -> Enter Results` for manual analytical entry. Enter primary
measured/preparation values and let LabWare calculate derived Mass/Final
results.

`Batch -> Batch Process Results` is for later instrument-file
integration.

Ready-for-Review validation enforces required analytical results.

## 2026-09-25 configuration investigation

`F/T` is a unitless Found/Theoretical recovery ratio. `CALC_QC_CONC`
uses the configured Final result as `found` and the matching INGREDIENTS
Concentration as `theory`, converting found into compatible theory units
before division.

Confirmed `1280 F/T` mappings:

- `found` -> `ISE : 1280 (Final)` / `ENTRY`, scope `CT`, trigger `1`
- `theory` -> `INGREDIENTS : Concentration` / `ENTRY`, scope `AR`
- `theoryElement` -> `INGREDIENTS : Concentration` / `ATTRIBUTE_1`, scope `AR`

Historical environment `OLD_DEV_050526` contained QCSM-specific branching
in `1280 (Final)`: QCSMs retained `fluorideMass` in `UG`, while other
samples used `fluorideMass / airVolume`. This branch was restored to
current DEV on 2026-09-25 and is pending regression validation.

Analogous QCSM branching was newly added to `1460 (Final)` on 2026-09-25:
QCSMs retain `fluorideMass` in `UG`; non-QCSM samples retain the existing
PPM calculation. This was not found in `OLD_DEV_050526` and is a new DEV
change pending validation.

ISE Air Volume was also changed to call shared `CALC_AIR_VOL`, used by GC
and IC. For active samples the shared routine retains the existing
flow-rate-times-time behavior; regression validation is required.

The last confirmed state of `1460 F/T` remains blank source code with no
calculation variables. Recheck its current configuration and demonstrate
recovery behavior before considering 1460 QCSM recovery resolved.
