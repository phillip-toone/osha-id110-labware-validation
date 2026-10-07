# OSHA ID-110 / HD-2026-12-02 Analytical Values Extraction Results

**Prepared:** 2026-10-07  
**Scope:** Historical/reference evidence extraction for LabWare DEV regression request 485928 / batch `OSHA_ID-110-261007-1` / ISE / OSHA_ID-110.  
**Rule applied:** No analytical input below was reverse-engineered from a desired Final or recovery. Values are transcribed from the supplied historical packet. Where the packet does not establish whether a LabWare component is manually entered, that limitation is stated.

## 1. Executive summary

The packet is sufficient to reconstruct the **historical analytical measurement dataset** for the seven field samples and the first 10/20/40/0-uL QC set for both 1280 (particulate fluoride) and 1460 (HF as F-): adjusted pH, solution volume, aliquot factor, E0, EF-, measured concentration, calculated ug F-, and QC Found/Theoretical ratios are all present.

For the immediate 10-uL QCSM entry, the historical analogs are **QC131052 for 1280** and **QC131268 for 1460**. The historical 1280 10-uL QC has pH **7.644**, solution volume **50 mL**, aliquot factor **1**, E0 **73.0 mV**, EF- **27.0 mV**, concentration **1.92 mg/L**, and calculated/found fluoride **96 ug**. The historical 1460 10-uL QC has pH **7.321**, solution volume **50 mL**, aliquot factor **1**, E0 **70.6 mV**, EF- **22.8 mV**, concentration **1.76 mg/L**, and calculated/found fluoride **88 ug**.

The historical QC preparation records establish a repeating 10/20/40/0-uL sequence. For 1280 the historical spike masses are 90.23681, 180.47362, 360.94724, and 0 ug; for 1460 they are 90.41912, 180.83824, 361.67648, and 0 ug. These historical theoretical amounts differ slightly from the fresh regression theoretical amounts (90.262476717, 180.52495343, 361.04990687, 0 ug), so the mapping below is by **spike volume and sequence**, not by equality of theoretical mass.

The packet directly supports the analytical values but does **not** itself prove the LabWare component configuration (manual versus calculated). The handoff independently establishes that `1460 Concentration` is manually entered. For the remaining source measurements (pH, E0, EF-, concentration, solution volume, aliquot factor), this document labels them **MANUAL-CANDIDATE / SOURCE MEASUREMENT** where they are values an analyst would need to reproduce the historical analytical record, but does not claim LabWare manual-entry status beyond the evidence. `ug F-`/Mass, Final/reporting result, and Found/Theoretical are treated as calculated/reference checks.

## 2. Source inventory

