# Toy training and test

Train DuetScreen in Docker and use the checkpoint selected during this run to score 40 labelled records. The [toy archive](https://doi.org/10.5281/zenodo.23538085) includes the toy training views and protein features. Download the [NT-V3 pretrained encoder](https://doi.org/10.5281/zenodo.23535423) separately and extract it under `assets/NTv3_8M_pre/` so that `model.safetensors` is directly in that folder. Build the image from the current repository with `docker build -t duetscreen-release:1.0 .`. The runner uses a CUDA-enabled GPU.

## Install the resources

Use the current [DuetScreen repository](https://github.com/bencaocs/DuetScreen) for the code and run the commands below from its root.

Linux / WSL2 / Git Bash:

```bash
unzip -o DuetScreen_ToySubset.zip 'data/*' 'cache/*' -d toy/
```

Windows PowerShell:

```powershell
Expand-Archive DuetScreen_ToySubset.zip -DestinationPath toy_tmp
Copy-Item -Path toy_tmp\data,toy_tmp\cache -Destination toy\ -Recurse -Force
Remove-Item toy_tmp -Recurse -Force
```

Check that the following files are present:

```
toy/
  README.md          # repository version, not from the archive
  run_toy.sh         # repository version
  run_toy.ps1        # repository version
  data/
    train_toy.fasta
    val_toy.fasta
    test_toy.fasta
  cache/
    variants_data_toy.pkl
    protein_seg_cache_toy.pkl
```

## Data

| Split | Source records | Positive | Negative |
|---|---:|---:|---:|
| Train | 960 | 480 | 480 |
| Validation | 240 | 120 | 120 |
| Test | 40 | 20 | 20 |

The FASTAs are fixed samples from the corresponding mixed-pool splits. The 40 test records are identical to those in the original toy archive. The view cache contains 1,057 training segments and 274 validation segments. The packaged protein cache covers their original sequences and all 40 test records. Augmented views use the zero-input fallback for the protein stream. The archive includes a data card and per-file SHA-256 checksums.

## Run

From the repository root, run one command:

```powershell
.\toy\run_toy.ps1
```

On Linux or WSL2:

```bash
./toy/run_toy.sh
```

If Windows PowerShell blocks scripts, use `powershell -ExecutionPolicy Bypass -File .\toy\run_toy.ps1`. The expanded run uses seed 42, batch size 16, up to 12 epochs and early-stopping patience 5. Validation combined accuracy selects the checkpoint. A second container loads it and predicts the test set at threshold 0.5.

| Output | Path |
|---|---|
| Selected checkpoint | `toy/out/expanded/seed42_toy/best_nucleotide_v3_mamba_model.pth` |
| Optimizer updates by epoch | `toy/out/expanded/seed42_toy/progress.jsonl` |
| Test predictions | `toy/out/expanded/test_pred/predictions_threshold_0.5.csv` |
| Test metrics | `toy/out/expanded/test_pred/detailed_results.json` |

See [the verification record](../CHECKS.md) for the recorded run. Exact scores can vary by GPU environment. This 40-record test is a demonstration of the training and prediction workflow.

### Original toy archive

If you have an older archive with 120 training, 40 validation and 40 test records, the same runner recognizes it and runs the original one-epoch check under `toy/out/`.
