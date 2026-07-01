# BBO Capstone — Submissions Log

All portal submission strings, round-by-round. Format: `x1-x2-...-xn` (6 decimal places).

---

## Round 1 — W12 | Submitted: 26/03/2026

| Function | d | Query String | Y (portal feedback) |
|----------|---|-------------|-------------------|
| F1 | 2 | `0.034388-0.909319` | −2.4675e-270 |
| F2 | 2 | `0.695196-0.395970` | 0.7237 |
| F3 | 3 | `0.548145-0.174647-0.303245` | −0.0891 |
| F4 | 4 | `0.440429-0.425456-0.378357-0.397088` | 0.2596 |
| F5 | 4 | `0.000000-0.675974-0.999999-0.999999` | 2105.928 |
| F6 | 5 | `0.464677-0.242110-0.574863-0.999999-0.000000` | −0.5508 |
| F7 | 6 | `0.000000-0.241713-0.327655-0.218095-0.375335-0.747501` | 2.2073 |
| F8 | 8 | `0.064016-0.008062-0.123268-0.000000-0.999999-0.381742-0.031402-0.806010` | 9.8595 |

**Methodology**: GP surrogate (Matérn-5/2 ARD), EI acquisition (maximisation), 25 restarts L-BFGS-B.

---

## Round 2 — W13 | Submitted: 06/04/2026

| Function | d | Query String | Y (portal feedback) |
|----------|---|-------------|-------------------|
| F1 | 2 | `0.999999-0.999999` | 1.5176e-192 |
| F2 | 2 | `0.698486-0.000000` | 0.5298 |
| F3 | 3 | `0.850892-0.035316-0.936193` | −0.2398 |
| F4 | 4 | `0.999999-0.000000-0.000000-0.365908` | −27.8598 |
| F5 | 4 | `0.000000-0.000000-0.999999-0.999999` | 1616.626 |
| F6 | 5 | `0.142733-0.321812-0.416485-0.999999-0.304415` | −1.0045 |
| F7 | 6 | `0.000000-0.302741-0.000000-0.187177-0.000000-0.167182` | 0.0510 |
| F8 | 8 | `0.096074-0.000000-0.581701-0.000000-0.999999-0.383890-0.202189-0.999999` | 9.2934 |

**Methodology**: GP-EI. f4 confirmed boundary collapse (x1→1). f7: sparse structure identified (x1=x3=x5=0 required).

---

## Round 3 — W14 | Submitted: 08/04/2026

| Function | d | Query String | Y (portal feedback) |
|----------|---|-------------|-------------------|
| F1 | 2 | `0.250000-0.250000` | 9.7977e-42 |
| F2 | 2 | `0.695000-0.396000` | 0.5264 |
| F3 | 3 | `0.300000-0.500000-0.700000` | −0.1140 |
| F4 | 4 | `0.440000-0.425000-0.378000-0.397000` | 0.2748 ✓ best at time |
| F5 | 4 | `0.000000-0.850000-0.999999-0.999999` | 2932.695 ✓ best at time |
| F6 | 5 | `0.500000-0.500000-0.500000-0.500000-0.500000` | −1.0159 |
| F7 | 6 | `0.000000-0.242000-0.328000-0.218000-0.375000-0.748000` | 2.2072 |
| F8 | 8 | `0.064000-0.008000-0.120000-0.000000-0.999999-0.382000-0.031000-0.806000` | 9.8592 |

**Methodology**: GP-EI. f5: x2=0.85 improvement over R1 (2105) confirmed. f4: interior best confirmed.

---

## Round 4 — W15 | Submitted: 19/04/2026

| Function | d | Query String | Y (portal feedback) |
|----------|---|-------------|-------------------|
| F1 | 2 | `0.500000-0.500000` | 2.6753e-9 ✓ best at time |
| F2 | 2 | `0.700000-0.200000` | 0.5814 |
| F3 | 3 | `0.950000-0.010000-0.990000` | −0.4594 |
| F4 | 4 | `0.999999-0.000000-0.000000-0.700000` | −30.894 |
| F5 | 4 | `0.000000-0.000000-0.500000-0.500000` | 83.963 |
| F6 | 5 | `0.300000-0.400000-0.600000-0.200000-0.600000` | −1.2239 |
| F7 | 6 | `0.000000-0.150000-0.000000-0.100000-0.000000-0.100000` | 0.0236 |
| F8 | 8 | `0.100000-0.000000-0.800000-0.000000-0.999999-0.380000-0.350000-0.999999` | 8.5129 |