| File | Pages | Contribution |
| --- | ---: | --- |
| `01 Analysis Worksheet.pdf` | 1-2 | Historical field sample IDs F58027-F58034, SAMPLE 1-7 mapping, air volumes, media, and analyte applicability. |
| `02 Quality Control Data Sheet 1460.pdf` | 1 | 1460 QC Found, Theoretical, Found/Theor and control status for QC131268-QC131271. |
| `03 Quality Control Data Sheet 1280.pdf` | 1 | 1280 QC Found, Theoretical, Found/Theor and control status for QC131052-QC131055. |
| `04 Summary.pdf` | 1 | Notes the 1280 Found/Theoretical ratios 1.064, 1.014, 0.997 and discusses the high QC131052 recovery/precision observation; no analytical entry values added. |
| `05 QC Analysis Worksheet 1280.pdf` | 1 | QC131052-QC131055, 50-mL solution volume annotation, 1280 identity. |
| `06 QC Analysis Worksheet 1460.pdf` | 1 | QC131268-QC131271, 50-mL solution volume annotation, 1460 identity. |
| `07 Traceability for Fluoride Analysis by Ion Selective Electrode.pdf` | 1 | Reagent/equipment traceability and annual RL verification context; not needed for sample analytical entry. |
| `08 pH Adjustment for Fluorides by Ion Selective Electrode.pdf` | 1-2 | Adjusted pH values for all relevant QCs and F58027-F58034 for both 1280 and 1460. |
| `09 Results of Fluoride Analysis by Ion Selective Electrode.pdf` | 1 | 50-mL sample volume, E0, EF-, and concentration for all relevant QCs and field sample/analyte pairs. |
| `10 Fluoride Stock Standard.pdf` | 1 | Standard preparation context; not used to infer sample entries. |
| `11 Fluoride Stock ICV.pdf` | 1 | ICV preparation context; not used to infer sample entries. |
| `12 Reporting Limits.pdf` | 1 | 1280/1460 reporting-limit context; not needed for analytical entry. |
| `13 IDE Sample Calculation Worksheet 1280.pdf` | 1 | 1280 solution volume 50 mL, aliquot factor 1, concentration, calculated ug F-, air volume, and mg/m3. |
| `131048- 131063 Particulate Fluorides-1280 KR.pdf` | 1-9 | Historical 1280 QCSM preparation: 10/20/40/0-uL repeating sequence and spike masses based on 9.023681 ug/uL stock. |
| `131268- 131279 HF (as F-) KR.pdf` | 1-9 | Historical 1460 QCSM preparation: 10/20/40/0-uL repeating sequence and spike masses based on 9.041912 ug/uL stock. |
| `14 IDE Sample Calculation Worksheet 1460.pdf` | 1 | 1460 solution volume 50 mL, aliquot factor 1, concentration, calculated ug F-, air volume, and mg/m3. |

## 3. Classification used in this handoff

- **MANUAL INPUT (established):** `1460 Concentration`, because the primary validation work independently established this LabWare behavior.
- **MANUAL-CANDIDATE / SOURCE MEASUREMENT:** pH, Solution Volume, Aliquot Factor, E0, EF-, and 1280 Concentration. The packet gives direct historical values, but these PDFs do not by themselves demonstrate the current LabWare component's entry/calculation setting.
- **CALCULATED OUTPUT / REFERENCE CHECK:** ug F- (Mass), Final/reporting concentration, and Found/Theoretical (F/T). These should not be populated merely because they appear in the historical packet.
- **Air Volume:** historical sample metadata/input, independently known in the handoff and also present in the worksheets. QC air volume is blank/not applicable.

## 4. Historical QC manual/source-measurement dataset

### 4.1 Particulate fluoride / 1280

| Historical QC | Spike | pH | Solution Vol. (mL) | Aliquot Factor | E0 (mV) | EF- (mV) | Concentration (mg/L) | Calculated/Found ug F- | Historical Theoretical ug | F/T |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| QC131052 | 10 uL | 7.644 | 50 | 1 | 73.0 | 27.0 | 1.92 | 96 | 90.237 (QC sheet) | 1.064 |
| QC131053 | 20 uL | 7.435 | 50 | 1 | 56.1 | 23.0 | 3.66 | 183 | 180.474 | 1.014 |
| QC131054 | 40 uL | 7.062 | 50 | 1 | 38.8 | 17.0 | 7.20 | 360 | 360.947 | 0.997 |
| QC131055 | 0 uL | 7.118 | 50 | 1 | 135.5 | 31.3 | 0.164 | 8.2 | 0 / blank QC | N/A |

**Provenance:** pH: `08 pH Adjustment...`, p.1. E0/EF-/concentration: `09 Results...`, p.1. Solution volume and aliquot factor/calculated ug: `13 IDE Sample Calculation Worksheet 1280.pdf`, p.1. Found/Theoretical/F/T: `03 Quality Control Data Sheet 1280.pdf`, p.1. Spike-volume sequence and precise preparation masses: `131048- 131063 Particulate Fluorides-1280 KR.pdf`, pp.5-7 (especially p.7).

