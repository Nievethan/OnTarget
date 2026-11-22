# OnTarget — Soccer Expected Goals Prediction

OnTarget is a deep learning project developed for ARTE 4110U at Ontario Tech University. It predicts the probability that a soccer shot results in a goal using shot location, shooting conditions, goalkeeper positioning, and defensive features.

This probability is called **expected goals (xG)**. For example, a shot with 0.25 xG represents an estimated 25% chance of scoring.

## Dataset

We use the publicly available [StatsBomb Open Data](https://github.com/hudl/open-data).

Our preprocessing extracts non-penalty shots, calculates spatial and defensive features, and produces a cleaned dataset containing 106,717 shots and 54 columns.

The MLP uses 37 input features. We split the dataset approximately 70% for training, 15% for validation, and 15% for testing, grouped by match ID to prevent match-level leakage.

**No datasets are committed to this repository.** The main notebook automatically downloads the raw StatsBomb data and performs preprocessing when the cleaned dataset is unavailable.

The original preprocessing implementation is also documented in `DataPreprocessing.ipynb`.

## Model Architecture

OnTarget uses a PyTorch multilayer perceptron (MLP):

`37 input features → 64 neurons (ReLU) → 32 neurons (ReLU) → 1 output logit`

The final logit is converted into a goal probability using a sigmoid function during inference.

Training uses binary cross-entropy with logits, Adam optimization, validation monitoring, and early stopping.

A logistic regression classifier provides a baseline for comparison.

## Running the Project

### Google Colab

1. Open `OnTarget_training_Working_Model.ipynb` in Google Colab.
2. Connect to a runtime. CPU is supported; GPU acceleration is optional.
3. Select **Runtime → Run all**.

Section 2 automatically obtains StatsBomb Open Data, performs preprocessing, and generates `ontarget_shots_cleaned.parquet` when the file does not exist.

The remaining sections execute data splitting, feature scaling, baseline and MLP training, validation, evaluation, checkpoint saving, visualizations, and the interactive estimator.

**Note:** The initial source-data download and preprocessing may take substantial time. Our complete CPU preprocessing test took approximately 35 minutes. Training and repeated experiments require additional computation.

### Dependencies

Install the Python dependencies specified in `requirements.txt` when running locally. Google Colab may already provide some required libraries.

## Validation Results

| Model | Log Loss | Brier Score | ROC-AUC |
|---|---:|---:|---:|
| Constant baseline | 0.3284 | 0.0912 | 0.5000 |
| Logistic regression | 0.2650 | 0.0760 | 0.8085 |
| OnTarget MLP | 0.2622 | 0.0754 | 0.8120 |
| StatsBomb xG | 0.2638 | 0.0749 | 0.8089 |

These results are from the validation split. Final held-out test results are evaluated separately.

## Experiments

The project includes validation comparisons, calibration analysis, five-seed robustness testing, defensive-feature ablation, error analysis, and held-out test evaluation.

Learning curves and soccer-related visualizations are available in the complete notebook.

## Saved Models

Section 13 generates the model checkpoints and supporting configuration:

- `ontarget_mlp.pth`
- `logistic_regression.joblib`
- `standard_scaler.joblib`
- `model_config.json`

Section 13.2 verifies that the saved models can be reloaded.

## Interactive Estimator

Section 15 provides controls for selecting a shooting position, defender count, pressure condition, and shooting body part.

The estimator finds comparable historical training shots and uses the saved MLP to calculate their predicted scoring probabilities. It displays the average prediction, the number of matching shots, and recorded defensive and goalkeeper conditions.

The selected shooting position defines a search area around historical examples rather than an exact simulated shot.

## Team

- Gitae Yang — Model architecture, training pipeline, integration, and interactive visualizations.
- Arno Nazarian — Validation, robustness experiments, ablation, and error analysis.
- Nievethan Thanienayagam — Dataset research, extraction, cleaning, and feature engineering.

See `CONTRIBUTIONS.md` for detailed responsibilities.

## References

- StatsBomb, [Open Data GitHub Repository](https://github.com/hudl/open-data).
- PyTorch, [Documentation](https://docs.pytorch.org/).
- scikit-learn, [Documentation](https://scikit-learn.org/stable/).