# NanoDNA-Transformer

A deep learning–based DNA sequence analysis project that uses a decoder-only Transformer language model to identify and localize statistically unusual mutations by measuring per-position likelihood shifts.
This repository demonstrates how a decoder-only Transformer language model trained on healthy DNA can localize statistically unusual mutations by analyzing per-position likelihood shifts without relying on labeled data.


## Project Overview

This project explores how a neural language model trained on healthy genomic DNA can be used to analyze mutations without requiring labeled biological or disease data.

Rather than attempting to classify mutations as pathogenic or benign, the model answers a simpler and well-defined question:

Does a given DNA sequence conform to the statistical patterns learned from healthy DNA, and if not, where does it deviate?

The output is an interpretable, position-wise signal that highlights where mutations introduce statistically unlikely base transitions.

## Core Idea

DNA is treated as a sequence language over the alphabet `{A, C, G, T}`.  
A decoder-only Transformer is trained to predict the next nucleotide given all previous nucleotides in the sequence.

Through this training objective, the model learns:

- Context-dependent nucleotide transitions
- Common and rare k-mer patterns
- Local sequence structure in healthy genomic DNA

Mutations are analyzed by comparing how likely the model finds a reference sequence versus a modified sequence, evaluated position by position.

## Model Architecture

- Decoder-only Transformer (GPT-style)
- Causal self-attention
- Autoregressive next-token prediction
- Character-level DNA tokenization
- No encoder and no bidirectional attention

The architecture is intentionally minimal to emphasize interpretability rather than scale.

## How the Analysis Works

### Training

- The model is trained on raw DNA sequences only
- Objective: predict the next base given prior bases
- No mutation labels, annotations, or biological metadata are used

### Sequence Scoring

For any DNA sequence, the model computes the negative log-likelihood at each position:

\text{loss}_i = -\log P(\text{base}_i \mid \text{base}_1, \dots, \text{base}_{i-1})

Lower loss indicates that the base is expected in context, while higher loss indicates statistical surprise.

### Mutation Comparison

Given a reference sequence and a mutated sequence:

- Per-position losses are computed for both
- The difference (Δ loss) is calculated:

Δ_i = loss_i(mutated) − loss_i(reference)


### Interpretation

- Positive Δ loss indicates a statistically unlikely mutation
- Negative Δ loss indicates a mutation that is more common than the reference
- Local spikes indicate where the mutation disrupts learned sequence patterns

## Output and Visualization

The model produces:

- Per-position Δ loss values to localize mutation impact
- Mean Δ loss as a global summary
- Max Δ loss as the strongest localized deviation

Results are visualized as line plots of Δ loss versus sequence position.

## Example Experiments

The notebook includes two illustrative cases:

1. A single-point substitution producing a localized likelihood spike.
2. A synthetic repetitive sequence (`AAAAAAAA...`) producing consistently high loss across all positions.

These examples help build intuition about model behavior.

## Dataset Source

Training data was derived from a publicly available human reference genome sequence:

- Source: NCBI Nucleotide Database
- Accession: NC_000011.10 (Human Chromosome 11)
- Link: https://www.ncbi.nlm.nih.gov/nuccore/NC_000011.10/
- Genomic range used: 5,200,000 – 5,300,000

Only raw nucleotide sequences were used. No annotations or biological labels were included.

## What This Project Is Not

This project does not:

- Predict disease or pathogenicity
- Perform functional or protein-level analysis
- Provide clinical or medical conclusions
- Replace biological variant interpretation tools

It is strictly a statistical sequence analysis approach based on language modeling.

## How to Use This Repository

1. Open the provided notebook (`.ipynb`) on GitHub or in Google Colab
2. Run the notebook from top to bottom:
 - Data loading
 - Model training
 - Mutation analysis
 - Visualization
3. Replace example DNA sequences with custom sequences to explore model behavior

A small example FASTA file is included to demonstrate the expected input format.

## Disclaimer

This project is intended for educational and research purposes only.  
It does not provide biological, clinical, or medical interpretations.
