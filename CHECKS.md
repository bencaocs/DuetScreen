# Release verification

## Train and test on the toy data

The [toy walkthrough](docs/videos/toy-training-and-test.mp4) runs one epoch with seed 42,
saves the checkpoint and uses it to score all 40 toy test records.
AdamW updated the weights on 7 of 8 batches. Comparing the checkpoint with the
pretrained NT-V3 weights showed changes in all 56 decoder tensors. The other
91 NT-V3 parameter tensors matched their pretrained values exactly.

The recorded model had 2,763,264 trainable NT-V3 decoder parameters and 2,987,761
trainable downstream parameters (5,751,025 total trainable). On the toy test set
the new checkpoint achieved AUROC 0.4850 and accuracy 0.4750. The [toy guide](toy/README.md#run)
gives the commands and explains how to check the update count on your GPU.

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
