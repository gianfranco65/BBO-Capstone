# Bayesian Black-Box Optimisation — Capstone Project

**Imperial College London · Executive Master in Machine Learning & Artificial Intelligence**  
**Gian Franco Cattaneo · COO, SALOV S.p.A.**

---

## Non-Technical Summary

This project applies Bayesian optimisation to eight unknown mathematical functions, each accepting between two and eight numerical inputs and returning a single score. The goal is to find the input combination that produces the highest score using as few experiments as possible — much like tuning a complex industrial process without being able to see inside it. A Gaussian Process surrogate model learns from each result to predict where the next best experiment should be run. Across ten submission rounds, the algorithm progressively refined its search, achieving consistent improvements on six of eight functions and identifying stable optima on two. The project demonstrates how data-efficient, transparent optimisation can replace exhaustive trial-and-error in real-world engineering and business contexts.

---

## Project Structure

```
bbo-capstone/
├── README.md                         ← this file
├── DATASHEET.md                      ← dataset description, provenance, limitations
├── MODEL_CARD.md                     ← model behaviour, assumptions, interpretability
├── SUBMISSIONS_LOG.md                ← portal submission strings, round-by-round record
├── requirements.txt                  ← Python dependencies
├── .gitignore
│
├── notebooks/
│   ├── BBO_Round1_W12.ipynb          ← Submission 1: initial GP-EI, d=2–8D
│   ├── BBO_Round2_W13.ipynb          ← Submission 2: updated surrogate, boundary analysis
│   ├── BBO_Round3_W14.ipynb          ← Submission 3: SVM classification framing
│   ├── BBO_Round4_W15.ipynb          ← Submission 4: NN surrogate + gradient analysis
│   ├── BBO_Round5_W16.ipynb          ← Submission 5: corrected maximisation EI, R1–R4 data
│   ├── BBO_Round6_W17.ipynb          ← Submission 6: empirical gradient rules introduced
│   ├── BBO_Round7_W18.ipynb          ← Submission 7: f3 local optimum confirmed
│   ├── BBO_Round8_W19.ipynb          ← Submission 8: f4 new best, f8 x3 gradient law
│   ├── BBO_Round9_W20.ipynb          ← Submission 9: f7 monotone trend confirmed (R5–R9)
│   └── BBO_Round10_W21.ipynb         ← Submission 10: exploitation + x1 probe (f5)
│
├── data/
│   └── DATA_SOURCES.md               ← external data description (no large files on GitHub)
│
├── plots/
│   ├── convergence/                  ← best Y per round per function
│   ├── gp_posteriors/                ← GP posterior slice plots per round
│   └── gradient_heatmaps/            ← NN gradient magnitude charts (Rounds 4–5)
│
└── submissions/
    └── SUBMISSIONS_LOG.md            ← copy-paste portal strings, all rounds
```

---

## Problem Statement

Eight synthetic black-box functions `f₁(x)` … `f₈(x)` are optimised sequentially under a strict query budget. Each function:

| Function | d | Search Space | Landscape signature (observed) |
|----------|---|-------------|-------------------------------|
| f1 | 2 | [0, 1)² | Effectively flat; tiny positive gradient near (0.479, 0.499); outputs ≈1e-7 |
| f2 | 2 | [0, 1)² | Sharp narrow peak at x≈(0.695, 0.396); R1 best (0.7237) unbeaten after 9 rounds |
| f3 | 3 | [0, 1)³ | All-negative; local optimum at (0.478, 0.223, 0.408) confirmed R7 |
| f4 | 4 | [0, 1)⁴ | Bimodal: interior good (+0.47), boundary catastrophic (−30); exploitation active |
| f5 | 4 | [0, 1)⁴ | Very high dynamic range; x2–x4=0.999999 locked; non-monotone near x1=0 |
| f6 | 5 | [0, 1)⁵ | All-negative; x4=0.999999, x5=0.000 structural constraints; R1 unbeaten (−0.5508) |
| f7 | 6 | [0, 1)⁶ | x1=0 locked; uniform −0.003/round gradient on x2–x6; monotone 5-round trend |
| f8 | 8 | [0, 1)⁸ | Near-plateau; x3 sole active gradient (+0.002/round); 7 other dims frozen |

**Objective**: maximisation. All functions framed as higher-is-better.

---

## Methodology

### Surrogate Model
- **Gaussian Process** with Matérn-5/2 ARD kernel + ConstantKernel + WhiteKernel
- `StandardScaler` applied to X before GP fitting; `normalize_y=True`
- `n_restarts_optimizer=10`, `random_state=42`
- Kernel: `ConstantKernel(1.0, (1e-3,1e3)) × Matern(ν=2.5, ARD, bounds=(1e-3,10)) + WhiteKernel(1e-4, (1e-8,1e-1))`

### Acquisition Function
**Expected Improvement (maximisation)**:

$$\text{EI}(x) = (\mu(x) - y^* - \xi)\,\Phi(Z) + \sigma(x)\,\phi(Z), \quad Z = \frac{\mu(x) - y^* - \xi}{\sigma(x)}$$

where $y^* = \max_i\, y_i$, $\xi = 0.01$.

### Acquisition Optimiser
Multi-start L-BFGS-B with **35 restarts**, seeded from the top-35 of **5,000 random candidates** evaluated on the EI surface. This warm-start strategy was introduced in Round 6 and significantly reduced premature convergence to local EI maxima.

### NN Surrogate (Rounds 4–5 only)
MLPRegressor: 2×32 hidden layers, ReLU, L-BFGS-B solver, α=1e-3. Used for finite-difference gradient analysis at the best-known point to identify dominant input dimensions. Superseded from Round 6 onward by empirical per-round gradient analysis.

