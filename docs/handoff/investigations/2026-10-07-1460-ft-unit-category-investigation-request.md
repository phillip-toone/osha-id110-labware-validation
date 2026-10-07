# OSHA ID-110 LabWare Validation — 1460 F/T Unit-Category Investigation Handoff

**Prepared:** 2026-10-07  
**Environment:** OSHA_LIMS_DEV / LabWare 8  
**Primary tracking:** Jira `OSHA_LABS-3609`  
**Current request:** `485928`  
**Current batch:** `OSHA_ID-110-261007-1`  
**Purpose:** Bounded technical investigation of the remaining QCSM recovery problem, with emphasis on why fresh-runtime `1280 F/T` now calculates correctly while `1460 F/T` reports a theoretical-versus-actual unit-category mismatch.

## 1. Assignment to the investigating LLM

Investigate the current `1460 F/T` runtime failure far enough to identify the most likely root cause and the smallest scientifically/configurationally correct fix or next diagnostic step.

Do **not** assume that shared routine `CALC_QC_CONC` is defective. Do **not** recommend changing shared code merely to suppress the error. Trace the configuration and runtime data feeding the routine, compare the working 1280 path with the failing 1460 path, and identify the first meaningful difference.

The primary validation chat will retain ownership of the live regression. Your role is investigation only. Return your findings in a Markdown handoff document as described in Section 12.

## 2. Current regression state

A fresh post-fix regression request `485928` was imported and received. Seven field samples were created:

| Sample | Text ID |
| ---: | --- |
| 66045 | 485928-SAMPLE 1 |
| 66046 | 485928-SAMPLE 2 |
| 66047 | 485928-SAMPLE 3 |
| 66048 | 485928-SAMPLE 4 |
| 66049 | 485928-SAMPLE 5 |
| 66050 | 485928-SAMPLE 6 |
| 66051 | 485928-SAMPLE 7 |

Two fresh four-member QCSM sets were then prepared **after** the recent configuration corrections:

### QCSM035 — particulate fluoride / 1280

`QCSM035-0006`

| Sample | Text ID | Spike | Fresh theoretical fluoride |
| ---: | --- | ---: | ---: |
| 66052 | QCSM035-0006-001 | 10 uL | 90.262476717 ug |
| 66053 | QCSM035-0006-002 | 20 uL | 180.52495343 ug |
| 66054 | QCSM035-0006-003 | 40 uL | 361.04990687 ug |
| 66055 | QCSM035-0006-004 | 0 uL | 0.0 ug |

Preparation used:

- spiking solution `LAB_SOL00265-0006-001`
- physical media `FES0000028-0005-011`

### QCSM036 — HF / 1460

`QCSM036-0004`

| Sample | Text ID | Spike | Fresh theoretical fluoride |
| ---: | --- | ---: | ---: |
| 66058 | QCSM036-0004-001 | 10 uL | 90.262476717 ug |
| 66059 | QCSM036-0004-002 | 20 uL | 180.52495343 ug |
| 66060 | QCSM036-0004-003 | 40 uL | 361.04990687 ug |
| 66061 | QCSM036-0004-004 | 0 uL | 0.0 ug |

Preparation used:

- spiking solution `LAB_SOL00265-0006-001`
- physical media `FES0002281-0005-001`

All eight QCSMs were activated successfully.

LabWare accepted **both QCSM035 and QCSM036 simultaneously** through the normal `Scan Quality Control Samples to Add to Batch` workflow. The new batch therefore contains 15 samples: four QCSM035, four QCSM036, and seven field samples.

## 3. Prior defects already identified and corrected

Do not rediscover these from scratch unless needed to understand the new failure.

### 3.1 Theoretical target / alias mismatch

`CALC_QC_CONC` uses the recovery component alias to select the matching theoretical `INGREDIENTS : Concentration` result through `RESULT.ATTRIBUTE_1`.

Pre-change state:

```text
1280 F/T.ALIAS_NAME = Fluoride (F)
1460 F/T.ALIAS_NAME = NULL
QCSM theoretical Concentration.ATTRIBUTE_1 = Sodium Fluoride
```

Corrections implemented before request 485928 was created:

```text
LAB_SOL00265.X_QCSM_TARGET = Fluoride (F)
1460 F/T.ALIAS_NAME        = Fluoride (F)
```

`1280 F/T` already had `ALIAS_NAME = Fluoride (F)`.

No change was made to shared `CALC_QC_CONC`.

### 3.2 Missing 1460 F/T calculation-variable inputs

`1460 F/T` calls:

```text
GOSUB CALC_QC_CONC
RETURN qcRecVal
```

The following Component inputs were added so it parallels working `1280 F/T`:

| Variable | Component source | Value | Output |
| --- | --- | --- | --- |
| `found` | `ISE : 1460 (Final)` | ENTRY | Array |
| `theory` | `INGREDIENTS : Concentration` | ENTRY | Array |
| `theoryElement` | `INGREDIENTS : Concentration` | ATTRIBUTE_1 | Array |

### 3.3 Batch precision wrapper targets

Incorrect copied targets were corrected:

```text
F (1280) QCSM Precision:
    targetElement = "1280 F/T"
    GOSUB CALC_BATCH_QC_REC_PRECISION

HF (1460) QCSM Precision:
    targetElement = "1460 F/T"
    GOSUB CALC_BATCH_QC_REC_PRECISION
```

No change was made to shared `CALC_BATCH_QC_REC_PRECISION`.

## 4. Relevant Final calculation behavior

Earlier investigation established that the Concentration -> Mass -> `(Final)` chain itself was calculating correctly.

The `1460 (Final)` calculation currently contains QCSM handling analogous to the 1280 path. The known source is:

```text
' PTOONE 25-Sep-2026
' Added QCSM handling during OSHA ID-110 validation.
' QCSM Final retains fluoride mass in UG for F/T recovery calculation.
' Non-QCSM samples retain the existing HF PPM calculation.

molarVolume = 24.45'l/mole
molarMass = 18.99840316'gram/mole
sampleType = SELECT SAMPLE.SAMPLE_TYPE
sampleNumber = SELECT SAMPLE.SAMPLE_NUMBER
testNumber = SELECT TEST.TEST_NUMBER
resultName = SELECT RESULT.NAME
IF (sampleType = "QCSM") THEN
    resultValue = fluorideMass
    status = SetOrCreateResult(sampleNumber, testNumber, resultName, resultValue, "UG", , , "F", , "F", , , )
    RETURN resultValue
ELSE
    RETURN (molarVolume*fluorideMass)/(molarMass*airVolume)
ENDIF
```

The 1280 Final path has analogous QCSM handling and historically/now returns QCSM mass rather than airborne concentration.

Important question for this investigation: distinguish between the value returned by the component calculation and the units/metadata actually stored on the result record by `SetOrCreateResult` and/or by LabWare's normal component evaluation. Do not assume the displayed Units column tells the whole story.

## 5. `CALC_QC_CONC` working model

The earlier investigation established the essential intended calculation:

```text
qcRecVal = Val(foundConvertedtoTheoryUnitsAmt) / Val(theory[i])
```

The routine receives:

- `found` — array sourced from the analyte `(Final)` component
- `theory` — array sourced from `INGREDIENTS : Concentration` ENTRY
- `theoryElement` — array sourced from `INGREDIENTS : Concentration` ATTRIBUTE_1

It locates the theoretical result matching the recovery component alias and reconciles actual/theoretical units before dividing.

The exact runtime message now observed is:

```text
Theoretical UNITS and actual results UNITS are not same category.
```

Locate the branch in `CALC_QC_CONC` that emits this message and determine exactly what values/units/categories cause it.

## 6. Fresh runtime evidence — sample 66052

The primary chat used the historical packet values on fresh sample `66052 / QCSM035-0006-001`.

### 6.1 1280 source values entered

Historical analog: `QC131052`.

```text
1280 pH              = 7.644
1280 Solution Volume = 50 mL
1280 Aliquot Factor  = 1
1280 E0              = 73.0 mV
1280 EF-             = 27.0 mV
1280 Concentration   = 1.92 ppm (historical source 1.92 mg/L)
```

LabWare calculated:

