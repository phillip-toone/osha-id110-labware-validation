# LabWare 8 — Observed batch-template component development workflow (OSHA ID-110)

**Evidence:** October 9, 2026 user-demonstrated LabWare DEV screenshots. This describes observed navigation, not an assumed generic LabWare interface.

1. In `OSHA_LIMS_DEV` main window, choose **Explore Tables**.
2. Expand **Batch Tests Template** and select `OSHA_ID-110`.
3. Click **Configure...** to open the **Fields** dialog.
4. Click **Components...** to open the **Components Dialog**.
5. Click **Add...**, enter the component name, and confirm. A new component initially opens Numeric Properties; select another type (e.g. **Calculated**) from the **Configure** dropdown on the right when required.
6. Configure type-specific properties, units, optional/reportable/displayed flags, and order. For a Calculated component, use **Calculation...** to inspect/edit source and component-variable inputs.
7. Save through the dialogs and verify persisted configuration using read-only `BATCH_COMPONENT` queries. **Saving does not equal runtime validation.**

## Verified equipment-browse pattern

`Balance Used` is Calculated with alias `BALANCE` and source:

```text
instGroupName = SELECT BATCH_COMPONENT.ALIAS_NAME
GOSUB BC_INSTRUMENT_BROWSE
RETURN instrumentName
```

`Pipettor Used` follows this pattern with alias `PIPETTOR`, confirmed by an `INSTRUMENTS` record (`002617`) whose `INST_GROUP=PIPETTOR`. Shared routine source was inspected; it queries `BATCH_COMPONENT.ALIAS_NAME` to filter instruments and includes owner/status/verification/calibration checks. Its preventive-maintenance check is explicitly disabled (`pmFlag=FALSE`). No runtime proof of pipettor selection yet.

## Variation configuration observed

Within ISE analysis, **Analysis Variations** lists five OSHA ID-110 variations. Selecting a variation and opening **Component Variations** exposes explicit per-variation component entries. Their **Properties...** include description, units, rounding, reportable, displayed, optional, and limit fields. Empty override lists were observed for QC, QCSM, SCREEN, and WIPE before the QCSM edits; empty does not prove no inherited components. AIR overrides for 1280/1460 Final were inspected. Do not assume all variation entries should be populated without method evidence.
