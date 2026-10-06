# osha-id110-labware-validation

Validation of the LabWare LIMS implementation of OSHA ID-110.

## Current status

As of 2026-10-06, diagnostic run `485927` identified two remaining OSHA ID-110 QCSM configuration defects: the QCSM theoretical target did not match the F/T component aliases required by `CALC_QC_CONC`, and the fluoride batch-precision wrappers still targeted copied `As F/T` / `Cd F/T` results. The ID-110-specific configuration corrections have been implemented and configuration-verified; shared `CALC_QC_CONC` and `CALC_BATCH_QC_REC_PRECISION` were not changed.

Because LabWare samples/tests remain linked to the analysis version that existed when they were created, `485927` is preserved as a diagnostic pre-fix run rather than used to validate the corrected configuration. The next operational step is a fresh post-fix regression, request `485928`, based on `cases/HD-2026-12-02`.

For continuation context, start with [`docs/handoff/CURRENT.md`](docs/handoff/CURRENT.md). Detailed QCSM recovery/precision findings are in [`docs/findings/2026-10-06-qc-recovery-and-precision-investigation.md`](docs/findings/2026-10-06-qc-recovery-and-precision-investigation.md).