**Important precision note:** the QC data sheet rounds the theoretical values to 90.237, 180.474, and 360.947 ug. The QCSM preparation packet shows the more precise preparation values **90.23681, 180.47362, and 360.94724 ug**.

### 4.2 HF / 1460

| Historical QC | Spike | pH | Solution Vol. (mL) | Aliquot Factor | E0 (mV) | EF- (mV) | Concentration (mg/L) | Calculated/Found ug F- | Historical Theoretical ug | F/T |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| QC131268 | 10 uL | 7.321 | 50 | 1 | 70.6 | 22.8 | 1.76 | 88 | 90.419 | 0.973 |
| QC131269 | 20 uL | 7.484 | 50 | 1 | 54.1 | 19.3 | 3.34 | 167 | 180.838 | 0.924 |
| QC131270 | 40 uL | 7.747 | 50 | 1 | 36.0 | 13.4 | 6.82 | 341 | 361.677 | 0.943 |
| QC131271 | 0 uL | 7.451 | 50 | 1 | 133.7 | 27.0 | 0.148 | 7.4 | 0 / blank QC | N/A |

**Provenance:** pH: `08 pH Adjustment...`, p.1. E0/EF-/concentration: `09 Results...`, p.1. Solution volume and aliquot factor/calculated ug: `14 IDE Sample Calculation Worksheet 1460.pdf`, p.1. Found/Theoretical/F/T: `02 Quality Control Data Sheet 1460.pdf`, p.1. Spike-volume sequence and precise preparation masses: `131268- 131279 HF (as F-) KR.pdf`, pp.5-7 (especially p.7).

**Important precision note:** the QC data sheet rounds the theoretical values to 90.419, 180.838, and 361.677 ug. The QCSM preparation packet shows **90.41912, 180.83824, and 361.67648 ug**.

## 5. Historical field-sample source-measurement dataset

All seven historical field samples use **50 mL solution volume** and **aliquot factor 1** on both 1280 and 1460 calculation worksheets.

| Historical sample | Field ID | Air Vol. (L) | 1280 pH | 1280 E0 | 1280 EF- | 1280 Conc. mg/L | 1280 ug F- | 1460 pH | 1460 E0 | 1460 EF- | 1460 Conc. mg/L | 1460 ug F- |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| F58027 | SAMPLE 1 | 79.04 | 7.062 | 138.2 | 31.3 | 0.147 | 7.35 | 7.603 | 137.1 | 27.0 | 0.130 | 6.5 |
| F58028 | SAMPLE 2 | 83.6 | 7.706 | 136.2 | 31.5 | 0.161 | 8.05 | 7.512 | 139.5 | 27.0 | 0.118 | 5.9 |
| F58029 | SAMPLE 3 | 88.16 | 7.368 | 133.2 | 31.3 | 0.180 | 9.0 | 7.742 | 139.7 | 27.1 | 0.117 | 5.85 |
| F58030 | SAMPLE 4 | 98.8 | 7.761 | 136.7 | 31.5 | 0.158 | 7.9 | 6.986 | 139.3 | 27.3 | 0.120 | 6.0 |
| F58031 | SAMPLE 5 | 91.2 | 7.829 | 135.5 | 31.4 | 0.165 | 8.25 | 7.507 | 138.3 | 27.2 | 0.124 | 6.2 |
| F58032 | SAMPLE 6 | 88.16 | 7.226 | 136.2 | 31.2 | 0.158 | 7.9 | 7.697 | 138.9 | 27.2 | 0.121 | 6.05 |
| F58033 | SAMPLE 7 | 98.8 | 7.398 | 136.0 | 31.6 | 0.163 | 8.15 | 7.646 | 140.8 | 27.2 | 0.113 | 5.65 |
| F58034 | BLANK | N/A | 7.535 | 135.9 | 31.4 | 0.162 | 8.1 | 7.085 | 139.4 | 27.2 | 0.119 | 5.95 |

