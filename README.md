# Final Year Project — Using Synthetic Data Generation to Improve Parkinson's Disease Severity Classification from Gait Data

This repository contains the code and supporting analysis for my final year project, completed as part of the MEng Integrated Mechanical and Electrical Engineering degree at the University of Bath.

The project investigates whether synthetic vertical ground reaction force (VGRF) gait signals, generated using a conditional Wasserstein GAN with gradient penalty (cWGAN-GP), can be used to improve a CNN classifier's performance at identifying Parkinson's disease severity (Hoehn & Yahr stages) from gait data.

## Dataset

The code uses the open-access **Gait in Parkinson's Disease** dataset (Hausdorff et al., 2008) from PhysioNet.

Before running any of the notebooks, you must:

1. Download the dataset from <https://physionet.org/content/gaitpdb/1.0.0/>
2. Place the dataset files into the same folder as the notebooks in this repository

The notebooks expect the data files to be available locally alongside the code.

## Files

### `cWGAN-GP.ipynb`
Trains the conditional Wasserstein GAN with gradient penalty used to generate synthetic VGRF gait signals. The generator is conditioned on Hoehn & Yahr class (Control, H&Y 2.0, 2.5, 3.0) and patient weight. Trained model checkpoints and generated signals are written to a `saved_models/` directory; the generated signals are saved as `generated_signals.npz` for use by the classifier notebooks.

### `Gait_Feature_Extraction.ipynb`
Performs the investigative analysis used to characterise the real and synthetic data. This includes extraction of 16 gait features (including the M-shape ratio, stride timing measures, and force asymmetry metrics), principal component analysis, a TSTR support vector machine sanity check, and the interpretability plots (power spectral density, autocorrelation, and averaged gait cycle profiles).

### `Classification_CNN_4ClassSeverity.ipynb`
The main downstream classifier — a lightweight 1D CNN that classifies severity across four classes (Control, H&Y 2.0, 2.5, 3.0). Uses subject-level `GroupKFold` cross-validation to prevent data leakage, and evaluates four training conditions: real-only baseline, classical augmentation (jittering and time-scaling), train-on-synthetic-test-on-real (TSTR), and real + generated augmentation. The final cell of this notebook produces a cross-task comparison plot using hardcoded result numbers from the report — these will need updating if the experiments are re-run.

### `Classification_CNN_Binary.ipynb`
A binary reclassification variant of the same CNN, classifying Control vs. Parkinson's. Otherwise mirrors the four-class notebook in architecture and evaluation protocol.

### `GaitFeatures_StatisticalAnalysis.xlsx`
Supporting spreadsheet containing the Kruskal-Wallis tests and Benjamini-Hochberg false-discovery-rate corrections applied to the extracted gait features across H&Y groups.

## Suggested run order

1. `cWGAN-GP.ipynb` — train the generator and export the synthetic signals to `saved_models/generated_signals.npz`
2. `Gait_Feature_Extraction.ipynb` — characterise real and synthetic data
3. `Classification_CNN_4ClassSeverity.ipynb` and `Classification_CNN_Binary.ipynb` — run the downstream classification experiments. Each loads the generated signals from the path produced in step 1; update the load path at the top of the relevant cell if your output file is elsewhere.

## Requirements

The notebooks were developed in Python with PyTorch, NumPy, SciPy, scikit-learn, pandas, and matplotlib. A CUDA-capable GPU is recommended — both the cWGAN-GP training and the repeated CNN cross-validation are computationally heavy on CPU.
