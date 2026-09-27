# Business Entity Resolution

The main reproducible implementation is provided in
`business_entity_resolution.ipynb`.

The final pipeline used:

1. Load Source 1, Source 2 and Source 3 records.
2. Normalize business names and addresses.
3. Generate candidate entity pairs using exact normalized-name
   and address-number blocking signals.
4. Score candidate pairs using normalized text similarity.
5. Select final matches using precision-oriented thresholds.
6. Generate candidate and matching TSV submission files.

The notebook contains the exploratory calibration and final
scoring workflow used for this submission.