**Provenance:** sample identity/air volume: `01 Analysis Worksheet.pdf`, pp.1-2; pH: `08 pH Adjustment...`, pp.1-2; E0/EF-/concentration: `09 Results...`, p.1; solution volume/aliquot factor/ug F- and air volumes: `13 IDE Sample Calculation Worksheet 1280.pdf`, p.1 and `14 IDE Sample Calculation Worksheet 1460.pdf`, p.1.

### Calculated/reference reporting checks for field samples

The 1280 calculation worksheet reports these mg/m3 values: SAMPLE 1 0.092990891; SAMPLE 2 0.096291866; SAMPLE 3 0.102087114; SAMPLE 4 0.079959514; SAMPLE 5 0.090460526; SAMPLE 6 0.0896098; SAMPLE 7 0.082489879. The 1460 worksheet reports: SAMPLE 1 0.082236842; SAMPLE 2 0.070574163; SAMPLE 3 0.066356624; SAMPLE 4 0.060728745; SAMPLE 5 0.067982456; SAMPLE 6 0.068625227; SAMPLE 7 0.057186235. Treat these as **reference/calculated outputs**, not manual analytical inputs.

## 6. Mapping to fresh request 485928

### 6.1 QCSM mapping

| New sample | New Text ID | Spike | Historical analog | 1280 source measurements to reproduce | 1460 source measurements to reproduce | Expected historical checks | Primary source |
| ---: | --- | ---: | --- | --- | --- | --- | --- |
| 66052 | QCSM035-0006-001 | 10 uL | QC131052 | pH 7.644; SolVol 50; AF 1; E0 73.0; EF- 27.0; Conc 1.92 | N/A | Mass/Found 96 ug; historical F/T 1.064 | 08 p.1; 09 p.1; 13 p.1; 03 p.1; 1280 KR pp.5-7 |
| 66053 | QCSM035-0006-002 | 20 uL | QC131053 | pH 7.435; SolVol 50; AF 1; E0 56.1; EF- 23.0; Conc 3.66 | N/A | 183 ug; historical F/T 1.014 | same 1280 sources |
| 66054 | QCSM035-0006-003 | 40 uL | QC131054 | pH 7.062; SolVol 50; AF 1; E0 38.8; EF- 17.0; Conc 7.20 | N/A | 360 ug; historical F/T 0.997 | same 1280 sources |
| 66055 | QCSM035-0006-004 | 0 uL | QC131055 | pH 7.118; SolVol 50; AF 1; E0 135.5; EF- 31.3; Conc 0.164 | N/A | 8.2 ug; blank-QC Found 0.000 on QC sheet (see discrepancy note) | same 1280 sources |
| 66058 | QCSM036-0004-001 | 10 uL | QC131268 | N/A | pH 7.321; SolVol 50; AF 1; E0 70.6; EF- 22.8; **Conc 1.76** | Mass/Found 88 ug; historical F/T 0.973 | 08 p.1; 09 p.1; 14 p.1; 02 p.1; 1460 KR pp.5-7 |
| 66059 | QCSM036-0004-002 | 20 uL | QC131269 | N/A | pH 7.484; SolVol 50; AF 1; E0 54.1; EF- 19.3; **Conc 3.34** | 167 ug; historical F/T 0.924 | same 1460 sources |
| 66060 | QCSM036-0004-003 | 40 uL | QC131270 | N/A | pH 7.747; SolVol 50; AF 1; E0 36.0; EF- 13.4; **Conc 6.82** | 341 ug; historical F/T 0.943 | same 1460 sources |
| 66061 | QCSM036-0004-004 | 0 uL | QC131271 | N/A | pH 7.451; SolVol 50; AF 1; E0 133.7; EF- 27.0; **Conc 0.148** | 7.4 ug; blank-QC Found 0.000 on QC sheet (see discrepancy note) | same 1460 sources |

