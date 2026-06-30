# LRFIO Reproduction Notebook

This repository contains the companion notebook for the paper:

**Learned Response-Field Inertia Operator for HEC-RAS 2D Water-Surface Elevation Prediction**

The notebook reproduces the core LRFIO experiment from the paper. It is intentionally focused on the LRFIO workflow rather than the full comparison/model-zoo used during development, so the repository stays easy to inspect and rerun.

## What is included

The notebook demonstrates the main LRFIO pipeline:

1. Load native-cell HEC-RAS water-surface elevation data.
2. Form the current WSE increment,  
   $$
   \Delta W_t = W_t - W_{t-1}
   $$
3. Fit/select the retained LRFIO response case:
   - `H0`: persistence
   - `H1`: global calibrated inertia
   - `H2`: segmented response-field inertia
4. Roll out future WSE directly on the native HEC-RAS computational cells.
5. Report the main accuracy and runtime summaries.

The goal is to reproduce the LRFIO result path cleanly, without extra experimental branches that are not needed to understand the method.

## Repository contents

```text
.
├── LRFIO_reproduction.ipynb
├── README.md
└── .gitignore
```

The notebook name may differ slightly depending on the final repository version.

## Environment

The notebook was written for Python 3. A minimal local setup is:

```bash
python3 -m venv .venv
source .venv/bin/activate

python -m pip install --upgrade pip
pip install numpy pandas scipy scikit-learn matplotlib jupyter
```

Then start Jupyter:

```bash
jupyter notebook
```

or:

```bash
jupyter lab
```

## Data

This repository is intended for the reproduction code. Large HEC-RAS project files and derived native-cell arrays are not tracked here unless explicitly added.

To run the notebook, place the required prepared inputs locally and update the data path near the top of the notebook, for example:

```python
DATA_DIR = "path/to/prepared/lrfio/data"
```

The paper evaluates LRFIO on four HEC-RAS 2D benchmark datasets:

- Beaver Bayou
- Upper San Saba River
- Lower San Saba River
- Tuttle Creek / Big Blue / Kansas River

Some source datasets may have separate access or redistribution terms. The notebook assumes the user has access to the required prepared inputs.

## Expected result

The strict LRFIO reproduction should recover the retained response cases and held-out test summaries reported in the paper:

| Dataset | Retained case | Stage RMSE (m) | Final RMSE (m) |
|---|---:|---:|---:|
| Beaver Bayou | `H2` segmented | 0.082421 | 0.143701 |
| Upper San Saba River | `H1` global | 0.006830 | 0.010248 |
| Lower San Saba River | `H0` persistence | 0.000060 | 0.000070 |
| Tuttle Creek / Big Blue / Kansas River | `H1` global | 0.133756 | 0.154906 |

Small numerical differences can occur depending on platform, package versions, and whether the exact same prepared arrays are used.

## Method summary

LRFIO is a no-forcing, increment-based surrogate for HEC-RAS 2D water-surface elevation prediction. Instead of rasterizing the domain, the method works directly on the native HEC-RAS computational cells.

At forecast time, LRFIO uses the current and previous WSE fields to compute the latest native-cell increment. A learned response rule then propagates that increment forward over the forecast horizon using a compact closed-form rollout.

In the strict version used here, the model selects the simplest response structure supported by validation evidence:

- persistence,
- global calibrated inertia, or
- segmented response-field inertia.

This keeps the retained model small while still allowing spatially varying response behavior when the validation evidence supports it.

## Running the notebook

After installing the environment and setting the data path:

1. Open `LRFIO_reproduction.ipynb`.
2. Run the setup/import cells.
3. Confirm the dataset paths.
4. Run the LRFIO calibration and selection cells.
5. Run the held-out evaluation cells.
6. Compare the printed tables with the expected result table above.

## Notes

This repository is meant to reproduce the LRFIO-focused result, not the full paper development environment. The manuscript also discusses additional model families, ablations, and extended learned-inertia variants. Those are intentionally left out here to keep the reproduction code narrow and readable.

## Citation

If this code helps your work, please cite the paper once the final publication details are available.

```bibtex
@article{holmberg2026lrfio,
  title   = {Learned Response-Field Inertia Operator for HEC-RAS 2D Water-Surface Elevation Prediction},
  author  = {Holmberg, Edward and Ioup, Elias and Ferdaus, Md Meftahul and Abdelguerfi, Mahdi and Simeonov, Julian},
  journal = {IEEE Transactions on [Journal Name]},
  year    = {2026},
  note    = {Manuscript under review}
}
```

## License

The source code and notebook in this repository are licensed under the MIT License. See `LICENSE`.

This license applies only to the code and notebook materials provided in this repository. It does not grant rights to third-party datasets, HEC-RAS project files, manuscript text, publisher-formatted paper content, or other materials not owned by the repository author.

The benchmark datasets used by the paper have their own access and redistribution terms. In particular, the Beaver Bayou dataset was privately provided, while the San Saba and Tuttle Creek datasets come from separate public dataset sources.
