# Business Entity Resolution

A data matching project that identifies records referring to the same business across multiple noisy data sources.

This project was developed as part of an **entity resolution / record linkage challenge** and focuses on building a practical, precision-oriented matching pipeline using Python, DuckDB, AWS SageMaker, and text similarity techniques.

## 📌 Project Overview

Business information can appear differently across different datasets because of:

* Spelling variations and typos
* Different business-name formats
* Company suffixes such as `Ltd`, `LLC`, `Pvt Ltd`, etc.
* Punctuation and formatting differences
* Address abbreviations
* Missing or reordered address information
* Transliteration and text variations

The goal of this project is to identify which records from **Source 2 and Source 3** correspond to each business in **Source 1**.

## 🔄 Approach

The matching pipeline consists of the following stages:

```text
Raw business records
        ↓
Text normalization
        ↓
Candidate generation
        ↓
Candidate pair scoring
        ↓
Precision-oriented matching
        ↓
Submission outputs
```

### 1. Text Normalization

Business names and addresses are normalized before comparison.

The preprocessing includes:

* Lowercasing
* Removing accents
* Removing punctuation
* Whitespace normalization
* Company suffix normalization
* Address abbreviation normalization

Examples of address normalization include:

```text
Street → st
Road → rd
Avenue → ave
Boulevard → blvd
Apartment → apt
Suite → ste
```

### 2. Candidate Generation

Comparing every Source 1 record against every Source 2 and Source 3 record would be computationally expensive.

Instead, candidate pairs are generated using blocking signals such as:

* Exact normalized business-name matches
* Address-number matching
* Country consistency

This significantly reduces the number of pairs that need detailed comparison.

### 3. Similarity Scoring

Candidate pairs are scored using normalized text similarity.

Two primary signals are calculated:

* Business-name similarity
* Business-address similarity

The final scoring process gives greater importance to business-name similarity while still considering address similarity.

### 4. Precision-Oriented Matching

Because incorrect entity merges are particularly costly in entity resolution, the final matching rule uses relatively strict similarity thresholds.

The final pipeline uses:

```text
Name similarity ≥ 0.85
Address similarity ≥ 0.85
Combined similarity ≥ 0.90
```

with the combined score calculated from the name and address similarities.

> These thresholds were selected through experimentation on sampled training data and are documented as part of this implementation. They should not be interpreted as universally optimal thresholds.

## 🛠️ Technologies Used

* **Python**
* **Pandas**
* **DuckDB**
* **AWS SageMaker**
* **Boto3**
* **Regular Expressions**
* **SequenceMatcher**
* **Jupyter Notebook**

## 📊 Dataset Structure

The challenge data contains three source datasets:

```text
Source 1
Source 2
Source 3
```

Each source contains fields such as:

```text
entity_id
business_name
business_address
country
```

Training data also includes ground-truth entity relationships.

The original datasets are **not included in this repository** because they are large and were provided by the challenge.

## 📁 Repository Structure

```text
business-entity-resolution/
│
├── README.md
├── Documentation_template.md
├── LICENSE
│
└── code/
    ├── README.md
    └── business_entity_resolution.ipynb
```

The notebook contains the implementation and experiments used to develop the matching pipeline.

The generated submission files are not stored in GitHub because of their large file sizes. They were generated separately as part of the challenge workflow.

## 💻 Computational Approach

The project was developed in **AWS SageMaker JupyterLab**.

Because the datasets are large, the implementation uses:

* DuckDB for disk-backed data processing
* Batch processing
* Indexed entity lookups
* Controlled memory usage
* Candidate blocking before similarity scoring

This avoids loading all source records into memory simultaneously.

## 📈 Final Processing

The test candidate generation produced approximately:

```text
9.76 million candidate pairs
```

These candidate pairs were scored in batches rather than processed as one large in-memory operation.

The final pipeline generated:

```text
candidate_pairs.tsv
matching_results.tsv
```

The output files contain the candidate relationships and final matched entity relationships respectively.

  
## 📄 Documentation

Additional implementation details are available in:

[`Documentation_template.md`](Documentation_template.md)


---

**Author:** Monika T M, Impana Rao
**Project:** Business Entity Resolution
