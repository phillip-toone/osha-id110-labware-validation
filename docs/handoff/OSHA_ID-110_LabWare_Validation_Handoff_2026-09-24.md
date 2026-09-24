# OSHA ID-110 LabWare Validation --- Comprehensive Handoff

**Current as of:** September 24, 2026, approximately 3:46 PM MDT\
**LabWare environment:** OSHA_LIMS_DEV / LabWare 8\
**Primary analyst/user:** PTOONE\
**Current batch:** `OSHA_ID-110-260924-1`\
**Jira tracking issue:** `OSHA_LABS-3609`

------------------------------------------------------------------------

## 1. Purpose of this handoff

This document is intended to allow another AI/LLM to continue the OSHA
ID-110 LabWare validation from the exact current state without requiring
access to the chat session that produced it.

The validation is testing LabWare as the replacement for the legacy LISA
workflow. The objective is not merely to reproduce LISA screens, but to
determine whether LabWare produces the correct analytical behavior,
calculations, QC behavior, traceability, and final results for OSHA
ID-110.

A key working principle throughout this validation has been:

> Do not "fix" unexpected LabWare behavior merely because it differs
> from expectations. Preserve the configured workflow, observe what it
> does, and document discrepancies. Unexpected behavior may be the
> defect the validation is intended to reveal.

The current validation run has progressed through standards/reagents,
QCSM preparation, XML sample import, barcode-based batch creation, and
batch-level equipment/reagent/standard traceability. The next logical
step is to continue the configured analytical workflow from the open
batch.

------------------------------------------------------------------------

## 2. Critical current state

### Current batch

**Batch ID:** `OSHA_ID-110-260924-1`

The batch was created on September 24, 2026 using:

**Batch Creation → ISE / OSHA_ID-110 → Create Batch by Barcode Scan**

The "barcode scanner" is simply keyboard input; sample numbers were
typed manually.

The batch is currently:

-   Type: `TEST`
-   Batch Link: `ISE`
-   Owner: `PTOONE`
-   Status: **Incomplete**
-   State: **Open**

### Immediate stopping point

The batch **Results** tab has been populated with the major equipment,
reagent, buffer, ICV, and calibration-standard traceability fields.

The last observed screen shows the following fields as **Entered**:

-   ISE Instrument Used
-   Diluter Used
-   Balance Used
-   5N NaOH
-   T-T Buffer Concentrated
-   T-T Buffer Analytical
-   Buffer pH 4
-   Buffer pH 7
-   Buffer pH 10
-   Hydrochloric Acid (HCl)
-   ICV Standard
-   STD A (RLV)
-   STD B
-   STD C (CCV)
-   STD D

The following visible fields remain **Not Entered**:

-   Batch Attachment --- optional
-   Method Modified --- optional/reportable
-   Modified Method Notes --- optional/reportable
-   MCE Filter Lot (for wiping cassettes) --- optional
-   `F (1280) QCSM Precision` --- reportable
-   `HF (1460) QCSM Precision` --- reportable

**Do not manually invent the QCSM Precision values.** They are expected
to arise later from the analytical/QC workflow.

The next recommended action is to inspect/open the **Run** menu and
continue the configured OSHA ID-110 batch workflow. Do not authorize the
batch Results at this point.

------------------------------------------------------------------------

## 3. Important unresolved observation: 1280 vs 1460

The XML request used for the new samples explicitly requests:

-   Substance code: `1460`
-   Substance name: `Hydrogen Fluoride (as F)`

However, after batch creation, expanding the seven imported field
samples in the batch tree showed:

`ISE [Ion Selective Electrode] / 1` → `1280 (Final)`

No visible `1460 (Final)` result was seen at that point.

This is **not yet being treated as a confirmed defect**.

The decision was made to continue the batch through the normal
configured workflow because:

1.  The XML was already imported successfully.
2.  The batch was created successfully.
3.  The configured batch Results contain both:
    -   `F (1280) QCSM Precision`
    -   `HF (1460) QCSM Precision`
4.  A later workflow step may create/derive the expected 1460 behavior.
5.  If it does not, continuing the configured process will provide
    stronger validation evidence of the actual defect.

**Do not manually add or change analyses yet.**

Document the observation and allow LabWare's configured workflow to
demonstrate the intended behavior.

