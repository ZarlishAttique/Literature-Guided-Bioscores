# Literature-Guided-Bioscores
Gene Prioritization - Biological Context Aware Engine

# Gene Prioritization - Composite Score Calculations

This repository provides a computational framework developed at **Ayass BioScience, LLC** for prioritizing genes using a composite scoring approach. It integrates multiple biologically and clinically relevant signals and supports interpretation of next-generation sequencing (NGS) panel data, especially in relation to rare genetic disorders and oncology.

---

## Overview

The core of this project is the notebook:
> `GenePrioritization_Composite_Score_Calculations.ipynb`

It calculates a composite gene score from five major biological signals:

- **LFC (Log Fold Change)** from patient-specific transcriptomic data  
- **PPI (Protein-Protein Interaction)** score  
- **CGC (Cancer Gene Census)** flag (with enhanced handling)  
- **GeneCards Relevance Score**  
- **DiseaseDB Z-score** for phenotype association

Each feature undergoes **min-max scaling**, is weighted, and contributes to a final prioritization score.

---

## Feature Engineering Details

| Feature | Description | Scaling | Notes |
|--------|-------------|---------|-------|
| **LFC** | Differential expression across patients | Min-Max | Emphasizes high-contrast regulation |
| **PPI Score** | Protein-protein interaction strength | Min-Max | Derived from STRING |
| **CGC** | Binary cancer gene flag | 1 or 0 | Includes additive bonus |
| **GeneCards** | Functional literature relevance | Min-Max | API-sourced or downloaded |
| **DiseaseDB Z-scores** | Phenotype-association signal | Min-Max | Based on z-score strength |

---

## Special Handling of CGC Genes

To account for well-documented cancer driver genes:

- Genes from the **Cancer Gene Census (CGC)** are flagged with a binary value `1`.
- Their composite score receives an **additive boost** (default `+0.05`) to prioritize them, even if some other scores are weak.

---

## Workflow

1. **Data Input**  
   Load data tables containing the 5 key features listed above.

2. **Min-Max Normalization**  
   All non-binary features are scaled:
   \[
   x_{\text{scaled}} = \frac{x - x_{\min}}{x_{\max} - x_{\min}}
   \]

3. **Weight Assignment**  
   Default values:
   - LFC: `0.25`  
   - PPI: `0.20`  
   - CGC: `0.10`  
   - GeneCards: `0.25`  
   - DiseaseDB: `0.20`  

4. **Scoring Formula**

   Composite Score for each gene:
   \[
   \text{Score} = w_1 \cdot \text{LFC}_{\text{scaled}} + w_2 \cdot \text{PPI}_{\text{scaled}} + w_3 \cdot \text{CGC}_{\text{binary}} + w_4 \cdot \text{GeneCards}_{\text{scaled}} + w_5 \cdot \text{DiseaseDB}_{\text{scaled}}
   \]

   For CGC genes:
   \[
   \text{Score}_{\text{final}} = \text{Score}_{\text{base}} + 0.05
   \]

5. **Ranking & Output**  
   Final gene list is sorted by descending composite score.  
   Output includes:
   - CSV of ranked genes  
   - Score distribution plots

---

## Files

- `GenePrioritization_Composite_Score_Calculations.ipynb`: Main scoring pipeline

---

## Requirements

```bash
pip install pandas numpy matplotlib seaborn scikit-learn
