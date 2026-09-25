# Findings --- ISE Configuration Investigation --- 2026-09-25

## Purpose

Following validation run 485926, configuration was investigated read-only before making controlled DEV changes. The investigation focused on the QCSM recovery path, comparison with established GC behavior, and comparison of current DEV with historical environment `OLD_DEV_050526`.

## Confirmed configuration findings

### Recovery / F-T

`F/T` is a unitless Found/Theoretical recovery ratio. The shared subroutine `CALC_QC_CONC` calculates recovery using a found result and the theoretical INGREDIENTS concentration, converting found into compatible theory units before division.

For `ISE / 1280 F/T`, the confirmed configuration is:

- `found` -> `ISE : 1280 (Final)` / `ENTRY`, scope `CT`, calculation trigger `1`
- `theory` -> `INGREDIENTS : Concentration` / `ENTRY`, scope `AR`
- `theoryElement` -> `INGREDIENTS : Concentration` / `ATTRIBUTE_1`, scope `AR`
- source code calls `CALC_QC_CONC` and returns `qcRecVal`

An established GC example (`MeCl2 F/T`) uses the same three-variable pattern and the same `CALC_QC_CONC` subroutine. This confirms that the 1280 interface to the shared recovery routine follows an established LabWare pattern.

As observed during run 485926, `1460 F/T` had blank calculation source and no calculation variables. This must be rechecked after configuration work; no completed 1460 F/T configuration was captured in this investigation record.

### Final-result handling for QCSMs

Current DEV initially contained simple field-sample Final calculations:

- `1280 (Final)`: `fluorideMass / airVolume`
- `1460 (Final)`: `(molarVolume * fluorideMass) / (molarMass * airVolume)`

Historical environment `OLD_DEV_050526` contained additional QCSM branching in `1280 (Final)`:

- for `QCSM`, return `fluorideMass` and set the result units to `UG`;
- otherwise retain the field-sample `fluorideMass / airVolume` calculation.

No analogous historical branch was found for `1460 (Final)` in `OLD_DEV_050526`.

The historical 1280 design is consistent with established GC behavior. `CALC_GC_FINAL` branches by sample type and, for QCSM/RLSM, retains the calculated mass and sets the Final result units to `UG` rather than applying the ordinary airborne-result conversion.

This explains the intended QCSM data path: measured mass is carried through the Final component as `found`, then compared with the theoretical spiked amount by `CALC_QC_CONC`.

## DEV changes made during investigation

These changes are **pending regression validation** and must not yet be treated as validated fixes.

### ISE Air Volume

The older embedded ISE Air Volume calculation was replaced with a call to shared subroutine `CALC_AIR_VOL`:

```text
GOSUB CALC_AIR_VOL
RETURN volume
```

Evidence supporting the change:

- GC versions 2-4 use `CALC_AIR_VOL`.
- IC version 1 uses `CALC_AIR_VOL`.
- for ordinary active samples, the shared routine uses the same `flowRate * totalTime` calculation as the prior ISE code;
- the shared routine additionally supports passive media.

This is a configuration-standardization change, not a correction demonstrated by run 485926. Regression testing must confirm unchanged ID-110 field-sample Air Volumes.

### 1280 (Final)

The QCSM branch developed in `OLD_DEV_050526` was restored to current DEV. A provenance comment dated 25-Sep-2026 was added.

Intended behavior:

- QCSM -> retain fluoride mass in `UG` for F/T recovery;
- non-QCSM -> retain existing particulate fluoride `MG_M3` calculation.

This is a restoration of historical development configuration that appears to have been omitted when configuration was transferred to current DEV.

### 1460 (Final)

Analogous QCSM branching was newly added in current DEV. A provenance comment dated 25-Sep-2026 was added.

Intended behavior:

- QCSM -> retain fluoride mass in `UG` for F/T recovery;
- non-QCSM -> retain existing HF `PPM` calculation using molar volume, fluoride molar mass, and Air Volume.

Unlike 1280, this is not a restoration of code found in `OLD_DEV_050526`. It is a new DEV configuration change supported by the historical 1280 pattern, established GC QCSM behavior, and the divide-by-zero behavior observed for 1460 QCSMs in run 485926.

## Historical DEV comparison

Source-code files compared between current DEV and `OLD_DEV_050526`:

- Air Volume
- 1280 Mass
- 1280 Final
- 1280 F/T
- 1460 Mass
- 1460 Final
- 1460 F/T

The only source-code difference reported by the comparison was `1280 (Final)`, where current DEV lacked the historical QCSM branch described above. The other compared calculation sources were identical at the time of comparison.

Parent ISE Analysis Variation records were also compared and no substantive ID-110 differences were identified. Component Variation child configuration has not yet been exhaustively diffed.

## Next validation run

Recycle reference case `HD-2026-12-02` with a new request number and use the same historical analytical inputs. Include both QCSM sets:

- `QCSM035` for 1280
- `QCSM036` for 1460

Verify at minimum:

1. field-sample Air Volume values are unchanged after adoption of `CALC_AIR_VOL`;
2. 1280 QCSM Final retains mass in `UG` and no longer attempts an Air Volume division;
3. 1460 QCSM Final retains mass in `UG` and the run-485926 divide-by-zero behavior is eliminated;
4. non-QCSM 1280 Final remains `MG_M3` and reproduces reference values;
5. non-QCSM 1460 Final remains `PPM` and reproduces reference values;
6. 1280 F/T recovery calculates from found/theoretical values;
7. 1460 F/T configuration and recovery behavior are explicitly verified before claiming the 1460 QCSM recovery issue resolved;
8. QCSM precision results populate from the recovery results;
9. `Batch -> Calculate` behavior is observed separately and not conflated with the QCSM Final/recovery corrections.
