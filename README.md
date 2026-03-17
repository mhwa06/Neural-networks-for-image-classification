# Neural Networks for Image Classification (Kidney Histopathology)

This repo contains notebook-based work for **kidney histopathology image patch classification**. The goal is to build machine learning / deep learning models that map an input RGB image patch to one of five clinically relevant classes.

## Task

**What are we predicting?**

Given an image patch, predict its class label:

$$\text{image} \rightarrow \text{tumor class}$$

where the class is one of:

- `Chromo` — Chromophobe renal cell carcinoma
- `Onco` — Oncocytoma
- `ccRCC` — Clear cell renal cell carcinoma
- `pRCC` — Papillary renal cell carcinoma
- `normal` — Healthy kidney tissue

The dataset is split into `train/`, `val/`, and `test/`. The **test set class distribution differs** from the training set, to better simulate real clinical conditions.

## Dataset

### Description

The dataset contains RGB histopathology image patches of kidney tissue organized into training, validation, and test sets. It is intended to support development of models capable of classifying patches into the five diagnostic categories above.

### Structure

```text
Histo images/
|
|-- train/
|   |-- Chromo/
|   |-- Onco/
|   |-- ccRCC/
|   |-- normal/
|   `-- pRCC/
|
|-- val/
|   |-- Chromo/
|   |-- Onco/
|   |-- ccRCC/
|   |-- normal/
|   `-- pRCC/
|
|-- test/
|   |-- Chromo/
|   |-- Onco/
|   |-- ccRCC/
|   |-- normal/
|   `-- pRCC/
|
`-- best_ckpt.pth
```

Examples:

```text
train/ccRCC/0001.png
train/normal/0234.png
train/Onco/1023.png
```

### Image format

- File format: PNG
- Image type: RGB patches
- Resolution: varies (commonly resized to 224×224 for CNNs)

### Checkpoint

`best_ckpt.pth` is a pretrained PyTorch checkpoint (ResNet-based) that can be used for transfer learning or feature extraction.

Note: the dataset and checkpoint are intentionally excluded from git via `.gitignore` to keep the repo lightweight.

## What the notebook does

The main notebooks in this repo (e.g. `starter-notebook.ipynb`) implement a typical end-to-end workflow:

### Part 1 — Dataset inspection and loading

- Load `train/`, `val/`, `test/` from the dataset directory
- Print class counts per split
- Create `torchvision.datasets.ImageFolder` datasets
- Apply standard transforms: resize to `224×224`, `ToTensor()`, and ImageNet normalization
- (Optional) remove corrupted images by attempting to open/verify each file
- Build `DataLoader`s for batching

### Part 2 — Baseline classifier using handcrafted features

- Define a feature extractor `extract_features(img)` for simple hand-crafted cues (e.g., color/texture)
- Build feature matrices for train/val/test
- Train a small classifier in PyTorch (`FeatureClassifier`)
- Evaluate with accuracy + confusion matrices, and compute macro Recall/F1

### Part 3 — Deep feature extraction with a CNN

- Load a ResNet model (`torchvision.models.resnet50`)
- Optionally load pretrained weights from `best_ckpt.pth`
- Use the CNN as a feature extractor to produce deep embeddings
- Train a classifier on top of deep features and evaluate again (metrics + confusion matrices)

## Environment (GPU) setup results

This workspace is configured to run the notebooks with **GPU-enabled PyTorch** using a local virtual environment.

## What was created / used

- Virtual environment: `.venv/` (workspace-local)
- Requirements file: `requirements.txt`
- Notebook kernel: configured to use `.venv (Python 3.13.12)`

## GPU detection result

Command:

```powershell
nvidia-smi
```

Observed (excerpt):

```text
NVIDIA-SMI 582.08    Driver Version: 582.08    CUDA Version: 13.0
GPU 0: NVIDIA RTX A4000 Laptop GPU
```

## Installed dependencies (with GPU support)

Dependencies were installed into `.venv/` from `requirements.txt`.

### PyTorch (CUDA build)

PyTorch was explicitly installed from the **CUDA wheel index**:

- `https://download.pytorch.org/whl/cu128`

Installed versions:

- `torch==2.10.0+cu128`
- `torchvision==0.25.0+cu128`
- `torchaudio==2.10.0+cu128`

### Notebook + ML stack

Installed (high level):

- `ipykernel`
- `numpy`, `matplotlib`, `pillow`
- `scikit-learn`, `seaborn`
- `tqdm`

## Verification results (CUDA working)

Command (run from the workspace venv):

```powershell
& ".\.venv\Scripts\python.exe" -c "import torch; print('torch', torch.__version__); print('cuda', torch.cuda.is_available()); print('device', torch.cuda.get_device_name(0) if torch.cuda.is_available() else 'cpu'); print('torch_cuda', torch.version.cuda)"
```

Observed output:

```text
torch 2.10.0+cu128
cuda True
device NVIDIA RTX A4000 Laptop GPU
torch_cuda 12.8
```

## How to use this environment

### 1) Activate the venv (optional but convenient)

```powershell
.\.venv\Scripts\Activate.ps1
```

### 2) Install/update dependencies

```powershell
python -m pip install -r requirements.txt
```

### 3) Use it in VS Code / Jupyter

- In the notebook, select the kernel named similar to: `.venv (Python 3.13.12)`
- If you open another notebook file in this folder, you may need to select the kernel again.

## Notes

- The first CUDA-enabled PyTorch install can download a **large wheel** (multi-GB). That’s expected for GPU builds.
- If CUDA is ever reported as unavailable inside the notebook, restart the kernel and re-run the verification snippet above.

