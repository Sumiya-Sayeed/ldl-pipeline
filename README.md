# MIMIC-IV | Explainable LDL Response Pipeline

This repository contains the code and outputs for analyzing **LDL cholesterol response during hospitalization** in a cohort of **21,690 cardiovascular patients** from the MIMIC-IV database.

The pipeline implements a **dual-model explainability framework** designed to preserve two fundamentally different clinical data regimes:

- **Static clinical features** (demographics, labs, baseline LDL)
- **Sequential prescription data** (ordered drug administration)

---

## Method Overview

Two complementary models are used to answer distinct but related clinical questions:

| Model | Clinical Question | Approach | Input |
|-------|------------------|----------|-------|
| **XGBoost + SHAP** | *Which clinical factors predict LDL improvement?* | Identifies population-level risk factors via feature attribution | 96 features: demographics, lab aggregates, baseline LDL, medication flags |
| **Transformer** | *Which drug sequences are informative of LDL response?* | Learns temporal treatment patterns via self-attention | Ordered drug sequence + initial LDL |

> These models are **not competing**, but provide **orthogonal explanatory views**:
> - SHAP → feature-level attribution  
> - Attention → position-aware sequence attribution  

---

## Key Insight

A central finding of this work is that:

- **Statins rank highly in SHAP importance**
- But show **near-zero contrast in attention**

→ indicating that **statin prescription is a marker of treatment**, not a fine-grained discriminator of LDL response.

This distinction emerges only when combining both models.

---

## Repository Structure

- `ldl_focused_pipeline.ipynb`  
  End-to-end pipeline: data processing, feature engineering, model training, and evaluation.

- `ldl_checkpoints/`  
  Saved Transformer weights (`.pt`) and mappings (`.pkl`).

- `ldl_outputs/`  
  Generated outputs:
  - SHAP plots
  - Attention maps
  - Contrastive analysis
  - Summary figures

---

## Pipeline Stages

The notebook is structured into the following stages:

1. **Data Extraction**  
   Loads MIMIC-IV tables (`labevents`, `pharmacy`, `chartevents`) filtered by cardiovascular cohort.

2. **Feature Engineering**  
   Constructs static feature matrix (demographics, labs, medications).

3. **XGBoost + SHAP**  
   - Trains tabular model  
   - Produces global and local explanations  

4. **Transformer + Attention**  
   - Learns sequence dynamics  
   - Extracts drug importance and positional effects  
   - Computes contrastive attention (responders vs non-responders)

---

## Notable Outputs

Located in `ldl_outputs/`:

- `shap_beeswarm_cls.png`  
  Global feature importance (static view)

- `contrastive_drug_attention.png`  
  Drug-level differences between responders and non-responders

- `attention_heatmaps.png`  
  Sequence-level interaction patterns

- `drug_category_analysis.png`  
  Category-level aggregation of drug effects

## Disclaimer

- This analysis is **observational**, not causal
- Attention weights are used as **structured attribution signals**
- Results should be interpreted as **hypothesis-generating**

---

## Data Access

MIMIC-IV is not included.

Access required via PhysioNet:  
https://physionet.org/content/mimiciv/

---

## License

For academic and research use only.