```text
1280 Mass    = 96.0 ug
1280 (Final) = 96.0000
```

On the first calculation attempt, `1280 F/T` displayed:

```text
Theoretical UNITS and actual results UNITS are not same category.
```

After subsequent result entry/recalculation on the same test, `1280 F/T` changed to:

```text
1.064
```

This agrees with the expected fresh-run ratio:

```text
96 / 90.262476717 = approximately 1.063565
```

Thus **1280 F/T is now demonstrably capable of calculating correctly at runtime on a fresh post-fix QCSM.**

The latest Result Entry screen showed:

```text
1280 Mass       96.0      ug       Status Entered
1280 (Final)    96.0000            Status Modified
1280 F/T        1.064              Status Entered
```

The visible Units cell for `1280 (Final)` appeared blank, while Mass displayed `ug`. Do not infer stored result units solely from this visual blank because F/T nevertheless eventually calculated successfully.

## 7. Fresh runtime evidence — 1460 path on sample 66052

For a quick controlled comparison, the historical 10-uL 1460 analog values were also entered on sample 66052:

```text
1460 pH              = 7.321
1460 Solution Volume = 50 mL
1460 Aliquot Factor  = 1
1460 E0              = 70.6 mV
1460 EF-             = 22.8 mV
1460 Concentration   = 1.76 ppm (historical source 1.76 mg/L)
```

LabWare calculated:

```text
1460 Mass    = 88.0 ug
1460 (Final) = 88.0000
```

But `1460 F/T` still displayed:

```text
Theoretical UNITS and actual results UNITS are not same category.
```

The expected fresh-run recovery, if the same theoretical 90.262476717 ug is found and unit conversion succeeds, is approximately:

```text
88 / 90.262476717 = 0.974934
```

This cross-analyte entry on sample 66052 was used only as a diagnostic comparison. The scientifically mapped fresh 1460 QCSM is sample 66058; do not confuse the diagnostic 66052 1460 entry with the final regression mapping.

## 8. Historical analytical source dataset available

A separate extraction session reviewed the HD-2026-12-02 PDFs without reverse-engineering inputs from desired outputs.

For the proper 1460 10-uL fresh QCSM `66058 / QCSM036-0004-001`, historical analog `QC131268` is:

```text
1460 pH              = 7.321
1460 Solution Volume = 50 mL
1460 Aliquot Factor  = 1
1460 E0              = 70.6 mV
1460 EF-             = 22.8 mV
1460 Concentration   = 1.76 mg/L / numerically 1.76 ppm in LabWare
Historical found     = 88 ug
```

Fresh theoretical value is 90.262476717 ug, so expected fresh F/T is approximately 0.974934.

The primary chat has the complete extraction results if additional historical values are needed.

## 9. Investigation priorities

Please investigate in this order unless evidence strongly suggests a better route.

### Priority A — compare 1280 F/T and 1460 F/T configuration side by side

Compare all relevant properties, not only source code:

- calculation source
- calculation-variable definitions
- variable type
- source analysis/component
- trigger
- scope
- value field (`ENTRY`, `ATTRIBUTE_1`, etc.)
- array flag
- `ALIAS_NAME`
- result/component unit configuration
- unit category / dimension / conversion metadata
- result type / datatype
- any hidden or advanced properties that differ

Identify every difference and classify it as expected versus suspicious.

### Priority B — inspect 1280 Final versus 1460 Final result/unit configuration

Compare:

- component configured units
- calculation return behavior
- `SetOrCreateResult` call behavior
- stored result units after calculation if accessible
- whether returning a scalar after `SetOrCreateResult` causes LabWare to overwrite or preserve unit metadata
- why 1280 can eventually calculate F/T despite a visually blank Final Units cell while 1460 cannot

### Priority C — inspect the theoretical `INGREDIENTS : Concentration` results

For fresh QCSMs, establish if possible:

- ENTRY value
- stored unit and unit category
- `ATTRIBUTE_1`
- whether `ATTRIBUTE_1` is now actually `Fluoride (F)` after the `X_QCSM_TARGET` correction
- whether QCSM035 and QCSM036 theoretical results differ in unit metadata despite equal displayed numerical values

