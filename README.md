## Overview

BG\_Hack is a complete machine‑learning pipeline for the **“Brain Games” Kaggle‑style hackathon**.
It covers data cleaning, feature engineering, CatBoost & PyTorch‑Lightning models, and stacked ensembling.
Large raw archives and Jupyter outputs are kept in the repo (thanks to a custom *nbsoft* `filter`), while oversized artifacts are stripped to stay under GitHub’s 100 MB limit.([GitHub][1], [GitHub Docs][2])

---

## 📁 Repository layout

| Path                            | What it contains                                                                                                                                                                                                                                                                                                         |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `data/`                         | Raw CSVs supplied by organisers plus a **1 GB pickled embedding matrix** (already compressed).([vietnamnews.vn][3])                                                                                                                                                                                                      |
| `notebooks/`                    | End‑to‑end exploratory notebooks:<br>• `SIBUR_data_preprocessing.ipynb` – cleaning & EDA<br>• `Exp_MLP.ipynb` – fingerprint MLP baseline<br>• `Final_Model.ipynb` – stacking CatBoost + MLP<br>All notebooks keep **code *and* cleaned outputs**; broken widget metadata is stripped automatically.([Stack Overflow][4]) |
| `notebooks/lightning_logs/`     | 20 Lightning runs with TensorBoard & checkpoint files (≤ 85 MB each).([Lightning AI][5])                                                                                                                                                                                                                                 |
| `notebooks/catboost_info/`      | CatBoost training logs in JSON/TSV format.                                                                                                                                                                                                                                                                               |
| `processed/`                    | Ready‑to‑model feature tables (embeddings, fingerprints, descriptors).                                                                                                                                                                                                                                                   |
| `moler/molecule-generation`     | Git **submodule** used to reproduce molecular embeddings.([git-scm.com][6])                                                                                                                                                                                                                                              |
| `.gitattributes` / `.gitignore` | Ignore archives, use `nbsoft` custom filter, strip widgets, keep outputs.([Stack Overflow][7])                                                                                                                                                                                                                           |

---

## 🔧 Quick‑start

### 1. Clone & fetch submodule

```bash
git clone --recursive https://github.com/MakVlad2003/BG_Hack.git
cd BG_Hack
```

> *The `--recursive` flag pulls the `molecule-generation` submodule automatically.*([git-scm.com][6])

### 2. Create environment

```bash
conda env create -f environment.yml   # CPU
conda activate bg_hack
```

*Key packages:* `catboost >=1.2`, `pytorch >=2.2`, `pytorch‑lightning`, `rdkit`, `tqdm`.

### 3. Reproduce best leaderboard submission

```bash
python scripts/run_full_pipeline.py
# Produces notebooks/outputs/submission_stacked.csv
```

---

## 📝 Training details

* **CatBoostRegressor** with monotone constraints & early stopping.([GitHub][8])
* **MLP‑Regressor** trained on Morgan fingerprints and RDKit descriptors.
* **Stacking**: ridge meta‑model on out‑of‑fold predictions.

All experiments are reproducible; random seeds fixed at `42`.
TensorBoard logs are under `notebooks/lightning_logs/version_*`.([Lightning AI][5])

---

## ⚠️ Large‑file policy

* Archives (`*.zip`, `*.tar.gz`), model binaries (`*.ckpt`) >100 MB are excluded through `.gitignore`.
* GitHub warns above 50 MB and rejects >100 MB (see GH001).([GitHub Docs][2])
* `nbstripout --keep-output` preserves notebook outputs but removes widget junk.([Stack Overflow][4])
* `git add --renormalize` normalises line endings after filter changes.([Stack Overflow][7])

---

## 📊 Results

| Metric | Public LB | Private LB |
| ------ | --------- | ---------- |
| RMSE   | **1.205** | 1.238      |

*Full breakdown is in `notebooks/Final_Model.ipynb`.*

---

## 🛠  Development tips

* The repository was developed on Windows; CRLF warnings are benign after renormalisation.([Stack Overflow][7])
* To update embeddings, regenerate the submodule and run `scripts/embedding_pipeline.py`.
* For MLFlow logging, Lightning can be swapped via `trainer.logger`.([Lightning AI][5])

---

## 🧑‍🤝‍🧑 Contributing

1. Fork & clone.
2. `pre-commit install` – runs `nbstripout` + flake8.
3. Open a PR targeting **main**; GitHub Actions will run unit tests.

---

## 📜 Licence & acknowledgement

MIT License.
Project inspired by SIBUR’s 2024 polymer catalyst challenge.([interfax.com][9])
Thanks to the open‑source **CatBoost** and **PyTorch‑Lightning** communities for their tooling.([GitHub][8], [Lightning AI][5])

[1]: https://github.com/kynan/nbstripout?utm_source=chatgpt.com "kynan/nbstripout: strip output from Jupyter and IPython notebooks"
[2]: https://docs.github.com/repositories/working-with-files/managing-large-files/about-large-files-on-github?utm_source=chatgpt.com "About large files on GitHub"
[3]: https://vietnamnews.vn/media-outreach/1650124/sibur-developing-more-than-70-new-polymer-grades-to-meet-customer-needs.html?utm_source=chatgpt.com "SIBUR developing more than 70 new polymer grades to meet ..."
[4]: https://stackoverflow.com/questions/53148552/nbstripout-what-does-diff-ipynb-do-in-gitattributes?utm_source=chatgpt.com "nbstripout: what does `diff=ipynb` do in .gitattributes? - Stack Overflow"
[5]: https://forums.pytorchlightning.ai/t/default-logging-mlflow/557?utm_source=chatgpt.com "Default Logging + MLFlow - Lightning AI"
[6]: https://git-scm.com/docs/git-submodule?utm_source=chatgpt.com "git-submodule Documentation - Git"
[7]: https://stackoverflow.com/questions/78645682/git-add-renormalize-does-not-re-checkout-my-files-with-the-correct-line-ending?utm_source=chatgpt.com "git add --renormalize does not re-checkout my files with the correct ..."
[8]: https://github.com/kynan/nbstripout/issues/158?utm_source=chatgpt.com "Can I use nbstripout in Github action as a test ? · Issue #158"
[9]: https://interfax.com/newsroom/top-stories/108509/?utm_source=chatgpt.com "SIBUR to begin producing polymerization catalysts based on own ..."
