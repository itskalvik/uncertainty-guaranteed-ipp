# Near-Optimal Informative Path Planning with Guaranteed Estimation Uncertainty

This repository contains the reference code used in the paper. It implements and benchmarks informative path planning (IPP) methods that **guarantee a target bound on Gaussian-process (GP) posterior variance** over a monitoring region.

The core IPP planners—including **HexCover**, **GreedyCover**, and **GCBCover**—are provided as new modules within the open-source Python package `sgp-tools`.

---

## Repository contents

**Data (SRTM subsets used in the paper’s benchmarks):**

* `N02E021.npy`, `N17E073.npy`, `N45W123.npy`, `N47W124.npy`

**Scripts:**

* `benchmark.py` — run benchmark; saves per-method solution figures and `results.json`.
* `plot.py` — generate metric-vs-threshold plots from `results.json`.

**Notebooks:**

* `demo.ipynb` — minimal usage examples for each IPP method.
* `baselines.ipynb` — reproduces baseline visualizations used in the paper’s introduction.
* `fov_plot.ipynb` — reproduces FoV/coverage-map visualizations used in the methods section.

**Library:**

* `sgp-tools/` — the `sgptools` IPP library (methods, kernels, utilities). **Only** the **HexCover**, **GreedyCover**, and **GCBCover** implementations in `method.py` are original to this repository; the remainder of the library is cloned from the public source.

---

## Setup

### 1) Create an environment

We recommend a fresh Python environment (e.g., `venv` or `conda`).

### 2) Install `sgptools`

From the repository root:

```bash
cd sgp-tools/
python -m pip install -r requirements.txt
python -m pip install -e .
```

This installs the planners and their dependencies (notably **TensorFlow** and **GPflow**). A GPU is not required.

### 3) LaTeX dependency (for PDF text rendering)

Both `benchmark.py` and `plot.py` set `matplotlib.rcParams["text.usetex"] = True` to ensure consistent, publication-quality text in figures. If you do **not** have a LaTeX installation available, either:

* install a LaTeX distribution (recommended for paper-quality plots), **or**
* set `text.usetex = False` in the scripts.

---

## Quick start

### Run a benchmark

Pick a dataset file:

* `./N02E021.npy`
* `./N17E073.npy`
* `./N45W123.npy`
* `./N47W124.npy`

Then run, for example:

```bash
python3 benchmark.py ./N47W124.npy --variance-ratios 0.9 0.8 0.7 0.6 0.5
```

**What this does**

* Fits an initial GP model from an automatically generated pilot path.
* Sweeps target variance thresholds (as *ratios* of the pilot model’s max variance on the evaluation grid).
* Runs each method (default: `HexCover`, `GreedyCover`, `GCBCover`, `GCBCover-Dist`).

  * **Note on `GCBCover-Dist`:** the benchmark script sets the distance budget to *“unconstrained GCBCover path length minus 20 m”*. This creates a meaningful shortfall that stresses the distance-constrained planner.
* Saves solution figures and a `results.json` file.

### Plot benchmark metrics

After `benchmark.py` finishes, it writes:

```
<dataset_stem>/<kernel>/results.json
```

Example:

```bash
python3 plot.py N47W124/Attentive/results.json
```

This produces one PDF per metric (max posterior variance, MSE, SMSE, runtime, num sensing locations, distance), saved under the dataset-named folder.

---

## Reproducing paper figures

* **Benchmark curves (e.g., max variance / distance vs. variance ratio):**

  1. run `benchmark.py` over the desired ratio sweep, then
  2. run `plot.py` on the produced `results.json`.

* **Method and FoV visualizations:**

  * `fov_plot.ipynb` generates the field-of-view / coverage-map visuals used in the paper’s method section.

* **Introduction comparison plot(s):**

  * `baselines.ipynb` reproduces baseline method visualizations.

* **Method API walkthrough:**

  * `demo.ipynb` shows how to instantiate and run the planners directly.

---

## Notes on reproducibility

* `benchmark.py` fixes random seeds for NumPy and TensorFlow (`1234`) for repeatability.
* If you run on GPU, TensorFlow may still exhibit nondeterminism depending on your stack.

---
