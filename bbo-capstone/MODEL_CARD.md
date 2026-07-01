# Model Card — BBO Capstone Surrogate Models

*Following the Model Cards for Model Reporting framework (Mitchell et al., 2019)*

---

## Model Details

| Attribute | Value |
|-----------|-------|
| **Primary surrogate** | Gaussian Process Regressor (GPR) |
| **Kernel** | `ConstantKernel(1.0) × Matérn(ν=2.5, ARD) + WhiteKernel` |
| **Input scaling** | `StandardScaler` (zero mean, unit variance) |
| **Output normalisation** | `normalize_y=True` (internal to GPR) |
| **Hyperparameter optimisation** | `n_restarts_optimizer=10`, marginal likelihood maximisation |
| **Acquisition function** | Expected Improvement (EI) — maximisation convention |
| **Acquisition optimiser** | Multi-start L-BFGS-B (35 restarts, top-35 of 5,000 warm-start candidates) |
| **Secondary surrogate** | MLPRegressor (2×32 ReLU, L-BFGS-B, α=1e-3) — gradient analysis only |
| **Framework** | scikit-learn 1.x, scipy, numpy |
| **Random seed** | 42 throughout |
| **Developed by** | Gian Franco Cattaneo |
| **Date** | March–June 2026 |
| **Version** | Round 10 (W21) |

---

## Intended Use

### Primary use
Surrogate-based Bayesian optimisation of eight synthetic black-box functions (d=2–8D) within the Imperial College London BBO Capstone project. The GP surrogate approximates each unknown function from limited observations, and the EI acquisition function guides selection of the next query point per round.

### Secondary use
NN surrogate (MLPRegressor) was used in early rounds (R4–R5) exclusively for finite-difference gradient analysis at the best-known point — identifying dominant input dimensions to inform per-function acquisition bounds. From Round 6 onward, empirical per-round gradients derived from the submission history replace the NN for directional guidance, as the accumulated dataset provides sufficient signal.

### Out-of-scope uses
- Real-world engineering or industrial deployment without revalidation
- Functions with discontinuous or non-stationary landscapes (Matérn-5/2 assumes local stationarity)
- Problems requiring batch queries (this model generates one point per function per round)

---

## Training Data

See `DATASHEET.md` for full data documentation.

**Summary**: Each function's GP is fitted on N=9 submitted query points (Rounds 1–9) for Round 10 computation. The initial portal `.npy` files (N₀=10–40 depending on dimensionality) were not incorporated into submitted queries. Incorporating these initial arrays would substantially improve surrogate quality and remains recommended for subsequent rounds.

---

## Model Architecture

### Gaussian Process Surrogate

```
Input x ∈ [0, 0.999999]^d
    ↓  StandardScaler  →  x_scaled ∈ ℝ^d
    ↓  GP posterior    →  μ(x), σ²(x)
    ↓  EI acquisition  →  EI(x) = (μ(x) − y* − ξ)·Φ(Z) + σ(x)·φ(Z)
    ↓  L-BFGS-B        →  x_next = argmax EI(x)
         (35 restarts, seeded from top-35 of 5,000 random candidates)
```

**Kernel decomposition**:
- `ConstantKernel(1.0, bounds=(1e-3, 1e3))`: overall output scale
- `Matérn(ν=2.5, ARD, length_scale_bounds=(1e-3, 10.0))`: spatial correlation with per-dimension length-scales. ARD identifies inactive or low-sensitivity dimensions automatically.
- `WhiteKernel(noise_level=1e-4, bounds=(1e-8, 1e-1))`: homoscedastic observation noise

**EI formulation (maximisation)**:

$$\text{EI}(x) = \begin{cases}
(\mu(x) - y^* - \xi)\,\Phi(Z) + \sigma(x)\,\phi(Z) & \text{if } \sigma(x) > 0 \\
0 & \text{otherwise}
\end{cases}$$

$$Z = \frac{\mu(x) - y^* - \xi}{\sigma(x)}, \quad y^* = \max_{i=1}^{N} y_i, \quad \xi = 0.01$$

Where $\Phi$ is the standard normal CDF and $\phi$ is the standard normal PDF.

### NN Surrogate (gradient analysis only, Rounds 4–5)

```
Architecture:  input(d) → Dense(32, ReLU) → Dense(32, ReLU) → output(1)
Solver:        L-BFGS-B
Regularisation: L2, α=1e-3
Gradient:      finite-difference central scheme, ε=1e-4
```

