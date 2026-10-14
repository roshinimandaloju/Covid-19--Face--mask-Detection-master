# Covid-19 Face Mask Detector

A simple **Face Mask Detection** project built with **Python**, **OpenCV**, and **Keras/TensorFlow**.  
It uses:

- **Haar Cascade** (`haarcascade_frontalface_default.xml`) to check if a face is present in an image.
- A small **CNN classifier** (2 classes) to classify the image as:
  - `with_mask`
  - `without_mask`

The project includes a **Tkinter GUI** (`MaskDetection.py`) to:
1. (Optionally) select a dataset folder,
2. load/train (or load an existing) model,
3. pick a test image and detect mask status,
4. show an accuracy/loss graph from training history.

---

## Repository structure

- `MaskDetection.py` — Tkinter GUI app for training/loading the CNN and testing on images.
- `test.py` — dataset preprocessing + training script (creates `model/X.txt.npy` and `model/Y.txt.npy`, and trains/saves the CNN).
- `run.bat` — Windows helper to start the GUI.
- `dataset/with_mask/` — training images (masked).
- `dataset/without_mask/` — training images (unmasked).
- `testImages/` ��� sample images for testing.
- `model/`
  - `X.txt.npy`, `Y.txt.npy` — saved preprocessed arrays used for training.
  - `model.json`, `model_weights.h5` — saved CNN architecture + weights.
  - `history.pckl` — training history for plotting accuracy/loss.

---

## Requirements

You’ll need:

- Python 3.x
- OpenCV (`cv2`)
- TensorFlow / Keras
- NumPy
- Matplotlib

Example install (recommended: use a virtual environment):

```bash
python -m venv .venv
# Windows:
.venv\Scripts\activate
# macOS/Linux:
# source .venv/bin/activate

pip install opencv-python numpy matplotlib tensorflow
```

Note: the code imports `keras` directly. On modern setups, `keras` is typically provided via `tensorflow`. If you run into `ModuleNotFoundError: No module named 'keras'`, install one of:
- `pip install tensorflow` (usually enough), or
- `pip install keras`

---

## Dataset format

This project expects a folder structure like:

```text
dataset/
  with_mask/
    img1.jpg
    img2.jpg
    ...
  without_mask/
    img1.jpg
    img2.jpg
    ...
```

Class names are inferred from folder names, and the GUI displays the predicted label using those names.

---

## How training works (important)

There are two “stages” in this repo:

### 1) Build training arrays (`X.txt.npy`, `Y.txt.npy`)
`test.py` walks the `dataset/` folders, resizes each image to **64×64**, normalizes pixels to `[0,1]`, shuffles, one-hot encodes labels, and saves:

- `model/X.txt.npy`
- `model/Y.txt.npy`

### 2) Train or load the CNN
Both `test.py` and the GUI (`MaskDetection.py`) will:

- **Load existing model** if `model/model.json` exists, OR
- **Train a new CNN** for 10 epochs and then save:
  - `model/model.json`
  - `model/model_weights.h5`
  - `model/history.pckl`

---

## Run (GUI)

### Windows
Double click `run.bat` or run:

```bat
python MaskDetection.py
```

### Any OS
```bash
python MaskDetection.py
```

In the GUI, typical flow:

1. **Train Mask Images Using CNN**  
   - This loads `model/X.txt.npy` and `model/Y.txt.npy`
   - Then loads an existing model (if present) or trains a new one
2. **Upload Test Image & Detect Mask**  
   - Choose an image (default directory is `testImages/`)
   - If a face is found via Haar Cascade, the CNN predicts `with_mask` / `without_mask`
3. **Accuracy & Loss Graph**  
   - Plots data from `model/history.pckl`

---

## Command-line training (recommended for a fresh start)

If you want to regenerate the training arrays and (re)train:

```bash
python test.py
```

This will:
- preprocess images from `dataset/`
- save `model/X.txt.npy` and `model/Y.txt.npy`
- train the CNN (if no saved model exists) or load the existing one

Tip: to force retraining, delete:
- `model/model.json`
- `model/model_weights.h5`
- `model/history.pckl`

---

## Notes / Limitations

- **Face detection is only used as a gate**: the current GUI code checks whether a face exists, but it classifies the *whole image resized to 64×64* (it does not crop the detected face region).
- The CNN architecture is small and intended for demo/academic usage.
- `test1.py` appears to be a leftover demo script for a different dataset (`flowers/`) and is not required for mask detection.

---

## Troubleshooting

- **Camera / webcam?**  
  This repo currently detects masks from *uploaded images* (file picker), not live webcam frames.
- **`model/X.txt.npy` not found**  
  Run `python test.py` first to generate the training arrays.
- **OpenCV window does not show**  
  Make sure you’re running locally with a GUI environment (not a headless server).
