# Evo2 7B LoRA Fine-Tuning on *Neurospora crassa* OR74A

A Google Colab implementation of LoRA fine-tuning for Evo2 7B on
*Neurospora crassa* OR74A genomic sequences using PEFT.

## Requirements

- Google Colab with A100 GPU (40GB or 80GB)
- HuggingFace account with access to Evo2 7B weights
- Google Drive for storing genome files and checkpoints
- Colab Secrets: `HF_TOKEN`, `GITHUB_PAT`

## Quick start

1. Open `ncrassa_evo2_finetune.ipynb` in Google Colab
2. Set runtime to A100 GPU
3. Add your HuggingFace token to Colab Secrets as `HF_TOKEN`
4. Run all cells top to bottom

The notebook handles dependency installation, genome download, windowing,
model loading, training, and checkpoint saving.

## Configuration

All hyperparameters are set in Cell 2. Key defaults:

| Parameter | Value | Notes |
|---|---|---|
| LoRA rank (r) | 8 | Increase for more capacity |
| LoRA alpha | 16 | Scaling factor |
| Epochs | 3 | |
| Batch size | 1 | A100 40GB |
| Gradient accumulation | 8 | Effective batch size = 8 |
| Learning rate | 2e-4 | AdamW with cosine decay |
| Sequence length | 4,096 bp | Truncated for 40GB GPU |
| LoRA targets | Attention blocks 3, 10, 17, 24, 31 | Wqkv + out_proj |

## Data

The *N. crassa* OR74A genome is downloaded automatically from FungiDB release 68
(publicly available, no authentication required).

- Genome: `FungiDB-68_NcrassaOR74A_Genome.fasta` (~41 MB)
- Assembly: GCF_000182925.2 (Broad Institute, NC12)
- 7 chromosomes (CM002236–CM002242), 40,463,072 bp

## Architecture note

Evo2 7B uses the StripedHyena (Vortex) architecture — a hybrid of Hyena SSM
and attention blocks. Standard PEFT LoRA requires two compatibility patches:

1. The model config must expose a `to_dict()` method
2. Parameters loaded in inference mode must be cloned before gradient flow
   can be enabled

Both patches are implemented in the notebook.

## Repository structure

```
fungal_evo2/
    fungal_evo2_finetune.ipynb   # Main notebook
    requirements.txt               # Package versions
    LICENSE                        # Apache 2.0
    README.md                      # This file
```

## License

Apache 2.0. See [LICENSE](LICENSE).

## Citation

If you use this code, please cite the Evo2 paper:

> Brixi et al. (2025). Genome modeling and design across all domains of life
> with Evo 2. *bioRxiv*. https://doi.org/10.1101/2025.02.18.638918

And the *N. crassa* OR74A reference genome:

> Galagan et al. (2003). The genome sequence of the filamentous fungus
> *Neurospora crassa*. *Nature*, 422, 859–868.

## Author

Rolando Perez, PhD
