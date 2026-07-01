# Black-Box Optimisation Capstone — Bayesian Optimisation over 8 Unknown Functions

**Author:** Gian Franco Cattaneo
**Programme:** Executive Master in Machine Learning & AI — Imperial Business School
**Module:** 25 — Reinforcement Learning & Optimisation (Capstone component 24.1)
**Status:** ✅ **Terminated at Round 13 (final round).**
**Last updated:** 2026-06-29

---

## 1. Objective

Maximise eight black-box functions of unknown analytic form, each accessible only through
a query–response portal. Inputs are bounded to the unit box, clipped at `0.999999`; outputs
are scalar. Dimensionality ranges from 2 to 8:

| Function | Dimensions | Objective |
|----------|-----------|-----------|
| f1 | 2 | maximise |
| f2 | 2 | maximise |
| f3 | 3 | maximise |
| f4 | 4 | maximise |
| f5 | 4 | maximise |
| f6 | 5 | maximise |
| f7 | 6 | maximise |
| f8 | 8 | maximise |

All objectives are framed as **maximisation**; any minimisation function is negated before
the acquisition step. This convention was corrected at Round 4→5 (see `SUBMISSIONS_LOG.md`).

## 2. Method (one-paragraph summary)

Each round fits an independent **Gaussian-Process surrogate** per function
(ConstantKernel × Matérn-5/2 with ARD length-scales + WhiteKernel) and proposes the next
query by maximising **Expected Improvement** over a trust region around the incumbent best.
The surrogate is used as a **cross-check, not an oracle**: with 12 observations in up to
8 dimensions the GP is data-starved, so a documented **override doctrine** lets a clean
empirical signal — ridge geometry, monotone gradient, hard anchor coordinate, or parabolic
vertex — govern the final decision where it is more reliable than the surrogate. Full
mathematics in `TECHNICAL_FOUNDATIONS.md`.

## 3. Final result (terminal state)

Best-confirmed objective from the complete 12-round record; the Round-13 query is the
terminal exploitation point. **Portal-realised Round-13 outputs were not ingested into
this repository**, except f8, which is deterministic and therefore known exactly.

| Fn | Best-confirmed value | Best point | Convergence status |
|----|----------------------|-----------|--------------------|
| f1 | 3.449e-07 | (0.485, 0.493) | Solved-geometric (ridge x₁+x₂=0.978, peak at symmetry) |
| f2 | 0.723740 | (0.695196, 0.39597) | Noise-limited (sd ≈ 0.10, irreducible) |
| f3 | −0.035216 | (0.4775, 0.2235, 0.4085) | Curvature/noise-limited (knife-edge basin) |
| f4 | 0.55365 | (0.4293, 0.4276, 0.3705, 0.4045) | Open-ascending (residual headroom) |
| f5 | 4440.4943 | (0.02, 1, 1, 1) | Saturated (corner, gains ~1e-3) |
| f6 | −0.513059 | (0.4625, 0.2455, 0.5789, 1, 0) | Slow-converging (anchored plane) |
| f7 | 2.285415 | (0, 0.22, 0.304, 0.194, 0.349, 0.724) | Converging-decelerating |
| f8 | 9.8596846 | x₃ = 0.130 (others pinned) | **Solved-exact** (deterministic, parabolic vertex) |

Two functions reached a structural ceiling (f5 saturated, f8 exact); two are information-
limited rather than search-limited (f2, f3 — only repeated sampling would tighten them);
f4 is the single function still on a productive gradient at termination.

## 4. Round 13 — final portal submission

```
f1: 0.489000-0.489000
f2: 0.695196-0.395970
f3: 0.477500-0.223500-0.408500
f4: 0.430000-0.412000-0.360000-0.419000
f5: 0.040000-0.999999-0.999999-0.999999
f6: 0.455000-0.248000-0.585000-0.999999-0.000000
f7: 0.000000-0.217000-0.301000-0.191000-0.346000-0.724000
f8: 0.064016-0.008062-0.130000-0.000000-0.999999-0.381742-0.031402-0.806010
```

## 5. Repository structure

```
.
├── README.md                       # this file
├── DATASHEET.md                    # dataset provenance, schema, per-function noise profile
├── MODEL_CARD.md                   # decision system: architecture, performance, limits
├── TECHNICAL_FOUNDATIONS.md        # GP-EI maths, override doctrine, RL framing
├── SUBMISSIONS_LOG.md              # round-by-round query log (R1–R13)
├── DATA_SOURCES.md                 # portal interface and feedback handling
├── requirements.txt
├── .gitignore
├── notebooks/
│   └── BBO_Round13_Submission_GianFranco_Cattaneo.ipynb
└── reflections/
    └── round13.md                  # Part 2 critical reflection
```

## 6. Reproducibility

The Round-13 notebook embeds the full 12-round dataset and runs standalone. The seed is
pinned (`np.random.seed(7)`). Note that the printed GP-EI **cross-check** coordinates depend
on the random candidate draw and may shift run-to-run; the **final submission strings are
hard-coded**, so the deliverable is deterministic regardless. `ConvergenceWarning` messages
during kernel fitting are benign (sparse-data likelihood flatness and noise-floor railing),
not errors — every function still returns a result.

```bash
pip install -r requirements.txt
jupyter notebook notebooks/BBO_Round13_Submission_GianFranco_Cattaneo.ipynb
```

## 7. RL framing

The project arc maps onto the module's reinforcement-learning themes: early **MAB-style
exploration** to locate each function's mass; a **reward-signal correction** at R4→5;
**feedback-driven local ascent** (Q-learning-like value refinement) through the middle
rounds; and **terminal exploitation** (ε → 0) at Round 13. The GP surrogate is the
model-based overlay; the realised outputs are the model-free signal that dominated.