**Mapping rationale:** the historical preparation screens/worksheets explicitly use the sequence 10, 20, 40, 0 uL in sets of four. Therefore the new 10/20/40/0-uL sets map directly by spike volume and ordinal position to QC131052-131055 (1280) and QC131268-131271 (1460). The two analytes use different historical standard concentrations and therefore different theoretical masses; they are not interchangeable.

### 6.2 Field-sample mapping

For every mapped field sample below, use solution volume **50 mL** and aliquot factor **1** for both analytes if those components are confirmed as manual in the current LabWare configuration.

| New sample | New Text ID | Historical source | Air Vol. L | 1280 source measurements (pH / E0 / EF- / Conc mg/L) | 1460 source measurements (pH / E0 / EF- / Conc mg/L) |
| ---: | --- | --- | ---: | --- | --- |
| 66045 | 485928-SAMPLE 1 | F58027 | 79.04 | 7.062 / 138.2 / 31.3 / 0.147 | 7.603 / 137.1 / 27.0 / 0.130 |
| 66046 | 485928-SAMPLE 2 | F58028 | 83.6 | 7.706 / 136.2 / 31.5 / 0.161 | 7.512 / 139.5 / 27.0 / 0.118 |
| 66047 | 485928-SAMPLE 3 | F58029 | 88.16 | 7.368 / 133.2 / 31.3 / 0.180 | 7.742 / 139.7 / 27.1 / 0.117 |
| 66048 | 485928-SAMPLE 4 | F58030 | 98.8 | 7.761 / 136.7 / 31.5 / 0.158 | 6.986 / 139.3 / 27.3 / 0.120 |
| 66049 | 485928-SAMPLE 5 | F58031 | 91.2 | 7.829 / 135.5 / 31.4 / 0.165 | 7.507 / 138.3 / 27.2 / 0.124 |
| 66050 | 485928-SAMPLE 6 | F58032 | 88.16 | 7.226 / 136.2 / 31.2 / 0.158 | 7.697 / 138.9 / 27.2 / 0.121 |
| 66051 | 485928-SAMPLE 7 | F58033 | 98.8 | 7.398 / 136.0 / 31.6 / 0.163 | 7.646 / 140.8 / 27.2 / 0.113 |

## 7. Immediate entry guidance for sample 66052

For **66052 / QCSM035-0006-001 / 10 uL / 1280**, the historical source record supports the following values:

| LabWare component | Historical value | Classification for this handoff |
| --- | ---: | --- |
| 1280 pH | 7.644 | MANUAL-CANDIDATE / SOURCE MEASUREMENT |
| 1280 Solution Volume | 50 mL | MANUAL-CANDIDATE / SOURCE/PREP VALUE |
| 1280 Aliquot Factor | 1 | MANUAL-CANDIDATE / SOURCE/CALC-WORKSHEET VALUE |
| 1280 E0 | 73.0 mV | MANUAL-CANDIDATE / SOURCE MEASUREMENT |
| 1280 EF- | 27.0 mV | MANUAL-CANDIDATE / SOURCE MEASUREMENT |
| 1280 Concentration | 1.92 mg/L | MANUAL-CANDIDATE / SOURCE MEASUREMENT |
| 1280 Mass / ug F- | 96 ug | **CALCULATED/REFERENCE - do not type solely from this packet** |
| 1280 Final | 96.0 ug (historical QC Found) | **REFERENCE CHECK** |
| 1280 F/T | 1.064 historical | **REFERENCE CHECK**; fresh theoretical mass differs slightly |

Because the fresh QCSM035 theoretical value is 90.262476717 ug rather than the historical 90.23681/90.237 ug, reproducing the historical **96 ug Found** would imply a fresh-run F/T of about **1.063565**, as already supplied in the primary handoff. That number is a cross-check only; no input in this document was derived from it.

## 8. Uncertainties, missing values, and discrepancies

