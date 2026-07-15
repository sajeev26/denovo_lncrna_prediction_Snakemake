# de_novo lncRNA prediction pipeline

A reproducible **Snakemake workflow** for de novo transcriptome assembly, coding-potential screening, and high-confidence lncRNA prediction from paired-end RNA-seq data in *Elettaria cardamomum*.

## Overview

This repository provides an end-to-end pipeline for identifying candidate long non-coding RNAs (lncRNAs) from raw RNA-seq reads. The workflow is designed around automated execution with Snakemake and combines read preprocessing, transcriptome assembly, redundancy reduction, assembly evaluation, transcript quantification, coding-potential prediction, and final filtering into a single reproducible framework.

The current pipeline structure includes modular Snakemake rules for download, QC, assembly, quantification, assembly assessment, coding-potential analysis, and final lncRNA filtering. The main `Snakefile` imports rule files from `rules/` and defines final outputs including assembled transcripts, CD-HIT clustered transcripts, Salmon quantification, BUSCO results, CPC2 output, PLEK output, and the final lncRNA tables and FASTA sequences.

## Workflow summary

The pipeline runs the following major steps:

1. Download or provide paired-end RNA-seq reads.
2. Perform raw read quality assessment.
3. Trim adapters and low-quality bases.
4. Assemble transcripts de novo using RNA-SPAdes.
5. Reduce transcript redundancy using CD-HIT.
6. Assess assembly completeness using BUSCO.
7. Quantify transcripts using Salmon.
8. Predict coding potential using CPC2 and PLEK.
9. Apply final custom filtering to retain confident lncRNA candidates.

## Repository structure

```text
cardamom_denovo_lncrna_prediction/
├── Snakefile
├── README.md
├── conda.yaml
├── data/
│   └── sra.txt
├── rules/
│   ├── download.smk
│   ├── qc.smk
│   ├── spades.smk
│   ├── quant.smk
│   ├── assembly_qc.smk
│   ├── cp.smk
│   └── filter.smk
├── script/
│   ├── extract.py
│   ├── filter.py
│   ├── merge.py
├── CPC2/
├── PLEK.1.2/
└── busco_downloads/
```

The repository already contains a modular Snakemake layout with separate rule files and a central `conda.yaml`. The conda file currently defines the conda channels, required bioinformatics tools, dependencies, which is a good foundation for reproducible execution.

## Inputs

### Required inputs

- A text file containing one SRA accession per line: `data/sra.txt`.
- Paired-end RNA-seq reads, either downloaded automatically from SRA or supplied locally through the workflow logic.

### Current conda file

The current `conda.yaml` includes:

```yaml
channels:
  - conda-forge
  - bioconda
  - defaults
dependencies
variable path

```

The BUSCO lineage is currently set to `embryophyta_odb10`, which is appropriate for plant transcriptome completeness assessment. The workflow is also configured to write outputs into a `results` directory.

## Main outputs

The `rule all` target in the current `Snakefile` expects the following key outputs:

- `results/rnaspades/transcripts.fasta`
- `results/cdhit/transcripts.cdhit.fa`
- `results/salmon/{sample}/quant.sf`
- `results/busco/run_busco`
- `results/CPC2/cpc2.txt`
- `results/PLEK/plek`
- `results/lncRNA/lncRNA.csv`
- `results/lncRNA/confident_lncRNA.csv`
- `results/lncRNA/confident_lncRNA.fasta`

These outputs clearly indicate that the pipeline is intended to deliver both intermediate evaluation products and a final high-confidence lncRNA candidate set. The final filtered table and FASTA file are the most important end products for downstream biological interpretation.

## Installation

Create a conda environment with Snakemake and run the pipeline with per-rule environments when applicable:

```bash
conda create -n cardamom-lncrna snakemake -c conda-forge -c bioconda
conda activate cardamom-lncrna
```

If your rules use conda environments, execute the workflow with `--use-conda`; this is the standard way to improve reproducibility in Snakemake-based bioinformatics pipelines.

## Usage

### 1. Clone the repository

```bash
git clone https://github.com/sajeev26/denovo_lncrna_prediction_-Snakemake-.git
cd denovo_lncrna_prediction
```

### 2. Add RNA-seq accessions

Create `data/sra.txt` with one accession per line:

```text
SRRXXXXXXX
SRRYYYYYYY
```

### 3. Review configuration

Edit `conda.yaml` to match your compute setup and dependencies.

### 4. Dry-run the workflow

```bash
snakemake -n -p
```

### 5. Execute the pipeline

```bash
snakemake --use-conda --cores 8
```

### 6. Rerun the pipeline if it interupts

```bash
snakemake --cores 8 --use-conda --rerun-incomplete
```

Snakemake is widely used in bioinformatics because it supports scalable and reproducible workflow execution, especially when rules, configuration, and environments are kept separate. That design fits this repository well and should be emphasized in the GitHub presentation.


## Citation and acknowledgment

This workflow draws on common reproducible workflow practices used in Snakemake-based bioinformatics pipelines and follows the general structure used by published and public lncRNA discovery workflows that combine assembly, quantification, coding-potential analysis, and filtering.

# For your queries and suggestions
Reach out to sajeevrajssr@gmail.com