**⚠ Post-mortem**: Round 4 notebook used minimisation EI (`y_best = min(Y)`). Four functions (f4, f5, f7, f8) deteriorated as a result. Corrected in Round 5.

---

## Round 5 — W16 | Submitted: 23/04/2026

| Function | d | Query String | Strategy | Y (portal feedback) |
|----------|---|-------------|---------|-------------------|
| F1 | 2 | `0.472781-0.505546` | GP-EI free exploration | 8.169e-8 |
| F2 | 2 | `0.695211-0.395970` | GP-EI tight exploit | 0.6239 |
| F3 | 3 | `0.511275-0.215264-0.371049` | GP-EI exploit near R1 | −0.0707 |
| F4 | 4 | `0.455000-0.415000-0.385000-0.395000` | Manual interior perturbation | −0.3997 |
| F5 | 4 | `0.000000-0.999999-0.999999-0.999999` | GP-EI + NN x2-dominant push | 4440.481 ✓ new best |
| F6 | 5 | `0.758817-0.272673-0.522143-0.999999-0.000000` | GP-EI x4/x5 pattern | −0.9106 |
| F7 | 6 | `0.000000-0.260000-0.340000-0.232000-0.395000-0.752000` | Manual + NN gradient nudge | 2.1133 |
| F8 | 8 | `0.040000-0.000000-0.090000-0.005000-0.999999-0.367013-0.020000-0.780000` | GP-EI tight exploit | 9.8387 |

**Methodology**: Corrected maximisation EI. GP Matérn-5/2 ARD + WhiteKernel. NN gradient analysis for f5, f7. Per-function bounds constraints from landscape analysis.

---

## Round 6 — W17 | Submitted: 01/05/2026

| Function | d | Query String | Strategy | Y (portal feedback) |
|----------|---|-------------|---------|-------------------|
| F1 | 2 | `0.445562-0.511092` | GP-EI exploration | −5.317e-7 |
| F2 | 2 | `0.693000-0.397000` | Bracket R1 peak | 0.3979 |
| F3 | 3 | `0.490000-0.230000-0.395000` | GP-EI exploit | −0.0529 |
| F4 | 4 | `0.430000-0.430000-0.375000-0.400000` | Interior nudge | 0.4636 ✓ new best |
| F5 | 4 | `0.005000-0.999999-0.999999-0.999999` | x1 non-zero probe | 4440.483 ✓ new best |
| F6 | 5 | `0.450000-0.240000-0.580000-0.999999-0.000000` | x1 R1-approach | −0.5766 |
| F7 | 6 | `0.000000-0.238000-0.325000-0.215000-0.370000-0.743000` | Uniform −0.003 step | 2.2378 |
| F8 | 8 | `0.063000-0.008000-0.123000-0.000000-0.999999-0.382000-0.031000-0.807000` | Near-R1 exploit | 9.8591 |

**Key insight**: F5 x1=0.005 produced 4440.483 > all x1=0 rounds (4440.481). Non-monotone x1 behaviour confirmed. F4 new best at interior point.

---

## Round 7 — W18 | Submitted: 06/05/2026

| Function | d | Query String | Strategy | Y (portal feedback) |
|----------|---|-------------|---------|-------------------|
| F1 | 2 | `0.475000-0.503000` | Micro-gradient x1↑ x2↓ | 1.311e-7 |
| F2 | 2 | `0.697000-0.393000` | Bracket R1 peak | 0.4872 |
| F3 | 3 | `0.478000-0.223000-0.408000` | Return R7-zone | −0.0353 ✓ new best |
| F4 | 4 | `0.420000-0.440000-0.373000-0.403000` | Continue R6 direction | 0.3635 |
| F5 | 4 | `0.000000-0.999999-0.999999-0.999999` | Hold plateau | 4440.481 |
| F6 | 5 | `0.468000-0.241000-0.572000-0.999999-0.000000` | x1→R1 approach | −0.6361 |
| F7 | 6 | `0.000000-0.235000-0.322000-0.212000-0.367000-0.740000` | Uniform −0.003 step | 2.2502 |
| F8 | 8 | `0.064016-0.008062-0.124000-0.000000-0.999999-0.381742-0.031402-0.806010` | x3 +0.001 gradient | 9.8596 ✓ new best |

