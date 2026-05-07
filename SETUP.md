# Setup & Run Instructions

## Prerequisites

If `python3-venv` is not installed:
```bash
sudo apt install python3-venv python3-full
```

## Step 1 — Create virtual environment
```bash
cd /home/nashmin/Documents/exp/cardiac-risk-prediction
python3 -m venv venv
```

## Step 2 — Install dependencies
```bash
venv/bin/pip install numpy pandas matplotlib seaborn scikit-learn tensorflow shap jupyter
```

## Step 3 — Run the notebook
```bash
venv/bin/jupyter notebook heart-disease-uci.ipynb
```

---

## Windows setup

### Python version requirement

**TensorFlow does not support Python 3.13 or later.** Use Python **3.11 or 3.12**.

To check which versions are installed:
```powershell
py --list
```

If 3.11 or 3.12 is not listed, download it from [python.org/downloads](https://www.python.org/downloads/) — pick the Windows installer for 3.12.x and check "Add Python to PATH" during installation.

### Known issue: MSYS2/MinGW Python

If your venv was created with MSYS2's Python (`C:\msys64\mingw64\...`), package installation will fail with SSL certificate errors. MSYS2's Python lacks pre-built Windows wheels, so pip tries to compile packages from source and the build toolchain (cmake, ninja) fails to download over HTTPS.

**Fix:** delete the venv and recreate it with the Windows Python launcher:

```powershell
Remove-Item -Recurse -Force venv
py -3.12 -m venv venv
venv\Scripts\pip install numpy pandas matplotlib seaborn scikit-learn tensorflow shap jupyter
```

### Step 1 — Create virtual environment (Windows)
```powershell
py -3.12 -m venv venv
```

### Step 2 — Install dependencies (Windows)
```powershell
venv\Scripts\pip install numpy pandas matplotlib seaborn scikit-learn tensorflow shap jupyter
```

### Step 3 — Run the notebook (Windows)
```powershell
venv\Scripts\jupyter notebook heart-disease-uci.ipynb
```

When Jupyter opens in the browser, confirm the kernel shows **Python 3** (top right).  
If not, go to **Kernel → Change Kernel → Python 3**.

---

## Headless execution (no browser)

Runs the notebook and saves all outputs without opening a browser:
```bash
venv/bin/jupyter nbconvert --to notebook --execute --inplace heart-disease-uci.ipynb
```

## Output files

All plots are saved to the `outputs/` folder after running:

| File | Description |
|---|---|
| `class_distribution.png` | Target class balance |
| `correlation_heatmap.png` | Feature correlation matrix |
| `feature_distributions.png` | Key clinical features by risk class |
| `boxplots.png` | Box plots of clinical features vs target |
| `training_history.png` | Loss and accuracy over epochs |
| `confusion_matrix.png` | TP / TN / FP / FN breakdown |
| `roc_curve.png` | ROC curve with AUC score |
| `shap_summary_plot.png` | Global SHAP beeswarm plot |
| `shap_feature_importance.png` | Mean absolute SHAP values (bar) |
| `shap_local_explanation.png` | Waterfall plot for one patient |
