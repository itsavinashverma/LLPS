# Phase 1 Results — Three-Regime Benchmark on the LLPSense Dataset

Dataset: LLPSense curation of LLPSDB. 3,080 experimental records, 854 sequences,
397 proteins, expanded to 50,557 condition rows.
Features: 1024 ProtT5 dims + 13 environmental parameters.
Model: XGBoost with the published Optuna hyperparameters from `configs/LLPSense.yaml`.
Metrics are record-weighted (one experiment, one vote). 5-fold grouped CV,
repeated over several random partitions.

## The generalization ladder

| Regime | Held out | Folds | ROC-AUC | PR-AUC | Accuracy | Brier |
|---|---|---|---|---|---|---|
| **A** new condition, known protein | experimental records | 10 | **0.834 ± 0.003** | 0.848 | 0.759 | 0.172 |
| **B** new protein | sequences | 10 | **0.786 ± 0.005** | 0.804 | 0.719 | 0.204 |
| **C** new family (their groups) | `cluster/group` | 25 | **0.683 ± 0.009** | 0.714 | 0.625 | 0.248 |
| **C\*** new family (homology-hardened) | merged groups | 25 | **0.659 ± 0.009** | 0.685 | 0.606 | 0.258 |
| *control* random merge, same granularity | random 168 groups | 25 | 0.701 ± 0.008 | 0.725 | 0.649 | 0.236 |

(± is standard error of the mean. Majority baseline: ROC-AUC 0.500, PR-AUC 0.548.)

## Finding 1 — the ladder itself

Performance falls monotonically across every metric as more is held out:
0.834 -> 0.786 -> 0.683 -> 0.659.

LLPSense's published applications — condition screening, mutation ranking,
LCST/UCST reprogramming — all operate at the **top** of this ladder, on proteins in
or near the training set. Their reported evaluation is at the bottom. Nobody has
published the decomposition, and the 0.175 ROC-AUC spread between the top and
bottom rungs is what tells a user how far these models can be trusted.

## Finding 2 — their grouping leaks homologues

Their `cluster/group` column is clean at the sequence level (no sequence spans two
groups) but not at the homology level: clustering the 854 sequences at 50% identity
and 90% coverage gives 207 homology clusters, of which **26 (12.6%) are split across
more than one of their groups**. Ten protein names span groups — FUS mutants across
four, U1-70K mutants across three. Under their `GroupKFold`, a FUS mutant can train
while another FUS mutant is scored.

**The decisive comparison is the control, not C vs C\*.** C\* differs from C in two
ways: homologues merged, and fewer/coarser groups (168 vs 234). The random-merge
control holds granularity fixed at 168 groups with homology ignored, so only
homology differs between control and C\*.

| Comparison | Difference | Fold-level p | Seed-level paired p |
|---|---|---|---|
| **control vs C\*** (granularity matched) | **+0.042** | **0.0012** | **0.018** |
| C vs C\* (confounded) | +0.024 | 0.081 | 0.103 |
| C vs control | −0.018 | 0.131 | 0.037 |

Control exceeds C\* in **all five** random partitions. Coarsening alone does not
explain it: the random control performs at least as well as their original grouping.

**How to state this:** "At matched group granularity, removing homology leakage costs
0.042 ROC-AUC (p = 0.001)." Do **not** state "their evaluation is inflated by 0.024" —
the direct C vs C\* comparison is confounded by granularity and does not reach
significance (p = 0.08).

## Caveats to keep in the report

- The control used a **single** random merge mapping. Several mappings would make it
  stronger; the effect is large and consistent across CV seeds, but this is the
  obvious thing a reviewer would ask for.
- Folds within a seed share training data, so fold-level p-values are optimistic.
  The seed-level paired test is the conservative one, and it still clears 0.05 for
  the decisive comparison.
- C vs C\* is directionally consistent in 4 of 5 seeds but reverses in one. Report
  the control comparison as the result, not this one.

## Reproduction

- `benchmark_colab.ipynb` — single-seed, four regimes
- `repeated_benchmark_colab.ipynb` — repeated seeds plus the random-merge control
- `llpsense_final_noseq.csv` — 50,557 rows with record IDs, weights, both groupings
- `llpsense_t5.npz` — ProtT5 embeddings, 854 x 1024
- `raw_folds.json` — every individual fold score