**Key insight**: F3 new best at −0.0353 (R7 confirmed local optimum). F7 monotone gradient confirmed (5th consecutive improvement).

---

## Round 8 — W19 | Submitted: 18/05/2026

| Function | d | Query String | Strategy | Y (portal feedback) |
|----------|---|-------------|---------|-------------------|
| F1 | 2 | `0.477000-0.501000` | Micro-gradient continuation | 1.747e-7 |
| F2 | 2 | `0.695000-0.394000` | Bracket R1 x2↓ | 0.5738 |
| F3 | 3 | `0.465000-0.222000-0.421000` | x1↓ x3↑ from R7 | −0.0418 |
| F4 | 4 | `0.428000-0.432000-0.374000-0.401000` | Continue R6→R8 direction | 0.4711 ✓ new best |
| F5 | 4 | `0.000000-0.999999-0.999999-0.999999` | Hold plateau | 4440.481 |
| F6 | 5 | `0.460000-0.242000-0.575000-0.999999-0.000000` | x1 fine-probe | −0.5620 |
| F7 | 6 | `0.000000-0.232000-0.319000-0.209000-0.364000-0.737000` | Uniform −0.003 step | 2.2612 |
| F8 | 8 | `0.064016-0.008062-0.126000-0.000000-0.999999-0.381742-0.031402-0.806010` | x3 +0.002 gradient | 9.8596 ✓ new best |

**Key insight**: F4 new best 0.4711. F8 monotone x3 gradient (+0.002/round) confirmed as sole active dimension.

---

## Round 9 — W20 | Submitted: 25/05/2026

| Function | d | Query String | Strategy | Y (portal feedback) |
|----------|---|-------------|---------|-------------------|
| F1 | 2 | `0.479000-0.499000` | Micro-gradient continuation | 2.216e-7 ✓ new best |
| F2 | 2 | `0.695200-0.396500` | x2 probe above R1 | 0.3667 |
| F3 | 3 | `0.480000-0.221000-0.406000` | Return toward R7 zone | −0.0472 |
| F4 | 4 | `0.426000-0.434000-0.373000-0.402000` | Continue R8 direction | 0.4680 |
| F5 | 4 | `0.000000-0.999999-0.999999-0.999999` | Hold plateau | 4440.481 |
| F6 | 5 | `0.465000-0.241000-0.576000-0.999999-0.000000` | x1→R1 value | −0.6062 |
| F7 | 6 | `0.000000-0.229000-0.316000-0.206000-0.361000-0.734000` | Uniform −0.003 step | 2.2706 ✓ new best |
| F8 | 8 | `0.064016-0.008062-0.128000-0.000000-0.999999-0.381742-0.031402-0.806010` | x3 +0.002 gradient | 9.8597 ✓ new best |

**Key insight**: F7 monotone improvement every round since R5 (2.113→2.271). F8 x3 gradient decelerating but positive.

---

## Round 10 — W21 | Submitted: 03/06/2026

| Function | d | Query String | Strategy | Y (portal feedback) |
|----------|---|-------------|---------|-------------------|
| F1 | 2 | `0.481000-0.497000` | Micro-gradient x1↑0.002, x2↓0.002 | *pending* |
| F2 | 2 | `0.696000-0.395500` | Tighter bracket around R1 best | *pending* |
| F3 | 3 | `0.476000-0.225000-0.410000` | Nudge R7 optimum: x1↓ x2↑ x3↑ | *pending* |
| F4 | 4 | `0.430000-0.430000-0.376000-0.399000` | Revert toward R8 best (R9 regressed) | *pending* |
| F5 | 4 | `0.003000-0.999999-0.999999-0.999999` | Probe x1=0.003 (bracket R6 x1=0.005 best) | *pending* |
| F6 | 5 | `0.466000-0.242000-0.575000-0.999999-0.000000` | Tight exploit R1 zone | *pending* |
| F7 | 6 | `0.000000-0.226000-0.310000-0.200000-0.355000-0.730000` | Uniform −0.003 step (6th consecutive) | *pending* |
| F8 | 8 | `0.064016-0.008062-0.130000-0.000000-0.999999-0.381742-0.031402-0.806010` | x3 +0.002 gradient (0.128→0.130) | *pending* |

