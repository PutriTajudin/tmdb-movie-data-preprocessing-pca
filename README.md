# Data Preprocessing & Dimensionality Reduction on the TMDB 5000 Movie Dataset

This project implements a data preprocessing pipeline and Principal Component Analysis (PCA) using the TMDB 5000 Movie Dataset. It covers data quality inspection, cleaning, integration, feature extraction, correlation analysis, and dimensionality reduction.

## Project Overview

Real-world datasets may contain missing values, implicit missing values, inconsistent data types, and redundant attributes. This project addresses these issues to prepare movie metadata for further data analysis and machine learning workflows.

### Key Highlights

- **Data Cleaning:** Handled missing values, converted zero values representing unavailable information into `NaN`, and inspected potential outliers using the Interquartile Range (IQR) method.
- **Data Integration:** Combined movie metadata and credits using their movie identifiers and removed redundant columns.
- **Feature Extraction:** Parsed nested JSON-like strings to extract genre, cast, and director information, then created numerical features.
- **Correlation Analysis:** Examined relationships between numerical attributes to identify potentially redundant information.
- **Dimensionality Reduction:** Reduced 8 numerical features to 4 principal components while retaining **80.68% of the total variance**.

## Dataset

The project uses the TMDB 5000 Movie Dataset obtained from Kaggle.

| File | Shape | Description |
|---|---:|---|
| `tmdb_5000_movies.csv` | 4,803 × 20 | Movie metadata, including budget, revenue, genres, popularity, runtime, and ratings |
| `tmdb_5000_credits.csv` | 4,803 × 4 | Cast and crew metadata |

**Integration key:** `movies.id` ↔ `credits.movie_id`

## Preprocessing Pipeline

### 1. Data Inspection and Cleaning

The initial inspection identified missing values, zero values that may represent unavailable information, and attributes requiring type conversion.

- Dropped `homepage` because approximately 64.3% of its values were missing.
- Filled missing `tagline` and `overview` values with descriptive placeholders.
- Converted `release_date` to datetime and removed one row with a missing or invalid date.
- Imputed missing `runtime` values using the median.
- Replaced zero values in `budget` and `revenue` with `NaN` to represent unavailable information.
- Set `vote_average` to `NaN` when `vote_count` was zero.
- Detected potential numerical outliers using IQR and retained them when they represented plausible movie characteristics.

Missing values in `budget`, `revenue`, and `vote_average` were preserved in the main cleaned dataset. Median imputation was applied to a separate PCA input dataset when required.

### 2. Data Integration and Feature Extraction

The two datasets were combined using an inner join on `movies.id` and `credits.movie_id`.

Redundant columns, including `movie_id` and `title_y`, were removed after verifying that the duplicated title columns contained matching values.

Nested JSON-like strings were parsed using Python's `ast.literal_eval` to extract structured information, including:

- `director`: director identified from the crew data.
- `genres_list`: list of movie genres.
- `cast_list`: up to three cast members.
- `genre_count`: number of genres associated with a movie.
- `cast_count`: number of cast members recorded for a movie.

The numerical features `genre_count` and `cast_count` were added to the six original numerical attributes for correlation analysis and PCA.

### 3. Correlation Analysis

Correlation analysis was used to examine relationships between the eight numerical features and identify potentially redundant information.

The strongest positive correlations reported in the analysis were:

- `vote_count` and `popularity`: **0.78**
- `vote_count` and `revenue`: **0.76**
- `budget` and `revenue`: **0.71**

These results indicate positive linear associations within the dataset; they do not establish causal relationships.

### 4. PCA Preparation and Dimensionality Reduction

Eight numerical features were used for PCA:

`budget`, `revenue`, `popularity`, `runtime`, `vote_average`, `vote_count`, `genre_count`, and `cast_count`.

The PCA workflow consisted of the following steps:

1. **Median imputation:** Filled remaining missing values in a separate PCA input dataset because PCA cannot directly process `NaN` values.
2. **Standardization:** Applied `StandardScaler` to place numerical features on comparable scales, with a mean of approximately zero and a standard deviation of approximately one.
3. **PCA:** Transformed the standardized features into principal components.
4. **Component selection:** Selected four principal components based on their cumulative explained variance.
5. **Interpretation:** Examined component loadings to understand how the original features contributed to each component.

## Key Results

### Dimensionality Reduction

| Metric | Result |
|---|---:|
| Numerical features before PCA | 8 |
| Principal components retained | 4 |
| Dimensionality reduction | 50% |
| Cumulative explained variance | **80.68%** |

The first two components explain approximately 59.29% of the total variance. The four selected components retain 80.68%, providing a lower-dimensional representation of the numerical features while preserving a substantial proportion of their variance.

### Key Takeaways

- Data cleaning addressed missing and unavailable values while preserving potentially valid outliers.
- Data integration combined the two CSV files into a unified dataset.
- Feature extraction converted semi-structured metadata into features suitable for numerical analysis.
- Correlation analysis revealed positive associations among vote count, popularity, revenue, and budget.
- PCA reduced the numerical feature space from 8 dimensions to 4 components while retaining 80.68% of the total variance.

## Tools and Libraries

- Python
- pandas
- NumPy
- Matplotlib
- Seaborn
- scikit-learn

## Data Source

The dataset used in this project is the [TMDB 5000 Movie Dataset on Kaggle](https://www.kaggle.com/datasets/tmdb/tmdb-movie-metadata).
