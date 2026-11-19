# Contributions — OnTarget

Team: Gitae Yang (100794460), Arno Nazarian (100868853), Nievethan Thanienayagam (100750591)

This file lists, per member and per milestone, the files, experiments and report sections each member owned. It is kept consistent with the Git history.

---

## Milestone 1 — Proposal (Nov 2)

| Member | Contributions |
|---|---|
| Nievethan Thanienayagam | Created the repository; TODO: proposal sections written |
| Gitae Yang | TODO: proposal sections written |
| Arno Nazarian | TODO: proposal sections written |

---

## Milestone 2 — Technical checkpoint (Nov 20)

### Nievethan Thanienayagam — Data
- `DataPreprocessing.ipynb`: parsing StatsBomb event JSON, shot extraction, penalty exclusion, distance/angle geometry, freeze-frame defender features, cleaning and one-hot encoding, export to `ontarget_shots_cleaned.parquet`
- TODO: anything else

### Gitae Yang — Training
- `OnTarget_training.ipynb` Sections 1–7: data validation, feature selection, match-grouped train/val/test split, standardization, logistic regression baseline, MLP architecture, training loop with early stopping, learning curve
- TODO: anything else

### Arno Nazarian — Evaluation and experiments
- Reviewed and merged pull requests #1 (pre-processing) and #2 (training)
- `OnTarget_training.ipynb` Section 2.1: data loading that works in both Colab and local environments
- `OnTarget_training.ipynb` Section 8: validation comparison of constant baseline, logistic regression, MLP and StatsBomb xG; calibration plots (`results/calibration_val.png`); saved metrics (`results/val_metrics.json`)
- `OnTarget_training.ipynb` Section 9: seed robustness, 5 seeds for each model (`results/seed_robustness.json`)
- `OnTarget_training.ipynb` Section 10: controlled experiment, defender feature ablation with 2 variants × 5 seeds and a paired comparison (`results/ablation.json`)
- `.gitignore`: added Python environment and model weight exclusions
- Checkpoint report: TODO: sections written

---

## Milestone 3 — Final submission (Dec 4)

(To be completed.)

---

## Use of generative AI tools

| Member | Tool | Purpose |
|---|---|---|
| Arno Nazarian | Claude (Anthropic) | Explaining Git and notebook workflows, debugging, explaining evaluation methods, and help drafting and reviewing evaluation and experiment code. All results were produced by running the code in the repository. |
| Gitae Yang | TODO | TODO |
| Nievethan Thanienayagam | TODO | TODO |