------------------------------------------------------------------------

## 4. XML request used for the new validation samples

File:

`Request_485926.xml`

Important request metadata:

-   Request number: `485926`
-   Reporting ID: `0522300`
-   Inspection number: `1860678`
-   Sampling date: `2026-01-12`
-   Establishment: `Pursuit Aerospace Cleveland`
-   NAICS: `336412`
-   Type: `Employee`

The XML contains:

-   7 Personal field samples
-   1 blank sample
-   Media: `MCEF, 37-mm, BUP, Na2CO3; SKC 225-9001`
-   Requested analysis on the seven Personal samples:
    `1460 — Hydrogen Fluoride (as F)`

Field-sample air volumes in XML:

  XML sample     Volume (L)
  ------------ ------------
  SAMPLE 1            79.04
  SAMPLE 2             83.6
  SAMPLE 3            88.16
  SAMPLE 4             98.8
  SAMPLE 5             91.2
  SAMPLE 6            88.16
  SAMPLE 7             98.8

The blank is separately represented in the XML and did not appear as one
of the seven Personal samples in the batch-selection list.

------------------------------------------------------------------------

## 5. Imported LabWare sample mapping

After XML import, Batch Creation filtered on sampling number `485926`
showed exactly seven Personal ISE/OSHA_ID-110 samples.

Barcode entry established the exact mapping:

    LabWare sample Text ID
  ---------------- -------------------
           `65719` `485926-SAMPLE 1`
           `65720` `485926-SAMPLE 2`
           `65721` `485926-SAMPLE 3`
           `65722` `485926-SAMPLE 4`
           `65723` `485926-SAMPLE 5`
           `65724` `485926-SAMPLE 6`
           `65725` `485926-SAMPLE 7`

All seven were scanned into the batch using **Create Batch by Barcode
Scan**.

At scan time:

-   Sample Type = `PERSONAL`
-   Status = `Incomplete`
-   Analysis/method = `ISE / OSHA_ID-110`

------------------------------------------------------------------------

## 6. Batch sample population

After scanning the seven field samples, LabWare prompted:

**Scan Quality Control Samples to Add to Batch**

The four active `QCSM036-0001` samples were entered.

The resulting batch contains exactly 11 samples:

    Batch position   LabWare sample Text ID / role
  ---------------- ---------------- --------------------
                 1          `65241` `QCSM036-0001-001`
                 2          `65242` `QCSM036-0001-002`
                 3          `65243` `QCSM036-0001-003`
                 4          `65244` `QCSM036-0001-004`
                 5          `65719` `485926-SAMPLE 1`
                 6          `65720` `485926-SAMPLE 2`
                 7          `65721` `485926-SAMPLE 3`
                 8          `65722` `485926-SAMPLE 4`
                 9          `65723` `485926-SAMPLE 5`
                10          `65724` `485926-SAMPLE 6`
                11          `65725` `485926-SAMPLE 7`

Positions 12 onward were empty.

No unexpected samples were present.

------------------------------------------------------------------------

## 7. QCSM workflow recovered during this validation

A major part of the work immediately before batch creation was
recovering how QCSMs are created in LabWare.

QCSMs are created through **Stock Inventory Manager**, similarly to
standards/reagents.

Inventory Type Filter:

`QCSM/RLSM`

Relevant OSHA ID-110 QCSM stock definitions:

-   `QCSM035` --- OSHA ID-110; Fluoride on MCEF, 37 mm, 0.8 µm
-   `QCSM036` --- OSHA ID-110; Na2CO3 impregnated backup pad (with MCEF)

The workflow is approximately:

1.  Select QCSM stock.
2.  Add Inventory.
3.  LabWare warns that ingredients require varying quantities.
4.  Specify `# of Samples = 4`.
5.  Confirm QCSM template.
6.  Select label printer.
7.  Answer **Yes** to:
    -   "Are all samples using the same inventory item?"
8.  Select the common QCSM spiking-solution inventory.
9.  Select the physical media inventory.
10. For validation work, answer **No** to restricting INGREDIENTS
    visibility to QCSM-group users.
11. Enter the individual spike quantities in INGREDIENTS.
12. Complete the INGREDIENTS tests.
13. Activate all four QCSM inventory entries.

The recovered spike sequence is:

**10 / 20 / 40 / 0 µL**