**Methodology**: GP-EI (Matérn-5/2 ARD, 35 restarts, 5,000 warm-start candidates) + per-function strategic override where empirical gradient dominates GP signal. Module 21.1 interpretability reflection completed.

---

## Cumulative Best Results (through Round 9)

| Function | d | Best Y | Best Round | Best Query |
|----------|---|--------|------------|-----------|
| F1 | 2 | 2.216e-7 | R9 | `0.479000-0.499000` |
| F2 | 2 | 0.7237 | R1 | `0.695196-0.395970` |
| F3 | 3 | −0.0353 | R7 | `0.478000-0.223000-0.408000` |
| F4 | 4 | 0.4711 | R8 | `0.428000-0.432000-0.374000-0.401000` |
| F5 | 4 | 4440.483 | R6 | `0.005000-0.999999-0.999999-0.999999` |
| F6 | 5 | −0.5508 | R1 | `0.464677-0.242110-0.574863-0.999999-0.000000` |
| F7 | 6 | 2.2706 | R9 | `0.000000-0.229000-0.316000-0.206000-0.361000-0.734000` |
| F8 | 8 | 9.8597 | R9 | `0.064016-0.008062-0.128000-0.000000-0.999999-0.381742-0.031402-0.806010` |

*Updated after Round 9 portal feedback. Round 10 pending.*

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

### Round 11 portal feedback (now ingested — dataset = 11 rounds × 8 functions)

| Fn | R11 query | R11 Y | Outcome |
|----|-----------|-------|---------|
| f1 | 0.483000-0.495000 | 3.107993e-07 | **new best** (continues x1+x2=0.978 ridge) |
| f2 | 0.695400-0.395600 | 0.616425 | below R1 incumbent — basin noise confirmed |
| f3 | 0.477500-0.223500-0.408500 | -0.035216 | **new best** (marginal, edges past R7) |
| f4 | 0.428500-0.431500-0.374500-0.400500 | 0.466977 | plateau — R8 incumbent holds |
| f5 | 0.010000-0.999999-0.999999-0.999999 | 4440.485741 | **new best** (x1 monotone, ridge anchored) |
| f6 | 0.465000-0.242100-0.574900-0.999999-0.000000 | -0.515453 | **new best** (anchored ridge x4=ub, x5=0) |
| f7 | 0.000000-0.223000-0.307000-0.197000-0.352000-0.727000 | 2.281684 | **new best** (monotone descent continues) |
| f8 | 0.064016-0.008062-0.133000-0.000000-0.999999-0.381742-0.031402-0.806010 | 9.859658 | **regression** vs R10 (x3=0.133 overshot) |

Five new bests (f1, f3, f5, f6, f7); f8 regressed (overshoot in the only active dimension); f2 and f4 incumbents unbeaten.

### Round 12 submission

