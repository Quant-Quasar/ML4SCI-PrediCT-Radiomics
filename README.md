# ML4SCI / CERN PrediCT — Cardiac CT Radiomics Pipeline
**Evaluation Test Submission**  


---

## Project Overview

This project implements a complete cardiac CT radiomics pipeline on the 
[COCA dataset](https://doi.org/10.71718/ge5g-ds80) (Coronary Calcium and Chest CTs, 
Stanford AIMI). It covers preprocessing, Agatston calcium scoring, PyRadiomics 
feature extraction, and statistical phenotyping across 30 patients.

### Key Results
| Metric | Value |
|--------|-------|
| Patients processed | 30 (IDs 0–30, excluding 12) |
| Agatston range | 1.6 – 3010.2 |
| Category distribution | 1-99: 14, 100-399: 9, 400+: 7 |
| Top feature (Spearman ρ) | total_calcium_volume_mm3 (ρ=0.986, p<0.0001) |
| Significant features (p<0.05) | See notebook2_radiomics.ipynb |
| KMeans ARI | 0.200 |

---

## Repository Structure

```
├── notebook1_preprocessing.ipynb   # Common Task: preprocessing pipeline
├── notebook2_radiomics.ipynb       # Project 2: feature extraction & analysis
├── params.yaml                     # PyRadiomics extractor configuration
├── requirements.txt                # Pinned dependencies for Python 3.8
├── outputs/
│   ├── agatston_scores.csv
│   ├── radiomics_features.csv
│   ├── agatston_distribution.png
│   ├── category_distribution.png
│   ├── sample_slice_overlay.png
│   ├── correlation_heatmap.png
│   ├── top_features_bar.png
│   ├── top3_scatter.png
│   ├── tsne.png
│   └── tsne_clusters.png
└── data/                           # Not committed — see Data Setup below
    └── .gitkeep
```

---

## Data Setup

1. Access the COCA dataset via Azure Blob Storage:
   `cocacoronarycalciumandchestcts-2`

2. Download from:
   - `Gated_release_final/patient/0–30` → `data/raw/`
   - `Gated_release_final/calcium_xml/0–30` → `data/calcium_xml/`

3. Set `DATA_ROOT` at the top of each notebook to your local path.

---

## Environment Setup

```bash
# Step 1: Create environment (Python 3.8 required for PyRadiomics)
conda create -n predict_env python=3.8 -y
conda activate predict_env

# Step 2: Install PyRadiomics
conda install -c radiomics -c conda-forge pyradiomics -y

# Step 3: Install all dependencies
pip install -r requirements.txt

# Step 4: Register Jupyter kernel
python -m ipykernel install --user --name predict_env --display-name "PrediCT Python 3.8"

# Step 5: Launch Jupyter
jupyter lab
```

---

## Execution Order

1. Open `notebook1_preprocessing.ipynb` with kernel **"PrediCT Python 3.8"**
2. Set `DATA_ROOT` to your data path
3. Run all cells — generates `outputs/agatston_scores.csv` and preprocessed `.npy` files
4. Open `notebook2_radiomics.ipynb` with same kernel
5. Run all cells — generates `outputs/radiomics_features.csv` and all plots

---

## Methodology

- **Preprocessing:** HU windowing (WC=150, WW=700), resampling to 1mm³ isotropic
- **Masks:** Reconstructed from expert radiologist XML contours (not thresholding)
- **Agatston:** Computed from original 3.0mm spacing per Agatston et al. 1990
- **Features:** PyRadiomics v3.x, IBSI Chapter 1 compliant, 15 features across Shape/GLCM/GLSZM/GLRLM
- **Statistics:** Spearman correlation, Kruskal-Wallis, Benjamini-Hochberg FDR correction

> **Note on model weights:** Project 2 is a radiomics feature extraction and 
> statistical analysis task. It produces no trainable neural network and therefore 
> has no model weights to submit. All outputs are the CSV feature matrices and 
> statistical analysis results in the `outputs/` folder.

- **Clinical context:** Radiomic phenotyping of coronary calcium is supported 
  by recent literature demonstrating prognostic value of CT-derived plaque 
  texture features (Kolossváry et al. JACC Imaging 2025, Abaid et al. 2024).
---

## AI Assistance Disclosure

AI tools were used for code review, documentation formatting, and literature 
citation verification. All experiments, model architectures, training runs, 
and results are the author's own original work.

---