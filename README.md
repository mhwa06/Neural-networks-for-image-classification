# Inverse Methods TD 6 — Environment (GPU) Setup Results

This workspace is configured to run the histopathology notebook(s) with **GPU-enabled PyTorch** using a local virtual environment.

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