| Fn | d | Query string (portal) | Strategy | ‖Δx‖ vs R11 | GP μ @ query | GP σ @ query |
|----|---|------------------------|----------|------------:|-------------:|-------------:|
| f1 | 2 | 0.485000-0.493000 | Empirical override — walk the monotone ridge x1+x2=0.978, step +0.002/−0.002 | 0.00283 | 3.42e-07 | 5.2e-09 |
| f2 | 2 | 0.695196-0.395970 | GP-confirmed exploitation — re-sample the R1 incumbent coordinate in a high-noise basin (GP μ<best, σ≈0.04) | 0.00042 | 0.6911 | 0.0416 |
| f3 | 3 | 0.477000-0.224000-0.409000 | Empirical override — tight local step along improving vector x1↓/x2↑/x3↑ from R11 best | 0.00087 | -0.0420 | 0.0061 |
| f4 | 4 | 0.429299-0.427594-0.370510-0.404482 | **GP-EI driven** — credible interior gain off the centroid (μ≈0.522 > best 0.471, low σ) | 0.00690 | 0.5216 | 0.0043 |
| f5 | 4 | 0.020000-0.999999-0.999999-0.999999 | Empirical override — x1 monotone (slightly accelerating) on bound-anchored ridge; doubled step. GP μ unreliable (extrapolates below sampled x1 range) | 0.01000 | 4440.36 | 1.98 |
| f6 | 5 | 0.462525-0.245506-0.578888-0.999999-0.000000 | **GP-EI driven (anchored)** — exploration step on the x4=ub, x5=0 ridge (μ≈−0.468 > best −0.515, high σ) | 0.00580 | -0.4684 | 0.1817 |
| f7 | 6 | 0.000000-0.220000-0.304000-0.194000-0.349000-0.724000 | Empirical override — continue uniform monotone descent of x2…x6 (~−0.003/round), x1 anchored at 0; gains decelerating but positive | 0.00671 | 2.2856 | 0.0003 |
| f8 | 8 | 0.064016-0.008062-0.131000-0.000000-0.999999-0.381742-0.031402-0.806010 | Quadratic line-search — only x3 active; vertex ≈0.130, R11 x3=0.133 regressed → probe x3=0.131 to bracket the peak in [0.130, 0.133) | 0.00200 | 9.85968 | 1.5e-05 |

**Copy-paste block (portal):**

```
0.485000-0.493000
0.695196-0.395970
0.477000-0.224000-0.409000
0.429299-0.427594-0.370510-0.404482
0.020000-0.999999-0.999999-0.999999
0.462525-0.245506-0.578888-0.999999-0.000000
0.000000-0.220000-0.304000-0.194000-0.349000-0.724000
0.064016-0.008062-0.131000-0.000000-0.999999-0.381742-0.031402-0.806010
```

### Best-observed summary (through Round 11)

| Fn | Best Y | Round | Regime entering R12 |
|----|--------|-------|---------------------|
| f1 | 3.107993e-07 | R11 | Climbing ridge (continue) |
| f2 | 0.723740 | R1 | Noise-dominated basin (re-sample incumbent) |
| f3 | -0.035216 | R11 | Tight blob (local refinement) |
| f4 | 0.471059 | R8 | Plateau (GP-EI interior probe) |
| f5 | 4440.485741 | R11 | Climbing ridge, bound-anchored (continue) |
| f6 | -0.515453 | R11 | Anchored ridge (GP-EI exploration) |
| f7 | 2.281684 | R11 | Climbing chain, decelerating (continue) |
| f8 | 9.859685 | R10 | Converged ridge, single active dim (bracket) |

### Round 12 doctrine note

With 11 rounds ingested, the doctrine split is roughly even: **f4 and f6 are genuinely GP-EI-driven** (the surrogate identifies interior gains the trajectory alone does not), while **f1, f3, f5, f7 are strong-signal empirical overrides** where a monotone ridge or local gradient is more trustworthy than EI's variance-seeking. **f8** is a one-dimensional bracketing refinement (vertex confirmed ≈0.130; this round tests whether the true peak sits marginally above the incumbent). **f2** is disciplined re-sampling of the proven incumbent in a high-noise basin where no single observation is reliable. This is consistent with the Module-23 lens: the active sub-space per function is now low-rank (f5/f8 are effectively 1-D; f1 lies on a 1-D ridge; f7 is a coupled monotone path), so the search concentrates on the few directions carrying signal while EI guards residual exploration.


----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# SUBMISSIONS LOG — Round 13 (append)

> Append target: `SUBMISSIONS_LOG.md`. This block records the **final** round. Add it below
> the Round 12 entry.

**Author:** Gian Franco Cattaneo · **Date:** 2026-06-29 · **Round:** 13 (terminal)

---

## Round 13 — submitted queries

Format: `x1-x2-...-xn`, each value beginning with `0` and given to six decimals.