---

## Evaluation

### GP Posterior Quality Indicators

| Metric | Assessment method |
|--------|------------------|
| Log marginal likelihood | Reported per function per round |
| ARD length-scales | Short scale → high sensitivity; long scale → low sensitivity |
| Posterior σ at query point | Low σ → confident prediction (exploitation); high σ → uncertain (exploration) |
| LOO-MSE (normalised) | Reported in R4 notebook for GP, SVR, NN comparison |

### Round 4 LOO-MSE Comparison (R1–R3 data, normalised Y)

| Function | LR | SVR | NN | Winner |
|----------|-----|-----|-----|--------|
| f1 | 2.047 | 2.301 | 2.841 | LR |
| f2 | 488.6 | 3.241 | 54.91 | SVR |
| f3 | 2.122 | 2.158 | 1.451 | NN |
| f4 | 0.889 | 1.507 | 0.386 | NN |
| f5 | 9.279 | 2.251 | 2.493 | SVR |
| f6 | 2.177 | 2.257 | 1.839 | NN |
| f7 | 1.469 | 1.507 | 0.608 | NN |
| f8 | 1.485 | 1.508 | 0.766 | NN |

*Note: with N=3–4, LOO-MSE is indicative only. GP remains primary surrogate for all functions.*

---

## Per-Function Landscape Characterisation (through Round 9)

| f | d | Best Y (R1–R9) | Dominant strategy | Landscape signature |
|---|---|---------------|-------------------|---------------------|
| f1 | 2 | 2.216e-7 | Micro-gradient (x1↑, x2↓) | Effectively flat; tiny positive gradient near (0.479, 0.499) |
| f2 | 2 | 0.7237 | Exploit R1 | Sharp narrow peak; R1 unbeaten after 9 rounds |
| f3 | 3 | −0.0353 | Exploit R7 | All-negative; local optimum at (0.478, 0.223, 0.408) confirmed by R8/R9 regression |
| f4 | 4 | 0.4711 | Exploit R8 | Bimodal: interior good, boundary catastrophic (−30); R8 current best |
| f5 | 4 | 4440.483 | Hold + x1 probe | Plateau at 4440.481 for x1=0; R6 x1=0.005 gave 4440.483 (non-monotone x1) |
| f6 | 5 | −0.5508 | Exploit R1 | All-negative; x4=0.999999, x5=0.000 structural anchors; R1 unbeaten |
| f7 | 6 | 2.2706 | Gradient descent x2–x6 | Monotone gradient law: −0.003/round on x2–x6; x1=0 locked; 5 consecutive gains |
| f8 | 8 | 9.8597 | x3 gradient | Near-plateau; x3 sole active gradient (+0.002/round); all other 7 dims frozen |

---

## Per-Function GP Behaviour (Round 5, indicative)

| f | GP kernel (fitted) | ARD top dim | σ at query | Reliability |
|---|-------------------|-------------|------------|-------------|
| f1 | ConstK × Matérn([10, 0.38]) + WK(1e-6) | x2 | ~0 | Low (flat landscape) |
| f2 | ConstK × Matérn([0.01, 0.01]) + WK(0.096) | both | 0.062 | Moderate |
| f3 | ConstK × Matérn([0.18, 10, 10]) + WK(1e-4) | x1 | 0.113 | Moderate |
| f4 | ConstK × Matérn([1.40, 10, 10, 10]) + WK(1e-8) | x1 | 6.17 | **Low** — manual override |
| f5 | ConstK × Matérn([0.04, 0.74, 1.02, 6.30]) + WK(0.10) | x2 | 700 | Moderate (high range) |
| f6 | ConstK × Matérn([3.40, 10, 10, 10, 1.65]) + WK(2e-8) | x1 | 0.182 | Moderate |
| f7 | ConstK × Matérn([7.03, 10, 10, 10, 0.99, 10]) + WK(1e-8) | x5 | 0.348 | Moderate |
| f8 | ConstK × Matérn([10, 10, 10, 0.07, 0.43, 10, 0.86, 10]) + WK(1e-8) | x4 | 0.406 | Good |

*WK = WhiteKernel. ARD length-scale ≤ 0.5 → high sensitivity; ≥ 5.0 → low sensitivity.*

---

## Known Limitations

