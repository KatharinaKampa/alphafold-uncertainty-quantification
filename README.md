# Uncertainty Quantification for AlphaFold2 Protein Structure Predictions

An extension of DeepMind's official AlphaFold2 Colab notebook that adds **perturbation-based uncertainty quantification** to protein structure predictions, evaluated against experimental structures.

> **Attribution**: This notebook is a modified version of [DeepMind's official AlphaFold Colab notebook](https://github.com/deepmind/alphafold) (Apache License 2.0). The base structure-prediction pipeline is DeepMind's; the uncertainty-quantification methodology, evaluation metrics, and analysis described below are original additions developed for this project.

## Project Goal

AlphaFold2 provides per-residue confidence scores (pLDDT) and inter-residue error estimates (PAE), but does not natively quantify how sensitive its predictions are to small input perturbations. This project investigates that question using **Interleukin-6 (IL-6)** as a test protein, extending the standard pipeline with a perturbation-based approach to uncertainty estimation, evaluated against the experimentally determined structure.

## Methods

**Perturbation-Based Prediction**
- The IL-6 amino acid sequence is systematically perturbed with slight, controlled mutations (`perturb_sequence`)
- AlphaFold2 predictions are generated for each perturbed variant
- Mean and standard deviation across the ensemble of perturbed predictions provide an empirical estimate of prediction uncertainty, complementing AlphaFold's native pLDDT/PAE confidence metrics

**Evaluation Against Ground Truth**
- The experimentally determined structure is loaded from a PDB file via `Biopython`'s `PDBParser`
- Predicted structures are compared against the experimental structure using:
  - **Mean Absolute Error (MAE)** and **Root Mean Square Error (RMSE)** between predicted and experimental atomic positions
  - **Confidence interval width** derived from the perturbation ensemble
  - **Prediction variance** across perturbed runs

**Confidence Metric Interpretation**
- pLDDT (per-residue local confidence) and PAE (inter-domain/inter-chain confidence) are analyzed alongside the perturbation-based uncertainty estimates to compare native AlphaFold confidence signals with the empirical, perturbation-derived uncertainty.

## Key Takeaways

- Practical application of **conformal-prediction-style uncertainty quantification** to a real deep learning system (AlphaFold2) rather than a toy model
- Hands-on experience evaluating model predictions against ground-truth experimental data using standard error metrics (MAE, RMSE)
- Bridges structural biology (protein folding, PDB structures) with core ML evaluation methodology

## Limitation: Confidence Interval Coverage

One of the more interesting findings was that the empirical **coverage** of the perturbation-derived confidence intervals was low (~4%), far below the ~95% one would expect from a well-calibrated interval — i.e. the true experimental structure fell inside the predicted confidence band far less often than intended. This indicates the perturbation-based intervals in this implementation **underestimate** the true prediction uncertainty rather than reflecting a failure of the pipeline itself. Likely contributors include the small number of perturbed variants used to build the ensemble and the fact that sequence-level perturbation only captures one source of uncertainty (input sensitivity), not others AlphaFold2 is subject to (e.g. MSA depth, template availability). A natural next step would be to increase the perturbation ensemble size and compare against AlphaFold's native pLDDT/PAE confidence estimates more systematically.

## Tech Stack

`Python` · `AlphaFold2` · `Biopython` · `NumPy` · `Google Colab`

## How to Run

This notebook is designed to run in **Google Colab** (GPU recommended) due to AlphaFold's dependency setup and compute requirements. Open `notebook/alphafold_uncertainty.ipynb` in Colab and run cells sequentially from the top.

## Author

Katharina Kampa · [LinkedIn](https://linkedin.com/in/katharina-kampa-b82b2733b) · [GitHub](https://github.com/KatharinaKampa)

Developed during an exchange semester in Computer Science at Jagiellonian University, Kraków.

## License

This project is licensed under the **Apache License 2.0** (see [`LICENSE`](./LICENSE)), consistent with the license of the original DeepMind AlphaFold notebook it derives from. See [`NOTICE`](./NOTICE) for a summary of the modifications made to the original work, as required by the license.
