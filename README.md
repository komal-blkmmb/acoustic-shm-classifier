# 🔊 Acoustic SHM Classifier

> A browser-based machine learning tool for structural health monitoring of sandwiched composite plates using acoustic percussion signals.

**[▶ Launch App](https://komal-blkmmb.github.io/acoustic-shm-classifier/)**

---

## What is this?

This tool classifies **healthy vs. debonded (unhealthy) cells** in a sandwiched composite plate by analyzing the sound produced when each cell is tapped. It runs entirely in your browser — no Python, no installation, no data ever leaves your machine.

The full pipeline goes from raw `.wav` files → signal processing → feature extraction → model training → results, all in one page.

---

## How to Use

### Step 1 — Mark your unhealthy cells
Click on the interactive **3×9 plate grid** to mark which cells are debonded. Marked cells turn red. You can clear and re-select at any time.

### Step 2 — Upload your recordings
Drag and drop all your `.wav` tap files onto the upload zone. The app auto-labels each file based on its name and the cells you marked in Step 1.

**Supported filename formats:**
```
1d_hit1.wav       →  row 1, column d, tap 1
s_2_g.wav         →  row 2, column g
s_1_d_3.wav       →  row 1, column d, tap 3
```

### Step 3 — Configure and train
Adjust the train/test split, k value for KNN, and random seed. Hit **▶ TRAIN**.

### Step 4 — Read your results
Side-by-side results for both models show:
- Train and test accuracy
- Color-coded confusion matrix
- Precision, Recall, F1-score per class

---

## Features

- **No installation** — single HTML file, works in any modern browser
- **Interactive plate map** — click to define your own unhealthy cells, works for any plate configuration
- **Two classifiers** — K-Nearest Neighbors (KNN) and Decision Tree (CART with Gini impurity)
- **Full signal processing pipeline** in JavaScript:
  - Stereo → mono conversion
  - Resampling to 48 kHz
  - Mean-centering and normalization
  - 200ms fixed-length windowing
- **154-dimensional feature vector** per tap:
  - 128 Welch PSD bins (log-compressed)
  - 26 MFCC features (13 coefficients × mean + std)
- **Stratified train/test split** to preserve class balance
- **Privacy-first** — all processing is client-side, no files are uploaded to any server

---

## Plate Setup

The plate is a **3 rows × 9 columns** grid:

```
     a    b    c    d    e    f    g    h    i
1  [ ]  [ ]  [ ]  [ ]  [ ]  [ ]  [ ]  [ ]  [ ]
2  [ ]  [ ]  [ ]  [ ]  [ ]  [ ]  [ ]  [ ]  [ ]
3  [ ]  [ ]  [ ]  [ ]  [ ]  [ ]  [ ]  [ ]  [ ]
```

Cells are identified by row + column (e.g. `2g`, `1d`). Click any cell in the app to mark it as debonded before uploading files.

---

## Data Collection Guidelines

| Condition | Taps per cell | Reasoning |
|-----------|--------------|-----------|
| Healthy   | 10           | Baseline |
| Unhealthy | 24           | More samples to compensate for fewer unhealthy cells |

Record each tap as a separate `.wav` file and name them consistently using one of the supported formats above.

---

## Signal Processing Pipeline

```
.wav file
   │
   ├─ Stereo → Mono (channel average)
   ├─ Mean-center + Normalize
   ├─ Resample → 48,000 Hz
   ├─ Window → 200ms (zero-pad / truncate)
   │
   ├─ Welch PSD  →  128 bins  →  log1p compress
   └─ MFCC       →  13 coeffs × (mean + std)  →  26 values
                                                       │
                                              154-dim feature vector
```

---

## Models

### K-Nearest Neighbors (KNN)
- Distance metric: Euclidean
- Configurable k (default: 5)
- Instance-based, non-parametric

### Decision Tree (CART)
- Split criterion: Gini impurity
- Full depth (no pruning)
- Typically overfits — useful as a baseline comparison

---

## Example Results

Results from a 3×9 composite plate with 8 debonded cells (382 total tap samples, 70/30 split):

| Model | Train Accuracy | Test Accuracy | Unhealthy Recall |
|-------|---------------|---------------|-----------------|
| KNN (k=5) | 92.1% | 85.2% | 89.7% |
| Decision Tree | 100.0% | 78.3% | 75.9% |

KNN consistently generalizes better. The Decision Tree's 100% train accuracy is a classic overfitting signal.

---

## Running Locally

No build step needed. Just open the file:

```bash
# Option 1 — open directly
open index.html

# Option 2 — serve locally (recommended, avoids Web Audio API restrictions)
npx serve .
# or
python -m http.server 8000
```

Then visit `http://localhost:8000` in your browser.

---

## Browser Compatibility

| Browser | Support |
|---------|---------|
| Chrome / Edge | ✅ Full support |
| Firefox | ✅ Full support |
| Safari | ✅ Full support |
| Mobile (Chrome/Safari) | ✅ Works |

---

## Project Context

Developed as part of **Machine Learning (Spring 2026) — HW2: KNN and Decision Tree with Hands-on Experiments**, using Lab-to-Go percussion experiments on sandwiched composite plates for structural health monitoring.

---

## License

MIT — free to use, modify, and share.
