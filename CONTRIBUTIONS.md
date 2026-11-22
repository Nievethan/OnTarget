# Contributions — OnTarget

**Team Members:**
- Gitae Yang (100794460)
- Arno Nazarian (100868853)
- Nievethan Thanienayagam (100750591)

This document summarizes each member's contributions to the OnTarget project, including their implementation, experiments, and documentation responsibilities.

---

## Milestone 1 — Proposal (November 2, 2026)

| Member | Contributions |
|---|---|
| Nievethan Thanienayagam | Repository setup, dataset research, and preprocessing planning. |
| Gitae Yang | Project concept, MLP architecture planning, training methodology, and evaluation strategy. |
| Arno Nazarian | Baseline comparison planning, evaluation methodology, and controlled experiment design. |

---

## Milestone 2 — Technical Checkpoint (November 20, 2026)

### Nievethan Thanienayagam — Data Preprocessing

**File:** `DataPreprocessing.ipynb`

- Extracted and cleaned StatsBomb shooting events, including penalty exclusion and missing-value handling.
- Engineered shooting distance, angle, defender, and goalkeeper positioning features from event and freeze-frame data.
- Encoded categorical variables and generated the processed dataset of 106,717 shots.
- Prepared the dataset for model training and reproducible preprocessing.

### Gitae Yang — MLP Training, Integration, and Visualization

**Files:** `OnTarget_training.ipynb` (Sections 1–7), `OnTarget_training_Working_Model.ipynb` (complete Sections 1–15)

- Implemented feature selection, leakage prevention, match-grouped 70/15/15 splitting, and train-only standardization.
- Developed the logistic regression baseline and PyTorch MLP (37–64–32–1), including loss function, optimizer, mini-batch training, validation, early stopping, and learning curves.
- Implemented model checkpoint saving, configuration storage, reloading, and inference verification.
- Integrated the completed training, evaluation, experiments, and final held-out testing into one working notebook.
- Developed soccer-specific visualizations, including shot-location plots, xG heatmaps, and analysis of distance, angle, and defensive positioning.
- Built the interactive shot estimator with position sliders, defender/pressure/body-part controls, comparable-shot matching, and live goal-probability estimates.
- Analyzed final test results and prepared the integrated notebook for the checkpoint submission.

### Arno Nazarian — Evaluation and Experiments

**Files:** `OnTarget_training.ipynb` (Sections 8–11), `results/`, `.gitignore`

- Implemented validation comparisons between constant baseline, logistic regression, MLP, and StatsBomb xG using Log Loss, Brier Score, and ROC-AUC.
- Generated calibration plots and saved validation results.
- Conducted five-seed robustness experiments to evaluate training consistency.
- Implemented controlled defensive-feature ablation experiments, including repeated runs and comparisons.
- Developed error analysis to investigate prediction failures and model limitations.
- Improved data-loading compatibility, organized experiment outputs, and reviewed/merged team pull requests.

---

## Use of Generative AI Tools

| Member | Tool | Purpose |
|---|---|---|
| Gitae Yang | ChatGPT (OpenAI) | Code development and debugging assistance, visualization and interactive estimator design, result interpretation, and documentation review. |
| Arno Nazarian | Claude (Anthropic) | Git and notebook assistance, debugging, evaluation methodology, and experiment code review. |
| Nievethan Thanienayagam | To be confirmed | Confirm AI tool use and purpose, if applicable. |

All team members are responsible for reviewing, testing, and understanding their contributions. Experimental results are based on executed project code.