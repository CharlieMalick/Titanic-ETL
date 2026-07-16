# Titanic ETL

This is a self-contained ETL (Extract, Transform, Load) pipeline built as a personal checkpoint project. The goal was to independently build a complete data pipeline from raw CSV to a queryable database, without external help, as a proof point before starting larger data engineering projects.

## Overview

This project takes the classic Titanic passenger dataset (from Kaggle) and runs it through a full ETL cycle:

1. **Extract** — Load the raw CSV into a pandas DataFrame.
2. **Transform** — Clean and enrich the data.
3. **Load** — Write the cleaned data into a SQLite database.
4. **Verify** — Run SQL queries against the loaded table to confirm correctness and pull out real insights.

## Dataset

- Source: [Kaggle Titanic competition](https://www.kaggle.com/c/titanic/data)
- File used: `train.csv` (891 rows, 12 original columns)
- Location in repo: `dataset/train.csv`

## Transform steps

- **Dropped `Cabin`** — ~77% of values were missing, making the column unreliable to impute or use meaningfully.
- **Imputed `Age`** — Filled missing values (177 of 891) with the median age, to avoid the distortion a mean would introduce from outliers.
- **Filled `Embarked`** — Filled the small number of missing values with the most common port of embarkation.
- **Converted `Sex` to a categorical dtype** — Reflects that it's a fixed set of repeating values, not free-form text.
- **Derived a `Title` column** — Extracted the honorific (Mr, Mrs, Miss, Master, etc.) from the `Name` field using a regex pattern. Titles capture demographic/social signal that `Sex` and `Age` alone don't fully represent (e.g., distinguishing young boys from adult men, or married from unmarried women).

## Load

The cleaned DataFrame is written to a local SQLite database (`titanic.db`) as a table called `passengers`, using `pandas.DataFrame.to_sql()`.
