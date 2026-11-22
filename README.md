# OnTarget — Soccer Expected Goals Prediction

OnTarget is a project that estimates the probability that a soccer shot results in a goal using shot location, shooting conditions, and defensive and goalkeeper information.

This probability is called **expected goals (xG)**. For example, a shot valued at 0.25 xG has an estimated 25% chance of resulting in a goal.

## Dataset and Preprocessing

The project uses publicly available [StatsBomb Open Data](https://github.com/hudl/open-data).

Our preprocessing pipeline:

1. Retrieves the original StatsBomb match-event JSON files.
2. Extracts shooting events and excludes penalties.
3. Calculates spatial features, including shooting distance and angle.
4. Extracts defender and goalkeeper information from freeze-frame data.
5. Cleans the records and encodes categorical variables.
6. Generates `ontarget_shots_cleaned.parquet`.

The resulting dataset contains **106,717 non-penalty shots and 54 columns**, of which 37 features are used as MLP inputs.

We use a match-grouped approximately 70/15/15 train/validation/test split, with seed 42 for the main experiment. This prevents shots from the same match appearing in different splits.

**Datasets are not committed to GitHub.** The complete notebook automatically downloads and preprocesses the source data when the cleaned Parquet file is missing.

The original preprocessing implementation is also documented in `DataPreprocessing.ipynb`.

## Model Architecture

The main model is a PyTorch multilayer perceptron (MLP):

`37 input features → 64 neurons (ReLU) → 32 neurons (ReLU) → 1 output logit`

The output logit is converted into a scoring probability using sigmoid during inference.

Training uses:

- Binary cross-entropy with logits (`BCEWithLogitsLoss`)
- Adam optimizer, learning rate 0.001
- Batch size 64
- Maximum 50 epochs, with early stopping patience of 7
- Validation monitoring and saved best-model checkpoint
- StandardScaler fitted only on training data for numerical features

The best model in the primary experiment was selected at epoch 6.

A logistic regression classifier serves as the main baseline. A constant prediction baseline and the StatsBomb xG reference are also included for comparison.

## Running OnTarget

### Google Colab — Complete Reproducible Pipeline

**Main notebook:** `OnTarget_training_Working_Model.ipynb`

1. Open the main notebook in Google Colab.
2. Select **Runtime → Change runtime type** and choose a CPU runtime or GPU runtime.
3. Select **Runtime → Run all**.
4. Allow the notebook to finish preprocessing, training, validation, evaluation and checkpoint saving.
5. Use the interactive estimator in Section 15 after the preceding sections have executed.

**No manual dataset upload is necessary.**

Section 2 automatically checks for `ontarget_shots_cleaned.parquet`. If the file is missing, it retrieves StatsBomb Open Data, runs the integrated preprocessing implementation, generates the cleaned Parquet file, and loads it.

In our fresh CPU test, initial data retrieval and preprocessing took approximately 35 minutes. Subsequent training and experiments require additional computation time. The first execution also requires internet access and sufficient temporary storage.

If the notebook is restarted in a fresh Colab runtime, temporary files may have to be regenerated. If the processed Parquet is already available in the current runtime, the preprocessing stage is skipped.

### Dependencies

The repository includes `requirements.txt` for the Python environment. For local execution, install dependencies with:

`pip install -r requirements.txt`

Python, Git, internet access, and the required packages must be available. Google Colab includes many common machine learning libraries, but dependencies should be checked against the environment file.

## Validation Results

The following results are recorded in `results/val_metrics.json` for the validation split.

| Model | Log Loss ↓ | Brier Score ↓ | ROC-AUC ↑ |
|---|---:|---:|---:|
| Constant baseline | 0.3284 | 0.0912 | 0.5000 |
| Logistic regression | 0.2648 | 0.0760 | 0.8092 |
| OnTarget MLP | **0.2622** | 0.0754 | **0.8120** |
| StatsBomb xG | 0.2638 | **0.0749** | 0.8089 |

The MLP achieves the lowest validation Log Loss and highest ROC-AUC among the compared methods. StatsBomb xG achieves the lowest Brier Score.

These are validation results, not held-out test results. The final test-set evaluation is performed separately in Section 12.

## Controlled Experiments and Analysis

The project includes:

- **Baseline comparison:** Logistic regression, constant prediction, MLP and StatsBomb xG.
- **Calibration analysis:** Comparison of predicted probabilities with observed outcomes.
- **Five-seed robustness testing:** Evaluation of MLP performance stability.
- **Defensive-feature ablation:** Comparison of the full model against versions with defender-count or freeze-frame features removed.
- **Error analysis:** Investigation of prediction performance across different shot conditions.
- **Held-out testing:** Final evaluation on the separate test split.

The five-seed average MLP validation Log Loss values from the ablation experiment are:

| Feature configuration | Average Log Loss ↓ |
|---|---:|
| Full feature set | **0.26209** |
| Without `defenders_in_cone` | 0.26429 |
| Without freeze-frame features | 0.27056 |

These findings suggest that defensive positioning information improves predictive performance.

Saved experiment evidence is available in the `results/` directory:

| File | Contents |
|---|---|
| `val_metrics.json` | Validation metrics and primary model configuration |
| `seed_robustness.json` | Repeated training results across five seeds |
| `ablation.json` | Controlled feature-ablation results |
| `error_analysis_groups.json` | Error analysis by shot conditions |
| `calibration_val.png` | Validation calibration visualization |

The complete notebook contains the executable analysis and learning curve.

## Saved Model Artifacts

Section 13 saves the trained models and supporting files under `models/`:

- `ontarget_mlp.pth` — Trained MLP checkpoint
- `logistic_regression.joblib` — Logistic regression baseline
- `standard_scaler.joblib` — Fitted numerical scaler
- `model_config.json` — Architecture and ordered feature configuration

Section 13.2 verifies that the saved model can be reloaded and packages these files into `OnTarget_models.zip`.

The generated files are written into the execution environment. In Google Colab, these files are temporary unless saved or downloaded separately.

## Interactive Shot Opportunity Estimator

Section 15 provides an interactive interface with controls for:

- Shooting location
- Number of defenders in the shooting cone
- Whether the shooter is under pressure
- Shooting body part

The estimator searches the training data for nearby historical shots matching the selected conditions. It uses the saved MLP to predict each comparable shot's scoring probability and reports the average prediction.

The interface also displays the number of comparable shots, their variation in predicted probability, and available goalkeeper and defensive information.

Shot location and distance are presented using StatsBomb coordinates and approximate meter conversions based on a 105 m × 68 m pitch.

**Important:** The estimate represents the average prediction for comparable historical shots. It is not an exact simulation of a newly created shot.

Section 15 does not retrain the MLP during inference. However, in the current complete notebook, its supporting functions and training reference data are prepared by earlier sections. A separate inference-only demonstration is planned for the final milestone.

## Repository Structure

- `OnTarget_training_Working_Model.ipynb` — Complete end-to-end pipeline and interactive estimator
- `DataPreprocessing.ipynb` — Original standalone preprocessing implementation
- `OnTarget_training.ipynb` — Earlier training and evaluation work
- `results/` — Validation metrics, experiments, analysis and plots
- `requirements.txt` — Environment dependencies
- `CONTRIBUTIONS.md` — Individual responsibilities and AI-tool disclosure
- `README.md` — Setup, execution and project documentation

## Team Contributions

- **Gitae Yang:** Model architecture, training, integration, visualizations and interactive estimator.
- **Arno Nazarian:** Validation comparisons, robustness experiments, ablations and error analysis.
- **Nievethan Thanienayagam:** Dataset research, preprocessing and feature engineering.

Detailed responsibilities are documented in `CONTRIBUTIONS.md`.

## References

- StatsBomb. [Open Data GitHub Repository](https://github.com/hudl/open-data).
- PyTorch. [Official Documentation](https://docs.pytorch.org/).
- scikit-learn. [Official Documentation](https://scikit-learn.org/stable/).