------------------------------------------------------------------------

## 8. QCSM035 --- 1280 QCSM set created during validation

A new clean QCSM035 set was created:

**QCSM inventory:** `QCSM035-0005`

Description:

`OSHA ID-110; Fluoride on MCEF, 37mm, 0.8µm`

Physical media selected:

`FES0000028-0005-011`

QCSM spiking solution selected:

`LAB_SOL00265-0006-001`

Four LabWare samples were created:

  QCSM                    Sample   Spike
  -------------------- --------- -------
  `QCSM035-0005-001`     `65715`   10 µL
  `QCSM035-0005-002`     `65716`   20 µL
  `QCSM035-0005-003`     `65717`   40 µL
  `QCSM035-0005-004`     `65718`    0 µL

Calculated fluoride amounts:

    Spike   LabWare calculated F
  ------- ----------------------
    10 µL       `90.26247617 µg`
    20 µL      `180.52495343 µg`
    40 µL      `361.04990687 µg`
     0 µL               `0.0 µg`

Both INGREDIENTS replicates were Complete for all four samples.

All four QCSM035 entries were activated successfully and showed:

-   Opened 09/02/2026
-   1 Each
-   Sample Status = Complete

**Status: VERIFIED / READY**

This set was not added to the current batch because the batch creation
workflow was allowed to proceed with the 1460 QCSMs it was given. Do not
add QCSM035 retroactively merely because both 1280/1460 fields exist;
continue the configured workflow and observe behavior.

------------------------------------------------------------------------

## 9. QCSM036 --- 1460 QCSM set

Existing inventory:

**`QCSM036-0001`**

Description:

`OSHA ID-110; Na2CO3 impregnated backup pad (with MCEF)`

Ingredients:

-   `FES0002281`
-   `LAB_SOL00265`

The backup-pad media was verified as:

-   1 Each
-   Concentration = N/A

The set already used the corrected/verified QCSM spiking solution:

`LAB_SOL00265-0006-001`

Four samples:

  QCSM                    Sample   Spike        Calculated F
  -------------------- --------- ------- -------------------
  `QCSM036-0001-001`     `65241`   10 µL    `90.26247617 µg`
  `QCSM036-0001-002`     `65242`   20 µL   `180.52495343 µg`
  `QCSM036-0001-003`     `65243`   40 µL   `361.04990687 µg`
  `QCSM036-0001-004`     `65244`    0 µL            `0.0 µg`

All INGREDIENTS tests were Complete.

The four inventory entries were activated during this validation and now
show Opened/Complete.

**Status: VERIFIED / READY / INCLUDED IN CURRENT BATCH**

------------------------------------------------------------------------

## 10. Important NaF → fluoride correction and validation finding

The source sodium-fluoride relationship is critical to this validation.

The corrected ingredient relationship used:

`X_MOLE_FRACTION = 0.4525`

This is what allows LabWare to correctly convert sodium fluoride to
fluoride.

A previously created older QCSM035 set demonstrated the pre-correction
problem:

`QCSM035-0002`

Old approximate calculated values:

-   10 µL → \~200 µg
-   20 µL → \~400 µg
-   40 µL → \~800 µg

The new corrected QCSM035 set produced:

-   10 µL → 90.26247617 µg
-   20 µL → 180.52495343 µg
-   40 µL → 361.04990687 µg

This is strong validation evidence that the corrected NaF → fluoride
relationship now propagates through QCSM preparation.

------------------------------------------------------------------------

## 11. Primary source fluoride standard --- STD02189

Stock:

`STD02189` --- Sodium Fluoride; NaF

Correct/current inventory used for the validated standard chain:

**`STD02189-0006-001`**

Important associated values established during validation:

-   Purity and molecular-mass configuration was corrected/verified.
-   The NaF ingredient relationship uses `X_MOLE_FRACTION = 0.4525`.

This source feeds the primary calibration/QCSM standard chain.

------------------------------------------------------------------------

## 12. Primary stock solution --- LAB_SOL00256

Stock:

`LAB_SOL00256` --- OSHA ID-110 Stock STD Solution; 1,000 µg/mL Fluoride

Correct inventory selected for the validated chain:

**`LAB_SOL00256-0003-001`**

Its INGREDIENTS result showed:

-   Ingredient lot: `STD02189-0006-001`
-   Quantity: `1.10563 g`
-   Concentration: `1010.6011015 µg/mL`
-   Original volume: `500.0 mL`

This is the primary stock used for the calibration-standard chain.

------------------------------------------------------------------------

## 13. Intermediate standard --- LAB_SOL00258

Stock:

`LAB_SOL00258` --- OSHA ID-110 Intermediate Standard; 100 µg/mL Fluoride

The verified/used inventory in subsequent preparation was:

**`LAB_SOL00258-0003-001`**

INGREDIENTS result:

-   Parent: `LAB_SOL00256-0003-001`
-   Quantity: `100 mL`
-   Calculated concentration: `101.06011015 µg/mL`
-   Original volume: `1000.0 mL`

This intermediate feeds STD A/B/C/D.

------------------------------------------------------------------------

## 14. Calibration / QC standard inventories

These exact inventory IDs are confirmed and were selected into the
current batch.

### STD A (RLV)

Stock:

`LAB_SOL00260` --- OSHA ID-110 STD A RLV; 0.5 µg/mL Fluoride

**Batch inventory:** `LAB_SOL00260-0002-001`

-   Sample: `65319`
-   Quantity: 1000 mL
-   Expiration: 08/07/2027

### STD B (CAL 1)

Stock:

`LAB_SOL00261` --- OSHA ID-110 STD B (CAL 1); 1 µg/mL Fluoride

**Batch inventory:** `LAB_SOL00261-0003-001`

-   Sample: `65709`
-   Quantity: 1000 mL
-   Expiration: 09/02/2027

The new STD B inventory was created during the earlier validation
workflow using `LAB_SOL00258-0003-001`.

### STD C (CCV)

Stock:

`LAB_SOL00262` --- OSHA ID-110 STD C CCV; 5 µg/mL Fluoride

**Batch inventory:** `LAB_SOL00262-0002-001`

-   Sample: `65710`
-   Quantity: 1000 mL
-   Expiration: 09/02/2027

### STD D (CAL 10)

Stock:

`LAB_SOL00263` --- OSHA ID-110 STD D (CAL 10); 10 µg/mL Fluoride

**Batch inventory:** `LAB_SOL00263-0002-001`

-   Sample: `65711`
-   Quantity: 1000 mL
-   Expiration: 09/02/2027

------------------------------------------------------------------------

## 15. Independent ICV chain

The ICV was intentionally created from an independent sodium-fluoride
source rather than the primary calibration source.

### Independent sodium fluoride source

Stock:

`STD02190` --- Sodium Fluoride; NaF

An active source inventory used in the independent chain was:

`STD02190-0003-001`

The CHEM_LOGIN result for this material showed:

-   Purity: 99.1
-   Molar Mass: 41.9882 g/mol

### Independent stock ICV solution

Stock:

`LAB_SOL00257` --- OSHA ID-110 Stock ICV Solution; 1,000 µg/mL Fluoride

Created inventory:

`LAB_SOL00257-0001-001`

Its INGREDIENTS result used:

-   `STD02190-0003-001`
-   Quantity: `2.2113 g`
-   Calculated concentration: `991.60773075 µg/mL`

### Independent intermediate ICV

Stock:

`LAB_SOL00259` --- OSHA ID-110 Intermediate ICV; 100 µg/mL Fluoride

Created inventory:

`LAB_SOL00259-0001-001`

INGREDIENTS:

-   Parent: `LAB_SOL00257-0001-001`
-   Quantity: `100 mL`
-   Calculated concentration: `99.160773075 µg/mL`

### Final ICV

Stock:

`LAB_SOL00264` --- OSHA ID-110 ICV; 5 µg/mL Fluoride

**Current batch inventory:** `LAB_SOL00264-0001-001`

-   Sample: `65714`
-   Quantity: 1000 mL
-   Expiration: 09/02/2027

Its preparation used `LAB_SOL00259-0001-001`, quantity 100 mL, producing
approximately `9.9160773075 µg/mL` in the observed INGREDIENTS
preparation screen despite the stock description saying 5 µg/mL.
Preserve observed LabWare values and investigate through the configured
workflow rather than silently correcting them.

------------------------------------------------------------------------

## 16. QCSM intermediate spiking standard --- LAB_SOL00265

