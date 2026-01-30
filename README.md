# Electricity Consumption Regime Discovery & Anomaly Detection (U.S. 2015–2024)

## Overview

This project analyzes U.S. utility-scale electricity generation data (2015–2024) to uncover **structural operating regimes** and detect **statistically meaningful anomalies** in consumption behavior.

Rather than treating anomalies as isolated outliers, the pipeline explicitly separates:

1. **Structural regime changes** (long-run shifts in system behavior)
2. **Short-term deviations** from otherwise stable dynamics

This distinction is critical in energy systems, where demand patterns evolve due to policy, fuel mix transitions, climate variability, and macroeconomic shocks.

---

## Data

- **Source**: U.S. Electricity Generation (Utility-Scale)
- **Frequency**: Monthly
- **Scope**: All fuels + fuel-specific series
- **Time Span**: 2015–2024

All series are aligned to a monthly `DatetimeIndex` to preserve temporal semantics throughout the pipeline.

---

## Methodology Overview

The analysis proceeds in **three logically independent layers**:

1. **Regime Discovery (Unsupervised Learning)**
2. **Baseline Time-Series Modeling (Within Regime)**
3. **Anomaly Detection on Model Residuals**

This separation avoids a common failure mode in time-series ML: confusing structural change with noise.

---

## 1. Regime Discovery (Unsupervised)

### Objective

Identify **persistent operating regimes** in electricity generation without imposing labels or thresholds.

### Feature Construction

Fuel-level generation series are standardized and reduced using PCA to remove multicollinearity and scale dominance.

```python
from sklearn.decomposition import PCA

pca = PCA()
X_pca = pca.fit_transform(X_scaled)
