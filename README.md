# Uncertainty Quantification for AlphaFold2 Protein Structure Predictions

An extension of DeepMind's official AlphaFold2 Colab notebook that adds a **perturbation-based
ensemble** to protein structure predictions, as a first step towards uncertainty estimation.

> ## Known issues (October 2026): please read before using any numbers
>
> 1. **No structural alignment.** The current notebook computes MAE, RMSE, interval coverage and
>    the concordance correlation coefficient directly on raw atom coordinates, without
>    superimposing the predicted structure onto the reference first. Without alignment these
>    metrics are not meaningful. (Thanks to the reader who pointed this out in
>    [issue #1](../../issues/1).)
> 2. **Reference structure under review.** The reference file used in the notebook
>    (`AF-P20607-F1-model_v4.pdb`) follows the naming scheme of the AlphaFold Protein Structure
>    Database, so it appears to be a *predicted* model rather than an experimental structure.
>    I have also not yet verified that it corresponds to interleukin-6 (UniProt P05231). Earlier
>    versions of this README described the reference as an experimentally determined structure;
>    that description had not been verified.
> 3. **Terminology.** The method is an ensemble of predictions for perturbed sequences, summarised
>    with percentile intervals. It uses no calibration set and is therefore **not conformal
>    prediction**, although earlier descriptions suggested otherwise.
>
> A corrected version is planned (see [Planned corrections](#planned-corrections)). Until this
> note is removed, please do not rely on any numerical results from this repository.

> **Attribution**: This notebook is a modified version of
> [DeepMind's official AlphaFold Colab notebook](https://github.com/deepmind/alphafold)
> (Apache License 2.0). The base structure-prediction pipeline is DeepMind's; the perturbation
> ensemble and the evaluation code described below are additions made for this project.

## Project Goal

AlphaFold2 provides per-residue confidence scores (pLDDT) and inter-residue error estimates
(PAE), but does not natively quantify how sensitive its predictions are to small perturbations
of the input. This project explores that question: it predicts structures for slightly perturbed
versions of an input sequence and summarises the spread across the ensemble. The intended test
protein is interleukin-6 (IL-6); see known issue 2 regarding the reference file.

## What the notebook does

1. Perturbs the input amino acid sequence with small, controlled mutations (`perturb_sequence`).
2. Runs AlphaFold2 for each perturbed variant.
3. Summarises the ensemble of predicted atom coordinates (mean, variance, 2.5th/97.5th
   percentiles).
4. Loads a reference structure with Biopython's `PDBParser` and computes MAE, RMSE, confidence
   interval width, prediction variance, coverage and the concordance correlation coefficient
   (CCC). **These metrics are currently computed on unaligned coordinates (known issue 1).**
5. Plots the results next to AlphaFold's own pLDDT and PAE outputs.

## Planned corrections

- [ ] Match residues between the predicted and the reference structure (compare the sequences
      first; the numbering can be offset) and use only residues present in both.
- [ ] Superimpose the predicted structure onto the reference using the matched Cα atoms
      (e.g. `Bio.PDB.Superimposer`) before computing any distance-based metric.
- [ ] Verify the reference: use a confirmed experimental structure of human IL-6 from the Protein
      Data Bank (for example the X-ray structure 1ALU) and document which entry and chain are used.
- [ ] Bring all ensemble members into one common coordinate frame before computing means and
      percentiles.
- [ ] Recompute the metrics, rerun the notebook and update this README with the results.

## Results

No reliable results are claimed at this point. An earlier version of this README interpreted a
low interval coverage (about 4%) as a sign of under-estimated uncertainty. Because that value
was computed on unaligned coordinates, the interpretation is not supported and has been removed.

## Tech Stack

`Python` · `AlphaFold2` · `Biopython` · `NumPy` · `Google Colab`

## How to Run

The notebook is designed for **Google Colab** (GPU recommended) because of AlphaFold's
dependencies and compute needs. Open `alphafold_uncertainty.ipynb` in Colab and run the cells
from the top.

## Author

Katharina Kampa · [LinkedIn](https://linkedin.com/in/katharina-kampa-b82b2733b) ·
[GitHub](https://github.com/KatharinaKampa)

Developed during an exchange semester in Computer Science at Jagiellonian University, Kraków.

## License

This project is licensed under the **Apache License 2.0** (see [`LICENSE`](./LICENSE)),
consistent with the license of the original DeepMind AlphaFold notebook it derives from. See
[`NOTICE`](./NOTICE) for a summary of the modifications made to the original work.
