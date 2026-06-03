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

