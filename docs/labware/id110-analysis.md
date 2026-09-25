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
