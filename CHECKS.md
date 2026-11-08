# Release verification

## Train and test on the toy data

The [toy walkthrough](docs/videos/toy-training-and-test.mp4) starts with the source ZIP, expanded toy ZIP and pretrained encoder ZIP. It trains on 960 source records, selects a checkpoint on 240 validation records, and starts a fresh Docker container to score all 40 test records with that checkpoint.

| Seed-42 run | Value |
|---|---:|
| Training epochs completed | 12 |
| Epoch selected by validation combined accuracy | 7 |
| Optimizer updates through the selected epoch | 462 |
| Optimizer updates across the recorded run | 792 |
| Test AUROC | 0.6475 |
| Test accuracy at 0.5 | 0.5500 |

The model had 2,763,264 trainable NT-V3 decoder parameters and 2,987,761 trainable downstream parameters. Comparing the selected checkpoint with pretrained NT-V3 showed that 56 decoder tensors changed and all 91 frozen parameter tensors matched exactly. The [toy guide](toy/README.md#run) gives the commands and output paths.

## Evaluate the released seed-42 checkpoint

The [checkpoint walkthrough](docs/videos/released-checkpoint-evaluation.mp4) evaluates
the published mixed-pool checkpoint on all three main evaluation sets using the
same cached protein features. AUROC uses the predicted scores. Accuracy uses a fixed
threshold of 0.5.

| Evaluation set | Records | AUROC | Accuracy at 0.5 |
|---|---:|---:|---:|
| Mixed test set | 1,677 | 0.9217 | 0.8748 |
| Species holdout | 1,164 | 0.8845 | 0.8308 |
| Protein-family holdout | 817 | 0.8815 | 0.7809 |

Records were matched by sequence ID to the seed-42 reference predictions. AUROC and
accuracy matched the reference to four decimal places on each set. All predicted
labels agreed at the fixed threshold.

To check your run, follow the [labelled evaluation instructions](README.md#labelled-evaluation-metrics).
Compare `metrics.auc` and `metrics.accuracy` in each `detailed_results.json` with
the values above. The [asset manifest](assets_manifest.json) gives the SHA-256 for
the distributed checkpoint.