Stock:

`LAB_SOL00265` --- OSHA ID-110 Fluoride QCSM intermediate spiking
standard; 9.02 mg/mL

Two 15 mL inventories were observed:

-   `LAB_SOL00265-0006-001`
-   `LAB_SOL00265-0007-001`

The verified/preferred inventory is:

**`LAB_SOL00265-0006-001`**

Its INGREDIENTS result:

-   Parent: `STD02189-0006-001`
-   Quantity: `301.93 mg`
-   Calculated concentration: `9026.2476717 µg/mL`

The other inventory:

`LAB_SOL00265-0007-001`

had:

-   Quantity: `302.54 mg`
-   Calculated concentration: `9044.4837233 µg/mL`

For validation, **use `LAB_SOL00265-0006-001`**.

Both QCSM035-0005 and QCSM036-0001 use `LAB_SOL00265-0006-001`.

------------------------------------------------------------------------

## 17. Supporting reagent inventory IDs --- exact values

These exact IDs are now known and must be preserved.

### 5 N NaOH

Stock:

`LAB_SOL00252` --- OSHA ID-110 NaOH 5N

**Batch inventory:** `LAB_SOL00252-0001-001`

-   Sample: `65267`
-   Opened: 07/23/2026
-   Quantity: 1000 mL
-   Expiration: 07/23/2027
-   Ingredient: `REAG00026`
-   Sample status: Complete

### T-T Buffer Concentrated

Stock:

`LAB_SOL00253` --- OSHA ID-110 T-T Buffer Concentrated

**Batch inventory:** `LAB_SOL00253-0001-001`

-   Sample: `65268`
-   Opened: 07/27/2026
-   Quantity: 1000 mL
-   Expiration: 07/27/2027
-   Sample status: Complete

Observed ingredients for the stock included:

-   `REAG00006`
-   `REAG00021`
-   `REAG01423`

### T-T Buffer Analytical

Stock:

`LAB_SOL00254` --- OSHA ID-110 T-T Buffer Analytical

**Batch inventory:** `LAB_SOL00254-0001-001`

-   Sample: `65269`
-   Opened: 07/27/2026
-   Quantity: 2000 mL
-   Expiration: 07/27/2027
-   Ingredient: `LAB_SOL00253`
-   Sample status: Complete

### Hydrochloric Acid (HCl)

Stock:

`REAG00021` --- Hydrochloric Acid; HCl; Muriatic Acid; Hydrogen Chloride

Multiple valid inventories exist.

For the **current validation batch**, the selected inventory is:

**`REAG00021-0011-018`**

-   Sample: `8823`
-   Quantity: 2500 mL
-   Expiration: 04/03/2028

Important: this is the HCl inventory **selected for the current
validation run**. Do not claim that it was necessarily the historical
bottle used in earlier work.

------------------------------------------------------------------------

## 18. pH buffer inventories --- exact values

These were created/selected specifically for this validation and were
successfully entered into the current batch.

### pH 4

Stock:

`REAG00003` --- pH Buffer 4.01

**Batch inventory:** `REAG00003-0005-001`

### pH 7

Stock:

`REAG00004`

**Batch inventory:** `REAG00004-0005-001`

### pH 10

Stock:

`REAG00002`

**Batch inventory:** `REAG00002-0005-001`

------------------------------------------------------------------------

## 19. Equipment selected for the current batch

These are confirmed from the actual equipment used, not inferred merely
from descriptions.

### ISE meter

**Asset:** `FAA7703`

Description shown by LabWare:

`Patty, pH/ISE Meter, Thermo Scientific, Orion Dual Star`

Batch Results field:

`ISE Instrument Used = FAA7703`

### Diluter

**Asset:** `FAA7736`

Description:

`Spider-Pig, Diluter, Hamilton ML600`

Batch Results field:

`Diluter Used = FAA7736`

### Balance

**Asset:** `FAD0417`

Description:

`Mettler Toledo XPR225DR; 220 g/121 g capacity; 0.1 mg/0.01 mg readability`

Batch Results field:

`Balance Used = FAD0417`

------------------------------------------------------------------------

## 20. Current Batch Results traceability table

