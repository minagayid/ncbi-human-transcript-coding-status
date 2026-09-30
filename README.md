# Human transcript coding-status ML study

A reproducible baseline study of one narrow question:

> Can simple nucleotide sequence features distinguish human RefSeq transcripts labeled by accession prefix as protein-coding (`NM_`) versus non-coding (`NR_`)?

This is an annotation-label classification exercise, not a test of translation or biological function. FLIP2 results and variant information are intentionally out of scope.

## Open and run

[Open the notebook in Google Colab](https://colab.research.google.com/github/minagayid/ncbi-human-transcript-coding-status/blob/main/ncbi_human_transcript_coding_status.ipynb)

Run all cells from top to bottom with internet access. The notebook installs NCBI Datasets CLI and MMseqs2 in its temporary runtime, downloads the pinned human RefSeq package, and writes a compact JSON manifest plus aggregate CSV result tables. It does not save transcript sequences or row-level predictions in the results bundle.

## Data and operational labels

- Source: NCBI Datasets assembly `GCF_000001405.40` (GRCh38.p14), requesting `rna,gff3`.
- The notebook reads NCBI's assembly data report and asserts the expected assembly and annotation release `GCF_000001405.40-RS_2025_08` before modeling.
- Label: RefSeq accession prefix `NM_` = 1 and `NR_` = 0; other prefixes are excluded.
- Direct transcript-level GFF keys are checked against accession prefixes. Parent-gene biotypes are joined by NCBI GeneID and disagreements are reported separately because gene-level and transcript-level labels are not interchangeable; neither is silently relabeled.
- The 20 nt minimum is a data-integrity floor, not a biological length cutoff; short noncoding RNAs are retained and reported.
- Model fitting uses up to 3,000 randomly sampled records per class after the full parsed set is audited. Metrics therefore describe this capped, class-balanced modeling sample; the held-out class prevalence is printed and saved.

## Leakage-aware evaluation

The balanced modeling sample is capped at 3,000 records per label after a full-source audit. MMseqs2 clusters every sampled transcript sequence at 80% nucleotide identity and 80% coverage, with the maximum transcript length passed explicitly. A connected-component union joins transcripts that share either NCBI GeneID or a sequence cluster. Train, validation, and test are split by these components. The notebook asserts zero pairwise overlap in GeneID, sequence cluster, connected component, and exact sequence hashes.

The result is leakage-aware for this annotation snapshot and specified MMseqs2 rule; it is not proof of independence under all possible homology definitions. This bounded run does not repeat a multi-threshold sweep or use an external release.

## Models and error review

- Majority-class baseline.
- Logistic regression with transcript length, GC fraction, and normalized 3-mer frequencies.
- Character 3–5-mer TF-IDF with logistic regression; regularization is selected on validation AUPRC only.
- The final test is evaluated once after selection. Outputs include precision, recall, F1, AUROC, AUPRC, confusion matrix, connected-component bootstrap intervals, and split-seed sensitivity measured on development-only validation groups while keeping the primary test locked.
- Error tables summarize mistakes by label, transcript length, GC content, and GFF3 biotype. They include accession/header examples for review but do not export sequences in the result bundle.

## Limits and source handling

The `NM_`/`NR_` prefixes are operational RefSeq labels, not independent ground truth about protein production. A held-out connected-component split groups shared genes and sequence clusters within one annotation snapshot, but it does not establish external generalization or robustness to other homology thresholds. The model is not for clinical or experimental decisions. A bioinformatics researcher should review the label semantics, release, split threshold, and errors before stronger claims are made.

NCBI references remain external inputs; this repository does not redistribute the genome package or transcript sequences. See [NCBI Datasets genome packages](https://www.ncbi.nlm.nih.gov/datasets/docs/v2/reference-docs/data-packages/genome/) and the [NCBI assembly record](https://www.ncbi.nlm.nih.gov/datasets/genome/GCF_000001405.40/).
