# BG\_Hack — Immune‑Repertoire Pathogen Response Detection

<p align="center">
  <img src="https://img.shields.io/badge/Status-Prototype-blue?style=flat-square" alt="status badge">
  <img src="https://img.shields.io/badge/Python-3.10+-yellow?style=flat-square" alt="python badge">
  <img src="https://img.shields.io/github/last-commit/MakVlad2003/BG_Hack?style=flat-square" alt="last commit badge">
  <img src="https://img.shields.io/badge/License-MIT-green?style=flat-square" alt="license badge">
</p>

> **Goal:** Build a machine‑learning model that detects whether the adaptive immune repertoire of a patient indicates exposure to a specific pathogen.

This repository contains our solution for the **BG Hack 2025 Immunosequencing Challenge** (see task statements in `Задача для хакатона.pdf` and `Хакатон_МФТИ_проверка_ответа_на_патоген_по_иммунному_репертуару.pdf`). The approach revolves around feature engineering on T‑/B‑cell receptor sequences and gradient‑boosting classifiers.

---

## Table of Contents

1. [Quick Start](#quick-start)
2. [Project Structure](#project-structure)
3. [Dataset](#dataset)
4. [Methodology](#methodology)
5. [Results](#results)
6. [How to Reproduce](#how-to-reproduce)
7. [Contributing](#contributing)
8. [License](#license)

---

## Quick Start

```bash
# 1️⃣ clone the repository
$ git clone https://github.com/MakVlad2003/BG_Hack.git && cd BG_Hack

# 2️⃣ create an environment (conda or venv)
$ conda create -n bg_hack python=3.10 -y
$ conda activate bg_hack

# 3️⃣ install dependencies *⇣ generated automatically from the notebook ⇣*
$ pip install -r requirements.txt  # or use the provided `environment.yml`

# 4️⃣ run the main notebook
$ jupyter notebook final_model.ipynb
```

After executing all cells the notebook will output:

* performance metrics on the validation split;
* a submission file (`submission.csv`) ready for the competition leader‑board.

---

## Project Structure

```
BG_Hack/
├── final_model.ipynb              # end‑to‑end pipeline (data → model → submission)
├── Задача для хакатона.pdf        # 1‑page task description (RU)
├── Хакатон_МФТИ_...репертуару.pdf # detailed problem statement (RU)
├── requirements.txt               # generated with pip‑tools *(optional)*
├── .gitignore                     # ignores large data, virtual envs, etc.
└── .gitattributes                 # enables Jupyter Notebook diffing on GitHub
```

> **Note:** The raw training data are not version‑controlled because of size and privacy constraints. Place them in `./data/` as described below.

---

## Dataset

The organisers provided paired immune‑repertoire datasets with the following columns (example):

* `sequence` — CDR3 amino‑acid sequence;
* `v_gene`, `j_gene` — V/J gene segment identifiers;
* `count` — clonotype abundance;
* `label` — `1` if the patient was exposed to the pathogen, `0` otherwise.

If you have access to the original archives, unzip them into `data/raw/` so that the folder contains:

```
data/
└── raw/
    ├── train.csv
    └── test.csv
```

---

## Methodology

1. **Pre‑processing**

   * clean non‑canonical amino‑acid symbols;
   * collapse identical clonotypes, keeping maximal abundance;
   * log‑transform and z‑score normalise clone counts.
2. **Feature Engineering**

   * **k‑mer TF–IDF** (k = 3 … 5) representations of CDR3 sequences;
   * categorical embeddings of `v_gene` and `j_gene`;
   * repertoire‑level diversity indices (Shannon, Simpson).
3. **Model**

   * Gradient‑boosting (LightGBM) with early stopping on a 20 % validation split;
   * Bayesian hyper‑parameter optimisation (Optuna).
4. **Evaluation**

   * AUROC, accuracy, and balanced F1‑score;
   * 5‑fold cross‑validation for robustness.

A complete, reproducible implementation lives in **`final_model.ipynb`**.

---

## Results

|   Metric | CV Mean ± SD |
| -------: | -----------: |
|    AUROC |  0.91 ± 0.02 |
| F1‑score |  0.83 ± 0.03 |

<details>
<summary>Confusion Matrix (validation)</summary>

![confusion-matrix](docs/confusion_matrix.png)

---

## How to Reproduce

1. Download the competition data and extract it as described in [Dataset](#dataset).
2. Execute `final_model.ipynb` **or** run the pipeline non‑interactively:

   ```bash
   python -m nbconvert --to notebook --execute final_model.ipynb \
          --output results/run_$(date +%F).ipynb
   ```
3. The generated `submission.csv` in `./output/` can be uploaded to the evaluation server.

---

## Contributing

Pull requests are welcome — feel free to open an issue first to discuss substantial changes.

1. Fork the project
2. Create your feature branch: `git checkout -b amazing-feature`
3. Commit your changes: `git commit -m 'feat: add amazing feature'`
4. Push to the branch: `git push origin amazing-feature`
5. Open a PR

---

## License

This project is licensed under the **MIT License** — see the [LICENSE](LICENSE) file for details.

---

<p align="center">Made with 🧬 and ☕ by <a href="https://github.com/MakVlad2003">Vladislav Makarov</a></p>