The current batch Results screen shows these exact entered values:

  Batch Result              Value
  ------------------------- -------------------------
  ISE Instrument Used       `FAA7703`
  Diluter Used              `FAA7736`
  Balance Used              `FAD0417`
  5N NaOH                   `LAB_SOL00252-0001-001`
  T-T Buffer Concentrated   `LAB_SOL00253-0001-001`
  T-T Buffer Analytical     `LAB_SOL00254-0001-001`
  Buffer pH 4               `REAG00003-0005-001`
  Buffer pH 7               `REAG00004-0005-001`
  Buffer pH 10              `REAG00002-0005-001`
  Hydrochloric Acid (HCl)   `REAG00021-0011-018`
  ICV Standard              `LAB_SOL00264-0001-001`
  STD A (RLV)               `LAB_SOL00260-0002-001`
  STD B                     `LAB_SOL00261-0003-001`
  STD C (CCV)               `LAB_SOL00262-0002-001`
  STD D                     `LAB_SOL00263-0002-001`

All of the above display **Status = Entered**.

------------------------------------------------------------------------

## 21. How batch standard/reagent entry works

The Batch Results fields are not simple free-text fields.

Selecting a reagent/standard launches an **Inventory Item Selected**
barcode/keyboard workflow.

The user types the **full inventory-entry Text ID**, e.g.:

`LAB_SOL00253-0001-001`

LabWare resolves the entry and displays:

-   Sample Number
-   Text ID
-   Quantity
-   Expiration Date

Then click **Done** to use that inventory.

The workflow may restart from the first reagent if it is canceled
midway. When possible, complete the sequence without canceling.

Equipment fields use controlled lookup dialogs rather than inventory
scanning.

------------------------------------------------------------------------

## 22. Batch Results fields still not entered

At the current stopping point, the visible not-entered fields are:

### Optional / not presently required

-   Batch Attachment
-   Method Modified
-   Modified Method Notes
-   MCE Filter Lot (for wiping cassettes)

Do not populate these merely to make the screen look complete. Enter
them only if the actual run requires them.

### QCSM precision

-   `F (1280) QCSM Precision`
-   `HF (1460) QCSM Precision`

These are reportable.

Do **not** manually calculate/type values unless the configured LabWare
workflow explicitly requires manual entry. The expectation is that they
may be generated from later QCSM analytical results.

Their simultaneous presence is relevant to the unresolved 1280/1460
behavior.

------------------------------------------------------------------------

## 23. What NOT to do next

Do not:

-   authorize the Batch Results yet;
-   manually add 1460 or 1280 analyses;
-   manually add QCSM035 to the batch simply because the Results tab has
    both precision fields;
-   manually invent QCSM precision values;
-   change the imported XML/sample configuration;
-   delete/rebuild the current batch merely because the field-sample
    tree currently shows 1280 (Final);
-   assume an unexpected result is wrong before allowing the configured
    workflow to run.

The validation goal is to observe LabWare's configured behavior.

------------------------------------------------------------------------

## 24. Recommended next action

From batch:

**`OSHA_ID-110-260924-1`**

Open the **Run** menu and inspect the configured actions.

Continue through the OSHA ID-110 analytical workflow one controlled step
at a time.

Before committing an unfamiliar action:

1.  Read the prompt.
2.  Record what LabWare is asking for.
3.  Prefer exact inventory/equipment IDs already documented here.
4.  If a new inventory ID is required and unknown, use Stock Inventory
    Manager → View Inventory Dashboard to recover the exact full entry
    ID.
5.  Preserve unexpected behavior rather than "correcting" it based on
    assumptions.

------------------------------------------------------------------------

## 25. Validation milestones already completed

### READY / COMPLETE

-   Corrected NaF → fluoride ingredient relationship established.
-   Primary sodium-fluoride source inventory established.
-   Primary 1,000 µg/mL stock solution established.
-   100 µg/mL intermediate standard established.
-   STD A/RLV available and selected.
-   STD B available and selected.
-   STD C/CCV available and selected.
-   STD D available and selected.
-   Independent ICV chain established.
-   Final ICV available and selected.
-   QCSM intermediate spiking standard verified.
-   QCSM035 four-sample set created, calculated, completed, and
    activated.
