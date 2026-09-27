# Business Entity Resolution Challenge

## 1. Approach

This submission uses a deterministic, CPU-based entity-resolution
pipeline designed for high precision.

Source 1 is treated as the reference entity set. Source 2 and
Source 3 contain records that may correspond to Source 1 entities.

The pipeline consists of:

- text normalization
- candidate generation/blocking
- candidate-pair similarity scoring
- precision-oriented final matching

## 2. Text normalization

Business names and addresses are converted to lowercase, accents
are removed, punctuation is normalized, and repeated whitespace is
removed.

Business-name normalization also removes common corporate suffixes
where appropriate.

Address normalization standardizes common terms such as:

- street -> st
- road -> rd
- avenue -> ave
- boulevard -> blvd
- drive -> dr
- lane -> ln
- highway -> hwy
- apartment -> apt
- suite -> ste
- floor -> fl

## 3. Candidate generation

Candidate pairs were generated using same-country blocking signals,
including normalized business-name and address-number information.

The resulting test candidate universe contained:

- Source 1 entities: 1,732,544
- Candidate pairs: 9,765,660

Candidate pairs were stored in DuckDB and scored in memory-controlled
batches.

## 4. Candidate scoring

For each candidate pair, normalized business-name and business-address
similarities were calculated using Python SequenceMatcher.

The combined score used for final selection was:

    0.6 * name_similarity + 0.4 * address_similarity

The final precision-oriented selection rule was:

- name similarity >= 0.85
- address similarity >= 0.85
- combined score >= 0.90

This produced 450,622 selected candidate pairs across 376,417
Source 1 entities.

Source 1 entities without a selected match are retained with an
empty matched-entity list.

## 5. Final outputs

### candidate_pairs.tsv

Contains one row for every Source 1 test entity.

Columns:

- source1_entity_id
- candidate_entity_ids

### matching_results.tsv

Contains one row for every Source 1 test entity.

Columns:

- source1_entity_id
- matched_entity_ids

Every selected final match was checked to ensure that it appears
inside the corresponding candidate list.

## 6. Final file checks

Both final TSV files contain:

    1,732,544 rows

The final matching output contains:

    450,622 matched pairs

The internal candidate/match consistency check found:

    0 matches outside candidate lists

## 7. Compute and memory considerations

The pipeline was executed in AWS SageMaker Studio using a
memory-constrained environment.

Candidate scoring was performed in batches of 10,000 candidate
pairs with DuckDB configured for controlled memory use.

Large all-at-once aggregation operations were avoided.

## 8. Model/license information

No external pretrained machine-learning model is used.

The final matching method is a deterministic text-similarity
pipeline based on Python standard-library functionality and
DuckDB/Pandas processing.

The submitted implementation is released under the MIT License.

## 9. Limitations

The final threshold was calibrated using sampled training
ground-truth pairs and additional hard-match inspection. Because
the official submission validator was not available in the
execution environment, the final files were checked for row
coverage, required columns, and candidate/match consistency, but
an official validator result is not claimed here.
