# OSHA ID-110 Database Reference

Working SQL reference; verify before making configuration changes.

``` sql
SELECT * FROM ANALYSIS WHERE NAME = 'ISE';

SELECT *
FROM COMPONENT
WHERE ANALYSIS = 'ISE'
ORDER BY ORDER_NUMBER;

SELECT
    SAMPLE_NUMBER, TEST_NUMBER, RESULT_NUMBER, ORDER_NUMBER,
    ANALYSIS, NAME, RESULT_TYPE, STATUS, ENTRY,
    FORMATTED_ENTRY, NUMERIC_ENTRY, UNITS
FROM RESULT
WHERE SAMPLE_NUMBER = :sample_number
  AND ANALYSIS = 'ISE'
ORDER BY ORDER_NUMBER, RESULT_NUMBER;

SELECT
    ANALYSIS, COMPONENT, VERSION, DESCRIPTION, SOURCE_CODE
FROM CALCULATION
WHERE ANALYSIS = 'ISE'
ORDER BY COMPONENT, VERSION;

SELECT *
FROM CALC_VARIABLES
WHERE ANALYSIS = 'ISE'
  AND COMPONENT IN ('1280 F/T', '1460 F/T')
ORDER BY COMPONENT, NAME;
```

Observed 1280 F/T variables: `found`, `theory`, `theoryElement`.

No 1460 F/T calculation variables were found during run 485926.

Air Volume calculation reads `X_PI_OIS_SAMPLES` flow rate and total
time.

## Change discipline

Before modifying calculations/components:

1.  capture current configuration;
2.  understand the working 1280 pattern;
3.  inspect referenced subroutines such as `CALC_QC_CONC`;
4.  make controlled changes;
5.  recycle `HD-2026-12-02` with a new request number;
6.  compare against the fixed historical values.
