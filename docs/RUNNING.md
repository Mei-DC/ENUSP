# Running ENUSP

These commands apply to the complete source package received by email. The
public GitHub repository contains documentation and application materials.

## Setup

Use Python 3.10 or newer with a compatible PyTorch installation. For GPU use,
install the PyTorch build that matches your CUDA environment, then install the
remaining requirements:

```bash
python -m venv .venv
# Windows: .venv\Scripts\activate
# Linux/macOS: source .venv/bin/activate
python -m pip install -r requirements.txt
```

The package has two runtime dependencies: PyTorch and NumPy. It starts from
precomputed DRAI `.npy` files. See [DATA.md](DATA.md) before training.

## Train

Run from the extracted `ENUSP` directory:

```bash
python train.py --data "/path/to/drai" --output runs/enusp --seed 2024
```

The default configuration is in `config.json`. `--device cpu` selects the CPU;
otherwise CUDA is used when available. `--workers 0` is the portable default,
including on Windows. Use a new output directory for each experiment.

Training creates:

```text
runs/enusp/
  config.json       Effective training configuration
  run.json          Split counts, versions, and manifest hash
  split.csv         Exact sample membership, order, and labels
  history.jsonl     One training/validation record per epoch
  best.pt           Highest validation-accuracy checkpoint
  last.pt           Final completed epoch
```

Only training and validation tensors are loaded during training. The target
test split is reserved for the separate evaluation command. Split membership
is fixed; the seed controls initialization, training order, and augmentation.
To reuse a saved split, pass `--manifest runs/enusp/split.csv` with a new output
directory. Historical manifests with a different class-index order are rejected.

## Evaluate

```bash
python evaluate.py --data "/path/to/drai" --checkpoint runs/enusp/best.pt
```

Evaluation writes `metrics.json`, `predictions.csv`, and `confusion_matrix.csv`
under `runs/enusp/evaluation`. Rows of the confusion matrix are true classes;
columns are predicted classes. Accuracy, balanced accuracy, precision, recall,
and F1 use fractions in `[0,1]`; the console displays percentages.

The evaluator checks that the split manifest matches the hash stored during
training and loads all model weights strictly. Use checkpoints produced by this
package. Old checkpoints with different layer dimensions require an explicit
architecture audit and are not silently adapted.

## Files

```text
ENUSP/
  enusp/
    model.py         Encoder, gates, factorization, and prediction heads
    losses.py        Orthogonality, GRL-related losses, and ALB
    data.py          Dataset, deterministic split, and sequence reversal
    engine.py        Training and evaluation operations
    __init__.py
  train.py
  evaluate.py
  config.json
  requirements.txt
  docs/
```

The default recipe and known manuscript/implementation differences are recorded
in [METHOD.md](METHOD.md). A successful smoke test confirms execution, not the
manuscript's reported recognition accuracy.