### Priority D — trace the unit-category error inside `CALC_QC_CONC`

Find the exact logic that emits:

```text
Theoretical UNITS and actual results UNITS are not same category.
```

Document:

1. what unit/category is considered "theoretical";
2. what unit/category is considered "actual";
3. where those values come from;
4. what comparison fails for 1460;
5. why the same comparison ultimately succeeds for 1280;
6. whether the failure is configuration, result metadata, evaluation order, or another cause.

### Priority E — evaluate recalculation/order behavior

Because 1280 initially produced the same unit-category error and later recalculated successfully to `1.064`, determine whether calculation order, trigger timing, stale arrays, or result recreation can explain the transient 1280 failure and persistent 1460 failure.

Do not assume this is merely a refresh issue. Establish evidence.

## 10. Guardrails

- Do **not** change production/shared code during the investigation unless the primary chat explicitly decides to do so later.
- Do **not** recommend altering historical source values to make recovery pass.
- Do **not** manually populate Mass, Final, or F/T as a workaround.
- Do **not** conflate the diagnostic 1460 values entered on sample 66052 with the scientifically mapped QCSM036 sample 66058.
- Preserve the fresh `485928` regression as evidence.
- Prefer comparison with known-good LabWare configurations/routines where useful.
- Clearly distinguish observed facts, configuration evidence, inference, and hypotheses.
- If database queries are useful, provide read-only SQL first. Any proposed update SQL must be clearly separated and must not be executed merely for diagnosis.

## 11. Materials to request/use

Ask the user for screenshots, exported configuration, subroutine source, SQL query results, or repository files as needed. In particular, likely useful artifacts include:

- full `CALC_QC_CONC` source
- Component Calculations screenshots/exports for `1280 F/T` and `1460 F/T`
- Component Calculations screenshots/exports for `1280 (Final)` and `1460 (Final)`
- component/result properties showing units for the above
- fresh result records for sample 66052 and, if later entered, sample 66058
- `INGREDIENTS : Concentration` result records for fresh QCSM samples
- prior investigation document `docs/findings/2026-10-06-qc-recovery-and-precision-investigation.md`
- prior investigation handoff `docs/handoff/investigations/2026-10-06-calc-qc-conc-investigation.md`
- current `docs/handoff/CURRENT.md`

If the same LLM session that performed the prior `CALC_QC_CONC` investigation receives this handoff, reuse its established understanding but verify claims against current evidence rather than relying solely on conversational memory.

## 12. Required return handoff

At the end of the investigation, create a Markdown file named approximately:

```text
OSHA_ID110_1460_FT_unit_category_investigation_results.md
```

The return document must contain:

1. **Executive conclusion** — concise statement of the most likely root cause and confidence level.
2. **Evidence reviewed** — exact components, subroutines, screenshots, SQL results, files, etc.
3. **Working 1280 path** — explain why it now produces `1.064`.
4. **Failing 1460 path** — explain exactly where and why it diverges.
5. **Unit/category trace** — table showing theoretical and actual units/categories and their provenance for both 1280 and 1460.
6. **Configuration comparison** — side-by-side 1280 F/T versus 1460 F/T and 1280 Final versus 1460 Final.
7. **Role of recalculation/order** — explain the initial 1280 error followed by successful recalculation if determinable.
8. **Recommended correction or next diagnostic** — smallest defensible action, with exact configuration field/code/SQL only if supported.
9. **Regression instructions** — what the primary chat should do on samples 66052 and 66058 to prove the issue resolved.
10. **Remaining uncertainties** — anything not established.
11. **Repository-ready finding summary** — a concise section suitable for incorporation into the main project's findings/handoff documentation.

Do not claim resolution merely because a plausible configuration difference is found. State what still requires runtime proof.

## 13. Success criterion

The investigation is successful when the primary validation chat can answer, with evidence:

> Why does fresh QCSM `1280 F/T` now calculate correctly as approximately Final/Theoretical while `1460 F/T` reports "Theoretical UNITS and actual results UNITS are not same category," and what is the smallest correct change or next diagnostic needed to make the 1460 recovery path scientifically and configurationally correct?