### Strategic Override Rules (Rounds 6–10)
Where empirical signals dominate GP uncertainty, GP-EI suggestions are overridden by documented directional rules:

| Condition | Override rule |
|-----------|--------------|
| Confirmed plateau (f5) | Hold boundary coordinates; probe x1 non-monotone region |
| Monotone trend ≥3 rounds (f7, f8) | Continue extrapolated gradient direction |
| R_best regressed in two consecutive rounds (f3) | Return to best-ever coordinates with small perturbation |
| GP σ at query > 5× output range | Manual interior constraint applied |

All overrides are documented in the notebook's Cell 6 GP-vs-strategic comparison table.

---

## Results Summary (through Round 9)

| Function | d | R1 Y | Best Y | Best Round | Δ improvement | Status |
|----------|---|-------|--------|------------|--------------|--------|
| f1 | 2 | −2.5e-270 | 2.216e-7 | R9 | +∞ (from zero) | Micro-gradient active |
| f2 | 2 | 0.7237 | 0.7237 | R1 | — | R1 unbeaten; sharp peak |
| f3 | 3 | −0.0891 | −0.0353 | R7 | +0.054 | Local optimum confirmed |
| f4 | 4 | 0.2596 | 0.4711 | R8 | +0.211 | Exploitation active |
| f5 | 4 | 2105.9 | 4440.483 | R6 | +2334.6 | Plateau; x1 probe R10 |
| f6 | 5 | −0.5508 | −0.5508 | R1 | — | R1 unbeaten; structural BCs |
| f7 | 6 | 2.2073 | 2.2706 | R9 | +0.063 | Monotone gradient; 5 gains |
| f8 | 8 | 9.8595 | 9.8597 | R9 | +0.0002 | Near-plateau; x3 active |

---

## Key Insights

1. **Maximisation convention is critical**: Round 4 inadvertently used `y_best = np.min()` (minimisation EI), causing four function deteriorations. Corrected in Round 5 to `y_best = np.max()`. All subsequent rounds use the corrected formulation.

2. **ARD length-scales reveal structure**: short ARD length-scales (≤0.5) flag high-sensitivity dimensions without requiring domain knowledge. f8: x4, x7 most sensitive; f7: x5; f5: x2, x3.

3. **Boundary collapse in f4**: x1→1 triggers catastrophic output (−27 to −30). Interior constraint (x1∈[0.35, 0.50]) required from Round 5 onward to protect exploitation region.

4. **Empirical gradient laws (f7, f8)**: from Round 5 onward, output improvements follow a quantifiable per-round law. f7: uniform −0.003/round on x2–x6 (5 consecutive confirmations). f8: +0.002/round on x3 only. These laws supersede GP acquisition as the dominant decision signal.

5. **Non-monotone boundary behaviour (f5)**: R6 x1=0.005 produced 4440.483 vs 4440.481 for x1=0. The difference (0.002) is small but consistent, suggesting the true optimum lies at x1∈(0, 0.005) rather than exactly at x1=0. Round 10 probes x1=0.003.

6. **Acquisition warm-start matters**: upgrading from 25 to 35 restarts seeded from 5,000 candidates (Round 6) reduced EI surface misses and produced more consistent per-function improvements.

---

## Why Bayesian Optimisation

| Requirement | BO Solution |
|-------------|-------------|
| Expensive evaluations (1 query/round) | GP surrogate amortises each query across the whole input space |
| Non-linear, non-convex landscapes | Matérn-5/2 handles moderate roughness; ARD identifies inactive dimensions |
| Unknown noise level | WhiteKernel fits observation noise automatically |
| Exploration vs exploitation | EI balances both; strategic overrides encode accumulated landscape knowledge |
| High-dimensional (up to 8D) | ARD length-scales suppress inactive dimensions; empirical gradient rules focus search |
| Transparency requirement (Module 21) | GP-vs-strategic comparison table; per-function claim-level rationale in each notebook |

---

## Convergence Assessment

| Function | Convergence signal | Assessment |
|----------|--------------------|-----------|
| f1 | Outputs ≈1e-7, growing | Not converged; gradient marginal |
| f2 | R1 unbeaten after 9 rounds | Likely near global maximum; peak very narrow |
| f3 | R7 best; R8+R9 regressed | Local optimum confirmed; deeper search needed |
| f4 | R8 best; R9 regression small | Near local maximum; further gains marginal |
| f5 | Plateau 4440.481 (R5–R9) | Near maximum; x1 probe (R10) may yield small gain |
| f6 | R1 best after 9 rounds | Local or global maximum; boundary anchors confirmed |
| f7 | 5-round monotone trend | Not converged; deceleration visible; 2–3 rounds to plateau |
| f8 | Deceleration +6e-5/round | Near-plateau; maximum likely within 2–3 rounds |

---

## Data

Initial `.npy` datasets provided by the Imperial College London BBO portal at project start. Cumulative datasets updated after each portal submission. **Large arrays are not stored in this repository** — see `data/DATA_SOURCES.md`.

---

## Dependencies

See `requirements.txt`. Core: `numpy`, `scikit-learn`, `scipy`, `matplotlib`, `pandas`.

---

## Reproducibility

All notebooks use `np.random.seed(42)` and `random_state=42` throughout. GP kernel initialisations, acquisition warm-start random candidates, and L-BFGS-B restarts are all deterministic given the seed.

---

*Repository maintained as a living document. Rounds 1–10 complete. Final submission: Module 25, Imperial College London.*