### 1. Small sample size
With N=9 per function at Round 10, GP hyperparameter estimation remains constrained, particularly in d=6 and d=8 dimensions where N << d². Length-scale estimates can be degenerate (hitting bounds at 0.01 or 10.0). Results should be interpreted with wide confidence intervals.

### 2. Stationarity assumption
Matérn-5/2 assumes uniform smoothness across the input space. Functions with regime changes (f4's boundary collapse from +0.47 to −30) violate this locally. From Round 6 onward, per-function empirical gradient rules supplement the GP where stationarity is demonstrably violated.

### 3. Single-point acquisition
One query per round per function produces slow convergence. Batch EI or Thompson sampling would accelerate optimisation but require pipeline modification.

### 4. Exploitation concentration risk
From Round 5 onward, queries are tightly clustered around incumbent bests (radius < 0.03 on each dimension for f7, f8). Large unexplored regions may contain superior optima, particularly in d=6 and d=8 spaces.

### 5. Round 4 maximisation error
Round 4 used minimisation EI (`y_best = min(Y)`), causing four functions to deteriorate. Corrected in Round 5 (`y_best = max(Y)`). Historical R4 values for f4, f5, f7, f8 should be treated as exploration results.

### 6. Temporal gradient stationarity
Extrapolations for f7 and f8 assume per-round improvement laws observed over Rounds 5–9 persist. Deceleration is already visible in both (+0.009/round → +0.001/round for f7). A plateau may be imminent.

---

## Assumptions

1. All eight functions return noise-free or low-noise scalar outputs for a given input.
2. The portal applies the maximisation transformation correctly (minimisation functions are negated before returning outputs).
3. The search space is [0, 0.999999]^d per project specification; 0.999999 is used in place of 1.0 throughout.
4. The GP kernel hyperparameters learned on N=9 points are sufficient for local landscape characterisation near the observed best point.
5. Empirical per-round trends observed over R5–R9 (particularly f7 and f8) represent true gradient directions rather than noise fluctuations.

---

## Interpretability

### What the GP posterior tells us
- **μ(x)**: best estimate of function value at unobserved point x
- **σ(x)**: uncertainty — high where no data exists; low near observed points
- **ARD length-scales**: proxy for input dimension sensitivity. Short length-scale = rapid function change in that direction = high sensitivity

### What the empirical gradient law tells us (Rounds 6–10)
For functions with confirmed monotone trends (f7, f8), per-round coordinate changes form a quantified descent/ascent law:
- **f7**: uniform −0.003/round on x2–x6; confirmed over 5 consecutive rounds
- **f8**: +0.002/round on x3 only; all other dimensions exhibit zero marginal return

### What EI tells us
- High EI → either GP predicts improvement above current best (exploitation), or uncertainty warrants exploration
- ξ=0.01 favours exploitation; strategic overrides apply explicit exploration (e.g., f5 x1 probe at R10)

### Transparency of decision-making
Each round 10 query is grounded in a falsifiable empirical claim (documented in Cell 5–6 of the notebook). The GP-vs-strategic comparison table in Cell 6 makes every override explicit and auditable. A reviewer with access to `SUBMISSIONS_LOG.md` can reconstruct the same queries from the data alone.

---

## Ethical Considerations

This model is used exclusively for academic purposes within a controlled educational exercise. The synthetic functions have no real-world consequences. No personal data, proprietary information, or sensitive materials are involved.

---

## Citation

Cattaneo, G.F. (2026). *Bayesian Black-Box Optimisation Capstone Project*. Executive Master in Machine Learning & Artificial Intelligence, Imperial College London. GitHub repository: [link to be added at Module 25 submission].

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# MODEL CARD — Round 13 Update

> Merge target: `MODEL_CARD.md`. This block documents the **terminal (Round 13)** state of
> the decision system. 

**Author:** Gian Franco Cattaneo · Imperial Business School (Executive Master ML/AI)
**Model version:** v13 (terminal) · **Date:** 2026-06-29 · **Status:** frozen

---

## Model description (terminal)

The "model" is a **per-function Bayesian-optimisation decision system**, not a single
trained network. For each of the 8 functions it comprises:

1. A **Gaussian-Process surrogate** — `ConstantKernel(σ²) × Matérn-5/2(ARD length-scales)
   + WhiteKernel(noise)`, fit on standardised inputs and standardised targets.
2. An **Expected-Improvement** acquisition maximised over a **trust region** around the
   incumbent best (`tr = 0.015`, `xi = 0`), evaluated on ~3×10⁵ candidate draws.
3. An **override layer** (the operative policy) that supersedes the surrogate when a clean
   empirical signal is more reliable than a data-starved GP extrapolation.

### Fit configuration

| Item | Value |
|------|-------|
| Kernel | C(1.0, [1e-3, 1e3]) × Matérn(ν=2.5, ARD, ls∈[1e-2, 1e2]) + WhiteKernel([1e-8, 1e1]) |
| Optimiser restarts | 30 (L-BFGS-B) |
| Input scaling | StandardScaler |
| Acquisition | Expected Improvement (maximisation), xi = 0 |
| Trust region | ±0.015 about incumbent (+ ±0.030 halo), clipped to [0, 0.999999] |
| Seed | 7 |

## Intended use

Sequential proposal of the next query point under an expensive black-box, maximisation
objective, with ≤ a few dozen evaluations total. **Out of scope:** global function
reconstruction, deterministic guarantees of optimality, transfer to other portals.

## Round-13 decisions and rationale

| Fn | GP-EI cross-check | Final decision | Override reason |
|----|-------------------|----------------|-----------------|
| f1 | (0.485, 0.463) off-ridge | **(0.489, 0.489)** | Ridge x₁+x₂=0.978; GP off-ridge gradient unidentified; place at symmetry (predicted peak) |
| f2 | ≈ incumbent, sd ≈ 0.10 | **re-query incumbent** | Stochastic basin; exploit = re-pull best arm, not extrapolate |
| f3 | 0.03 jump in x₁ | **re-query incumbent** | Knife-edge basin; R12 regressed at near-identical input |
| f4 | μ ≈ 1.21, dir agrees | **(0.430, 0.412, 0.360, 0.419)** | GP + empirical vector concur (x₂↓/x₃↓/x₄↑); step taken, magnitude hedged |
| f5 | flat, μ ≤ incumbent | **(0.040, ub, ub, ub)** | x₂=x₃=x₄=ub is a hard anchor; extend monotone x₁ |
| f6 | x₄,x₅ drift | **(0.455, 0.248, 0.585, ub, 0)** | Keep anchors x₄=ub, x₅=0; follow agreed in-plane trend |
| f7 | raises x₁ off 0 | **(0, 0.217, 0.301, 0.191, 0.346, 0.724)** | x₁=0 anchor; one further monotone descent step |
| f8 | perturbs anchors | **x₃ = 0.130** | Deterministic; parabolic vertex identified analytically |

## Performance (best-confirmed, 12-round record)

| Fn | Best value | Status |
|----|------------|--------|
| f1 | 3.449e-07 | Solved-geometric |
| f2 | 0.723740 | Noise-limited |
| f3 | −0.035216 | Curvature/noise-limited |
| f4 | 0.55365 | Open-ascending |
| f5 | 4440.4943 | Saturated |
| f6 | −0.513059 | Slow-converging |
| f7 | 2.285415 | Converging-decelerating |
| f8 | 9.8596846 | Solved-exact (deterministic) |

**Realised Round-13 outputs are not ingested** for f1–f7 (portal returns not captured before
termination); f8 is deterministic and equals **9.8596846** at the submitted vertex.

## Limitations & known failure modes

- **Data starvation:** 12 points in up to 8 dimensions makes the GP gradient unreliable in
  high dimensions; global EI chased high-variance corners (mitigated by the trust region and
  override layer).
- **Convergence warnings:** L-BFGS-B `ABNORMAL_TERMINATION_IN_LNSRCH` and hyperparameters
  railing to bounds are expected on flat sparse-data likelihoods; they are warnings, not
  errors, and the multi-restart fit returns valid kernels.
- **Stochastic f2:** no surrogate refinement converges; only repeated sampling tightens the
  mean. The system correctly declines to extrapolate.
- **Reproducibility nuance:** cross-check coordinates are seed/draw-dependent; final
  submission strings are hard-coded and deterministic.

## Ethical / risk notes

Synthetic benchmark with no personal data and no real-world actuation. The transferable
caution is generic to BO: in any physical deployment, acquisition must be constrained to a
safe operating envelope, and a model-drift detector must trigger fallback to direct
experience when dynamics shift.
