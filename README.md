# Human transcript coding-status ML study

This repository contains a reproducible Google Colab notebook for a narrow FlyRank ML-track study: predicting coding vs non-coding status from human NCBI RefSeq RNA transcripts.

## Open in Google Colab

[Open the notebook in Colab](https://colab.research.google.com/github/minagayid/ncbi-human-transcript-coding-status/blob/main/ncbi_human_transcript_coding_status.ipynb)

## What is included

- NCBI RefSeq source and explicit `NM_`/`NR_` label definition
- Gene-grouped held-out split and an explicit leakage check
- Majority, length+GC, and character 3–5-mer TF–IDF logistic-regression models
- Held-out accuracy, balanced accuracy, ROC-AUC, confusion matrix, and error inspection
- Deeper error analysis of individual false positives/negatives using ORF and annotation-derived biological features
- Reproducibility manifest and limitations

FLIP2 and variant analyses are intentionally not mixed into this transcript study.