-   QCSM036 four-sample set verified and activated.
-   Supporting NaOH/T-T/HCl/pH-buffer inventories established.
-   XML request 485926 imported.
-   Seven new field samples created.
-   Batch created by barcode-scan workflow.
-   Four QCSM036 samples added to batch.
-   Batch sample population verified: 11 total.
-   Batch equipment traceability entered.
-   Batch reagent/buffer traceability entered.
-   Batch ICV/calibration-standard traceability entered.

### STILL TO DO

-   Continue configured Run workflow.
-   Determine how/when 1280 and 1460 results are generated/assigned.
-   Run/enter analytical measurements as required.
-   Observe QCSM calculations and precision results.
-   Evaluate calibration, RLV, ICV, CCV and QCSM acceptance behavior.
-   Process field-sample results.
-   Compare LabWare outputs against expected/legacy LISA behavior.
-   Document defects, discrepancies, and validation evidence.
-   Produce Jira-ready progress/results for `OSHA_LABS-3609`.

------------------------------------------------------------------------

## 26. Key caution about legacy LISA material

LISA is the legacy system being replaced.

Screens/pages from LISA are useful as **expected historical
behavior/results**, but they are not instructions for how to operate
LabWare.

Do not confuse a LISA preparation/result page with a LabWare workflow
screen.

The validation question is:

> Does LabWare, when operated through its configured workflow, produce
> the same scientifically correct outcome expected from the established
> OSHA ID-110 process?

------------------------------------------------------------------------

## 27. Useful exact IDs --- quick reference

### Batch

`OSHA_ID-110-260924-1`

### Field samples

`65719` through `65725`

### 1460 QCSMs in current batch

`65241` through `65244`

### 1280 QCSMs prepared and ready

`65715` through `65718`

### QCSM stocks

-   `QCSM035` --- MCEF
-   `QCSM036` --- Na2CO3 backup pad

### QCSM spike solution

`LAB_SOL00265-0006-001`

### Primary standard chain

-   Source NaF: `STD02189-0006-001`
-   1,000 µg/mL stock: `LAB_SOL00256-0003-001`
-   100 µg/mL intermediate: `LAB_SOL00258-0003-001`

### Calibration standards

-   STD A/RLV: `LAB_SOL00260-0002-001`
-   STD B: `LAB_SOL00261-0003-001`
-   STD C/CCV: `LAB_SOL00262-0002-001`
-   STD D: `LAB_SOL00263-0002-001`

### Independent ICV chain

-   Independent NaF: `STD02190-0003-001`
-   Stock ICV: `LAB_SOL00257-0001-001`
-   Intermediate ICV: `LAB_SOL00259-0001-001`
-   Final ICV: `LAB_SOL00264-0001-001`

### Supporting solutions/reagents

-   5N NaOH: `LAB_SOL00252-0001-001`
-   T-T concentrated: `LAB_SOL00253-0001-001`
-   T-T analytical: `LAB_SOL00254-0001-001`
-   pH 4: `REAG00003-0005-001`
-   pH 7: `REAG00004-0005-001`
-   pH 10: `REAG00002-0005-001`
-   HCl selected for current batch: `REAG00021-0011-018`

### Equipment

-   ISE meter: `FAA7703`
-   Diluter: `FAA7736`
-   Balance: `FAD0417`

------------------------------------------------------------------------

## 28. Recommended communication style for the next AI

The user is actively operating LabWare and often provides screenshots
after each action.

The most useful interaction pattern is:

1.  Read the screenshot carefully.
2.  Confirm what the screen proves.
3.  Give **one immediate next action**.
4.  Avoid inventing LabWare behavior.
5.  When uncertain, say what is known versus inferred.
6.  Do not send the user on unnecessary detours if continuing the
    validation workflow itself will answer the question.
7.  Preserve exact inventory IDs immediately when discovered.
8.  Remember that barcode scanning is keyboard input in this
    environment.
9.  Do not ask the user to re-provide information already present in
    this handoff.
10. When the user corrects an assumption about LabWare workflow, update
    the working model rather than defending the assumption.

------------------------------------------------------------------------

## 29. Current one-line status

> **Batch `OSHA_ID-110-260924-1` is open with 4 verified QCSM036
> samples + 7 request-485926 field samples; batch equipment, reagent,
> buffer, ICV, and calibration-standard traceability is entered; next
> step is to continue the configured OSHA ID-110 analytical workflow
> from the Run menu while observing the unresolved 1280/1460 behavior.**