1. **Manual-entry status is not fully documented by these PDFs.** They establish source measurements and calculation worksheets, not current LabWare component configuration. Only `1460 Concentration` is independently established by the primary validation work as manual. Before typing other values, use the current Result Entry behavior/configuration as the authority for which components accept analyst entry.
2. **Blank QC discrepancy:** the instrument/results and calculation worksheets show nonzero low-level measurements for QC131055 (1280: 0.164 mg/L -> 8.2 ug) and QC131271 (1460: 0.148 mg/L -> 7.4 ug), while the QC Data Sheets report the blank QCs as `Found: 0.000`. This is likely reporting/QC treatment rather than raw analytical concentration, but the packet does not explicitly explain the transformation. Do not replace the measured concentration with zero.
3. **Historical versus fresh theoretical QCSM mass:** historical 1280 and 1460 QCSMs were prepared from analyte-specific stock concentrations (9.023681 and 9.041912 ug/uL), whereas the fresh regression theoretical values are 9.0262476717 ug/uL equivalent. Therefore historical F/T ratios will not be numerically identical when the same historical measured Found value is compared with the fresh theoretical mass.
4. **F58034 is the historical blank**, not one of the seven field samples. It is retained above because it appears in the analytical run and may be useful as a blank reference, but it has no new 485928 field-sample mapping in the supplied handoff.
5. The source packet does not establish a separate historical value corresponding to a LabWare component named exactly `1280 Final` or `1460 Final` beyond the QC `Found` values and the sample calculation/reporting outputs. Those should be treated as checks rather than populated inputs.

## 9. Recommended entry order

The packet supports the analytical workflow sequence (pH adjustment, ISE result recording, calculation) but does **not** establish the exact required LabWare Result Entry order. Therefore no mandatory component-entry order is asserted. For regression purposes, enter only fields that the current LabWare UI/configuration identifies as editable/manual, using the source values above, and allow calculated fields to calculate before comparing them with the historical checks.

## 10. Provenance notes / quick verification index

- **Air volumes and SAMPLE 1-7 mapping:** `01 Analysis Worksheet.pdf`, pp.1-2.
- **1280 QC Found/Theoretical/F/T:** `03 Quality Control Data Sheet 1280.pdf`, p.1.
- **1460 QC Found/Theoretical/F/T:** `02 Quality Control Data Sheet 1460.pdf`, p.1.
- **QC 50-mL preparation annotations:** `05 QC Analysis Worksheet 1280.pdf`, p.1; `06 QC Analysis Worksheet 1460.pdf`, p.1.
- **All adjusted pH values:** `08 pH Adjustment for Fluorides by Ion Selective Electrode.pdf`, pp.1-2.
- **All E0, EF-, and measured concentrations:** `09 Results of Fluoride Analysis by Ion Selective Electrode.pdf`, p.1.
- **1280 solution volume, aliquot factor, calculated ug F-, air volume, mg/m3:** `13 IDE Sample Calculation Worksheet 1280.pdf`, p.1.
- **1460 solution volume, aliquot factor, calculated ug F-, air volume, mg/m3:** `14 IDE Sample Calculation Worksheet 1460.pdf`, p.1.
- **1280 QCSM 10/20/40/0-uL sequence and precise spike masses:** `131048- 131063 Particulate Fluorides-1280 KR.pdf`, pp.5-7.
- **1460 QCSM 10/20/40/0-uL sequence and precise spike masses:** `131268- 131279 HF (as F-) KR.pdf`, pp.5-7.

## Bottom line for the primary validation session

For **sample 66052**, the historical 10-uL 1280 analog is **QC131052**. The evidence-supported analytical record is **pH 7.644; solution volume 50 mL; aliquot factor 1; E0 73.0 mV; EF- 27.0 mV; concentration 1.92 mg/L**. Historical calculated/found fluoride is **96 ug**. Enter only those source values whose corresponding LabWare components are actually editable/manual; do **not** manually force Mass, Final, or F/T to the historical result.
