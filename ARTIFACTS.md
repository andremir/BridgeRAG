# BridgeRAG — Artifact Release Notes

## Canonical results

| Dataset | R@5 | Source file |
|---------|-----|-------------|
| MuSiQue | **0.8146** | `eval/pilot_audit/ablation_recompute_results.json` → `musique.all.C_3way` |
| 2WikiMultiHopQA | **0.9527** | `eval/pilot_audit/proposal_h_2wiki_results.json` → `all_r5` |
| HotpotQA | **0.9875** | `eval/pilot_audit/proposal_h_hotpot_results.json` → `results.all_r5` |

A single machine-readable summary of all three canonical numbers is at
`eval/pilot_audit/bridgerag_canonical_results.json`.

---

## MuSiQue result — why three JSON files exist

The paper reports MuSiQue R@5 = **0.8146**, taken from the corrected ablation
recompute (`ablation_recompute_results.json`), which we treat as canonical.

Earlier saved runs show slightly different values:

| File | R@5 | Timestamp |
|------|-----|-----------|
| `proposal_h_musique_results.json` | 0.8138 | 2026-04-02 16:07 |
| `ablation_judge_musique_results.json` | 0.8141 | 2026-04-02 16:57 |
| **`ablation_recompute_results.json`** | **0.8146** | **2026-04-02 17:14** |

The differences are due to LLM judge non-determinism (Llama 3.3 70B AWQ sampling)
across independent scoring passes with identical hyperparameters (α=0.1, same pool).
The 0.0008 spread does not affect any comparison in the paper: BridgeRAG exceeds
PropRAG (0.783) by +3.1pp under all three values.

`ablation_recompute_results.json` is canonical because it is the most recent run
and the most controlled experiment (all A/B/C conditions scored on the exact same
cached pool). See `eval/pilot_audit/RESULTS_PROVENANCE.md` for full details.

---

## System

- **LLM**: Llama 3.3 70B AWQ, served via vLLM on NVIDIA A100 80 GB
- **Embeddings**: NV-Embed-v2 (4096-dim, pgvector ANN index)
- **Metric**: R@5 = mean(|gold ∩ top-5| / |gold|), full dev set
- **Hyperparameters**: α=0.1 (MuSiQue, 2Wiki), α=0.15 (HotpotQA), selected on disjoint tune split
