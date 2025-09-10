# aviation-challenge

Machine learning algorithm utilized to aid aviation challenge team, taking a few test runs as training and extrapolating that data to find candidates for the best combination of airplane parameters to achieve the best score for the simulation.

## Project Structure

- **`data.ipynb`** - Data preprocessing and cleaning pipeline
- **`ml.ipynb`** - Machine learning model training and evaluation
- **`test.ipynb`** - Monte Carlo optimization to find best aircraft configurations
- **`y_test.csv`** - Actual scores from test set
- **`y_pred.csv`** - Predicted scores from trained model
- **`formatted_data.csv`** - Cleaned training data (generated)
- **`results.csv`** - Optimized aircraft configurations (generated)
- **`model.pkl`** - Trained Random Forest model (generated)

## Aircraft Parameters

The system optimizes the following aircraft design parameters:

### Engine & Configuration
- **Horsepower** (400-1000 HP in 25 HP increments)
- **Extra Propeller** (0 or 1)
- **Retractable Landing Gear** (0 or 1)

### Weight Parameters
- **Empty Weight** (calculated based on other parameters)
- **Payload Weight** (1700-6000 lbs in 300 lb increments)
- **Fuel Load** (fixed at 300 lbs for 40 min flight)
- **Total Weight** (must not exceed 8000 lbs)

### Wing Design
- **Wing Semi-Length** (25-60 ft in 5 ft increments)
- **Wing Root Chord** (2.0-6.5 ft in 0.3 ft increments)
- **Wing Tip Chord** (2.0-6.5 ft in 0.3 ft increments)
- **Wing Area** (calculated)
- **Aspect Ratio** (calculated)

### Performance Metrics
- **Wing Loading** (Total Weight / Wing Area)
- **Power Loading** (Total Weight / Horsepower)

## Methodology

### 1. Data Preprocessing (`data.ipynb`)
- Loads raw CSV data with aircraft configurations and scores
- Removes unnamed columns and invalid entries
- Filters for valid score entries only
- Calculates derived parameters (wing area, aspect ratio, etc.)

### 2. Model Training (`ml.ipynb`)
- Uses Random Forest Regressor with hyperparameter optimization
- Performs 5-fold cross-validation with 100 random search iterations
- Achieves ~81% R² score on test set
- Average prediction error: ~8.3%
- Maximum prediction error: ~16.1%

### 3. Optimization (`test.ipynb`)
- Runs 1,000,000 Monte Carlo simulations
- Tests random combinations within parameter constraints
- Filters results to top 20% of maximum observed score
- Returns ranked list of optimal configurations

## Usage

### Requirements
```python
pandas
scikit-learn
numpy
scipy
```

### Running the Pipeline

1. **Prepare Data**:
   ```bash
   jupyter notebook data.ipynb
   ```
   Ensure your raw data is in `data.csv` format.

2. **Train Model**:
   ```bash
   jupyter notebook ml.ipynb
   ```
   This will generate `model.pkl` and performance metrics.

3. **Find Optimal Configurations**:
   ```bash
   jupyter notebook test.ipynb
   ```
   This generates `results.csv` with top-performing aircraft configurations.

## Model Performance

The trained Random Forest model achieves:
- **R² Score**: 0.809 (81% variance explained)
- **Mean Absolute Percentage Error**: 8.28%
- **Maximum Percentage Error**: 16.08%

## Results

The optimization process identifies aircraft configurations achieving scores ≥80% of the maximum observed score. Results are sorted by predicted score and include all design parameters plus calculated performance metrics.

## Key Features

- **Automated parameter calculation**: Derives wing area, aspect ratio, empty weight, and performance metrics
- **Constraint enforcement**: Ensures total weight ≤ 8000 lbs
- **Monte Carlo optimization**: Explores vast parameter space efficiently
- **Performance filtering**: Returns only high-scoring configurations
- **Reproducible pipeline**: End-to-end workflow from raw data to optimized designs

## Applications

This tool assists aviation challenge teams by:
- Reducing the number of physical test flights needed
- Identifying promising parameter combinations quickly
- Providing quantitative guidance for design decisions
- Enabling rapid iteration on aircraft configurations

## Notes

- The model assumes a fixed longitudinal arm of 13 ft for all configurations
- Fuel load is standardized at 300 lbs (40-minute flight capacity)
- Wing lateral arm is fixed at 2 ft
- Sweep angle is set to 0° for all configurations
- Empty weight calculation includes adjustments for extra propeller and retractable landing gear
