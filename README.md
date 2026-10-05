# osha-id110-labware-validation

Validation of the LabWare LIMS implementation of OSHA ID-110.

## Current status

As of 2026-10-05, the known missing `1460 F/T` calculation inputs have been configured in DEV to parallel the established `1280 F/T` recovery pattern. This change, together with the DEV changes made on 2026-09-25, is **pending regression validation**.

The next operational step is a fresh regression run based on `cases/HD-2026-12-02` with both `QCSM035` and `QCSM036` included.

For continuation context, start with [`docs/handoff/CURRENT.md`](docs/handoff/CURRENT.md).