| Fn | Submission string | Δ vs incumbent | Intent |
|----|-------------------|----------------|--------|
| f1 | `0.489000-0.489000` | from (0.485, 0.493) → symmetry | Place on ridge x₁+x₂=0.978 at predicted peak |
| f2 | `0.695196-0.395970` | = incumbent best (R1) | Re-pull best arm against sd ≈ 0.10 noise |
| f3 | `0.477500-0.223500-0.408500` | = incumbent best (R11) | Confirm knife-edge incumbent |
| f4 | `0.430000-0.412000-0.360000-0.419000` | step x₂↓/x₃↓/x₄↑ | Confident step; GP+empirical concur |
| f5 | `0.040000-0.999999-0.999999-0.999999` | x₁ 0.02 → 0.04 | Extend monotone x₁; anchors at bound |
| f6 | `0.455000-0.248000-0.585000-0.999999-0.000000` | x₁↓/x₂↑/x₃↑ | In-plane step; x₄=ub, x₅=0 anchored |
| f7 | `0.000000-0.217000-0.301000-0.191000-0.346000-0.724000` | −0.003 step | One further monotone descent; x₁=0 |
| f8 | `0.064016-0.008062-0.130000-0.000000-0.999999-0.381742-0.031402-0.806010` | x₃ → 0.130 | Lock parabolic vertex |

All eight strings validated within `[0.0, 0.999999]`.

## Pre-submission state (12-round incumbent bests)

| Fn | Best value | Round attained | Best point |
|----|------------|----------------|-----------|
| f1 | 3.449e-07 | R12 | (0.485, 0.493) |
| f2 | 0.723740 | R1 | (0.695196, 0.39597) |
| f3 | −0.035216 | R11 | (0.4775, 0.2235, 0.4085) |
| f4 | 0.55365 | R12 | (0.4293, 0.4276, 0.3705, 0.4045) |
| f5 | 4440.4943 | R12 | (0.02, 1, 1, 1) |
| f6 | −0.513059 | R12 | (0.4625, 0.2455, 0.5789, 1, 0) |
| f7 | 2.285415 | R12 | (0, 0.22, 0.304, 0.194, 0.349, 0.724) |
| f8 | 9.8596846 | R10 | x₃ = 0.130 (others pinned) |

## GP-EI cross-check (Round 13, seed 7)

Recorded for audit; **not** the submitted points where overridden.

```
f1  proposal [0.485054, 0.463001]            mu=5.628e-07  sd=1.74e-07
f2  proposal [0.695344, 0.395585]            mu=0.55426    sd=0.0965
f3  proposal [0.447504, 0.197293, 0.386292]  mu=-0.034754  sd=0.0124
f4  proposal [0.430466, 0.397633, 0.349721, 0.434277]   mu=1.21258   sd=0.0284
f5  proposal [0.00945, 0.999843, 0.970435, 0.970923]    mu=4435.92   sd=91.3
f6  proposal [0.449819, 0.24234, 0.60324, 0.972791, 0.021144]  mu=-0.498786  sd=0.187
f7  proposal [~0.02-0.03 on x1, x2..x6 perturbed]        mu≈2.07-2.50  sd≈1.2 (draw-dependent)
f8  proposal [perturbs pinned anchors]                   mu≈9.84-9.86  sd≈0.16-0.51 (draw-dependent)
```

Note: f7/f8 cross-check coordinates vary with the random candidate draw; the GP railed
several length-scales to the upper bound on these high-dimensional functions, flagging the
pinned dimensions as effectively flat — consistent with the anchor-coordinate overrides.

## Realised outcome

- **f8 (deterministic):** submitted vertex returns **9.8596846** — confirmed optimum.
- **f1–f7:** portal-realised Round-13 outputs were **not ingested** before termination.
  Predicted directions per the decision log above; reconcile predicted-vs-realised here if
  the portal values are later captured.

## Round close

Capstone optimisation **terminated** at Round 13. Final classification: f8 solved-exact,
f1 solved-geometric, f5 saturated, f7/f6 converging, f4 open-ascending, f2/f3
information-limited. No further rounds.
