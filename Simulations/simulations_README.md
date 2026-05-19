# Understanding the Monte Carlo Simulations

## Summary

This notebook implements six Monte Carlo experiments to validate and understand the behavior of High-Dimensional Canonical Correlation Analysis (HDCCA) in finite samples. The experiments are designed to benchmark three key phenomena that arise when analyzing large panels of potentially dependent variables:

1. **Dimensionality-Induced Inflation**: Sample canonical correlations can be spuriously large even under independence due to the curse of dimensionality.
2. **Detectability Phase Transition**: Only sufficiently strong population correlations will be reliably detectable and distinguishable from noise in finite samples.
3. **Geometric Instability**: Even when a signal is detectable, the estimated canonical vectors do not reliably recover the population directions, remaining at non-zero angles from the truth.

These findings establish credible benchmarks for interpreting empirical results on financial returns, where the true dependence structure is unknown. The six experiments demonstrate when canonical correlations are interpretable and when they reflect statistical artifacts.

---

## Data Generation Process

### 1. **Baseline Design Parameters**
The simulations use a baseline configuration aligned with the empirical application:
- **Dimensions**: $p = 80$ (first panel), $q = 80$ (second panel)
- **Sample size**: $n = 520$ observations
- **Replications**: 2,000 replications for main experiments; 5,000 replications near the detectability threshold and for null cases (for improved precision)

### 2. **Population Covariance Structure**
A two-panel spiked covariance model is used:

**Population Model**:
$$\begin{pmatrix} X \\ Y \end{pmatrix} \sim \mathcal{N}\left(0, \begin{pmatrix} \Sigma_{xx} & \Sigma_{xy} \\ \Sigma_{xy}^T & \Sigma_{yy} \end{pmatrix} \right)$$

The cross-covariance $\Sigma_{xy}$ is constructed with a low-rank spiked structure:
$$\Sigma_{xy} = \sqrt{\Sigma_{xx}} \cdot K \cdot \sqrt{\Sigma_{yy}}$$

where $K = U_0 \text{diag}(\rho_1, \ldots, \rho_r) V_0^T$ with:
- $\rho_1 \geq \rho_2 \geq \ldots \geq \rho_r$ are the **population canonical correlations** (the "spikes")
- $U_0 \in \mathbb{R}^{p \times r}$ and $V_0 \in \mathbb{R}^{q \times r}$ are orthonormal matrices defining the signal directions
- Within-panel covariances $\Sigma_{xx}$ and $\Sigma_{yy}$ are identity (or non-identity for robustness checks)

### 3. **Data Simulation**
For each replication:

**Step 1**: Generate a random population covariance using the spiked model with specified population canonical correlations.

**Step 2**: Draw $n$ i.i.d. samples from the multivariate distribution:
- **Baseline**: Gaussian multivariate normal with covariance $\Sigma$
- **Robustness checks** (Experiment 6): Heavy-tailed alternatives (e.g., Student-$t$ with $df = 7$) or spiked within-panel covariances

**Step 3**: Compute sample covariance blocks:
$$S_{xx} = \frac{1}{n}X_c^T X_c, \quad S_{yy} = \frac{1}{n}Y_c^T Y_c, \quad S_{xy} = \frac{1}{n}X_c^T Y_c$$
where $X_c$ and $Y_c$ are mean-centered observations.

### 4. **Canonical Correlation Analysis**
For each sample:

**Step 1**: Standardize the cross-covariance:
$$M = S_{xx}^{-1/2} S_{xy} S_{yy}^{-1/2}$$

**Step 2**: Compute SVD: $M = U \Sigma V^T$

**Step 3**: Extract sample canonical correlations and weights:
- Squared sample canonical correlations: $\hat{\lambda}_j = \sigma_j^2$
- Sample canonical weights: $\hat{A} = S_{xx}^{-1/2}U$, $\hat{B} = S_{yy}^{-1/2}V$

### 5. **Performance Metrics Collected**
For each replication, the following metrics are tracked:

| Metric Category | Metric | Purpose |
|-----------------|--------|---------|
| **Spectral** | $\hat{\lambda}_j$ | Sample canonical correlations (detect inflation) |
|  | Number of outliers above null cutoff | Identify separable signal spikes |
|  | Bulk fit quality | Assess low-rank vs. diffuse structure |
| **Inflation** | $\hat{\lambda}_1 - \rho_1^2$ | In-sample bias |
|  | $\lambda_{1,\text{OOS}}$ | Out-of-sample (hold-out) squared correlation |
| **Geometry** | Principal angles $\theta_j$ | Angular distance to true canonical vectors |
|  | Squared cosines $\cos^2(\theta_j)$ | Overlap with true directions |
| **Stability** | Split-sample cosine overlaps | Portfolio consistency across sample splits |

### 6. **Experiment-Specific Variants**
The six experiments vary the population structure:

- **Exp 1**: Pure noise ($\rho = \emptyset$) — null benchmark
- **Exp 2**: Single spike $\rho_1 \in [0.1, 0.9]$ — signal strength sweep
- **Exp 3**: Single spike $\rho_1$ near detection threshold — phase transition
- **Exp 4**: Multiple spikes $\rho_1 > \rho_2 > \ldots$ — low-rank structure
- **Exp 5**: Dense diffuse dependence — non-low-rank case
- **Exp 6**: Variations on Gaussian (heavy tails, non-identity covariance) — robustness

---

## What each experiment contributes

- **Experiment 1 — Pure noise**: used  to establish that large sample canonical correlations can arise under independence.  This justifies a careful null benchmark and prevents over-interpreting large in-sample roots.

-  **Experiment 2 — One-spike sweep**: Used to show how spectral inflation, out-of-sample shrinkage, and vector recovery change with signal strength.

- **Experiment 3 — Threshold zoom**: Used as the main evidence for the finite-sample phase transition.   This experiment motivates why canonical portfolios can be unstable even when the leading root appears large.

- **Experiment 4 — Multiple spikes**: Used to show what a low-rank dependence structure looks like in finite samples.

- Experiment 5 — **Diffuse dependence**: Used to argue that not every multivariate dependence pattern is well summarized by a few factors. 

- **Experiment 6 — Robustness**: Used to defend the Monte Carlo findings against the criticism that the clean Gaussian benchmark is too stylized for finance.

## How to read each Experiment

### Experiment 1

The key object here is the distribution of `lambda1_hat` under **true independence**.

- If the mean of `lambda1_hat` is already large, then classic CCA is mechanically prone to finding apparently strong dependence in high dimensions.
- The `95%` and `99%` null cutoffs give a useful **finite-sample benchmark** for deciding whether a leading sample root is truly exceptional.
- The split-sample squared cosine overlaps indicate how unstable the estimated leading canonical portfolios are under the null. Under pure noise, they should be weak and unstable.
- `lambda1_oos` : if the in-sample top root looks large but the out-of-sample value collapses, that is strong evidence of spectral inflation.

### Experiment 2

This is the central **signal-versus-noise** experiment.

- First we **compare `lambda1_hat` to `rho^2`**: if the sample root is much larger than the true population root, there is strong in-sample inflation.

- Second we **compare `lambda1_hat` to `lambda1_oos`**: if in-sample values rise much faster than out-of-sample values, the leading canonical portfolio is partly overfit.

- **Track `lambda2_hat`**  : Because the DGP has only one true spike, the second root should mostly stay in the bulk. If it rises sharply too, the sample is generating extra apparent structure.

- **Track the angles and squared cosines**: these tell whether the sample canonical weights are actually learning the true signal. Large angles at low \(\rho\) mean the estimated portfolio is mostly a noise direction.


### Experiment 3

This experiment is the check of the **phase-transition idea**.

- **Detection probability**: should stay low below the threshold and rise above it.

- **Gap between `lambda1_hat` and `lambda1_oos`**: near the threshold, in-sample roots can look convincing before they become truly stable out of sample.

- **Angles**: f the angles remain large even when a root is occasionally detected, then detection and reliable vector recovery are not the same thing.

This distinction is crucial for the finance application as a canonical portfolio can look significant in-sample and still be too unstable to interpret or trade.

### Experiment 4

- **Mean scree plot**: a small number of visibly separated roots indicates low-rank dependence.

- **Mean number of outliers**: should roughly match the number of truly strong spikes, not necessarily the total number of nonzero spikes.

- **Component-by-component angles**: recovery should worsen as the signal strength declines. The first component should usually be the best estimated, the weakest component the worst.

### Experiment 5

This experiment is a **model discrimination exercise**.

If the data are low-rank:
- a few clear outliers,
- a steeper scree plot,
- stronger split-sample stability for the first component,
- and a small effective number of outliers.

If the data are diffuse:
- fewer clearly separated outliers relative to the number of true nonzero links,
- a smoother scree profile,
- weaker interpretability of the leading canonical portfolios,
- and a larger gap between “true signal rank” and “empirically separable rank.”

### Experiment 6

The purpose here is to check whether the main HDCCA messages remain visible:

- Does the leading in-sample root stay inflated relative to out-of-sample strength?
- Are the leading vectors still unstable near the edge of detectability?
- Does the broad “bulk + few strong directions” interpretation survive?

Interpretation:

- If `lambda1_hat` rises materially but `lambda1_oos` and split stability remain weak, the robustness setting is amplifying in-sample overfitting.
- If the angle deteriorates sharply under heavy tails or spiked covariance, then the clean Gaussian geometry may be too optimistic.
- If the qualitative ranking of metrics is stable across the three settings, then the baseline conclusions are more likely to carry over to actual stock returns.