# BridgeRAG: Training-Free Bridge-Conditioned Retrieval for Multi-Hop Question Answering

**Andre Bacellar** · Preprint, 2026

> BridgeRAG ranks later-hop evidence by bridge-conditioned utility rather than query-only relevance — without a graph database, proposition index, or any training.

**[Project Page](https://andremir.github.io/BridgeRAG) · [Paper PDF](https://andremir.github.io/BridgeRAG/main.pdf) · [arXiv](#) · [Artifacts](eval/pilot_audit/bridgerag_canonical_results.json)**

---

## Results

Best published training-free R@5 under matched benchmark evaluation on all three standard MHQA benchmarks.

| Method | MuSiQue R@5 | 2WikiMultiHop R@5 | HotpotQA R@5 | Training | Graph |
|--------|------------|------------------|-------------|----------|-------|
| HippoRAG2 | 0.747 | 0.904 | 0.963 | No | Yes (PPR) |
| PropRAG | 0.783 | 0.941 | 0.974 | No | Yes (prop.) |
| **BridgeRAG (ours)** | **0.8146** (+3.1pp) | **0.9527** (+1.2pp) | **0.9875** (+1.35pp) | No | **No** |

Δ vs. PropRAG. R@5 = mean(|gold ∩ top-5| / |gold|). All methods use NV-Embed-v2 embeddings.

---

## The Core Idea

Standard retrieval scores every candidate by **s(q, c)** — similarity to the query alone. For multi-hop questions this is the wrong target. The second-hop passage is relevant not to the original query but to what the *first hop already found*.

BridgeRAG replaces **s(q, c)** with **s(q, b, c)**, where *b* is the top-1 first-hop "bridge" passage. A tripartite LLM judge attends jointly to the query and the bridge to identify which reasoning chain is active before ranking second-hop candidates.

### Motivating Example

| | |
|---|---|
| **Query** | "Who is the spouse of the actor who played the Terminator?" |
| **2-way judge s(q,c) — wrong** | "The Terminator is a 1984 film directed by James Cameron…" — film passage, high surface overlap, wrong chain. |
| **Bridge b (top hop-1)** | "Arnold Schwarzenegger is an Austrian-American actor and former politician…" |
| **3-way judge s(q,b,c) — correct** | "Maria Shriver is an American journalist and author, and the wife of Arnold Schwarzenegger…" |

---

## What Is New

- **Tripartite scoring.** The judge takes (question, bridge, candidate) rather than (question, candidate). This is the scoring target, not a hyperparameter.
- **Coverage and scoring are separable.** Dual-entity ANN expansion improves pool coverage independently; bridge-conditioned judging improves ranking over any fixed pool. The ablation confirms both effects cleanly.
- **Graph-free and training-free.** No offline entity graph, proposition index, or fine-tuning. Applicable to any new corpus with no preprocessing beyond passage embeddings.

---

## Mechanism Validation

Four controlled experiments — all reusing cached artifacts (zero extra LLM calls for three of four) — confirm the conditioning signal is:

- **Selective:** +2.55pp on parallel-chain queries (`bridge_comparison`, p<0.001, Bonferroni-corrected), ≈0 on single-chain subtypes.
- **Irreplaceable:** Substituting the retrieved bridge with SVO query text reduces R@5 by 2.1pp — worse than a random pool passage. Passage content, not retrieval intent, drives the gain.
- **Predictable:** cos(bridge, gold_hop2) positively correlates with per-query B→C improvement (Spearman ρ=0.104, p<0.001 on MuSiQue).
- **Mechanistically precise:** 18.7% of judge-induced top-1 changes improve R@5 on parallel-chain queries vs. 0.6% on single-chain queries. Not noise — correct decisions.

---

## Reproducibility

- **Models:** NV-Embed-v2 (embeddings) · Llama 3.3 70B AWQ (LLM judge, SVO generation, entity extraction)
- **Infrastructure:** vLLM on NVIDIA A100 80 GB · pgvector ANN index · fully local, no closed-source API calls
- **Cost per query:** 3 LLM calls · 6 ANN passes · ~4–5 seconds sequential
- **Artifacts:** [`eval/pilot_audit/bridgerag_canonical_results.json`](eval/pilot_audit/bridgerag_canonical_results.json) · [`ARTIFACTS.md`](ARTIFACTS.md)

---

## BibTeX

```bibtex
@article{bacellar2026bridgerag,
  title     = {{BridgeRAG}: Training-Free Bridge-Conditioned Retrieval
               for Multi-Hop Question Answering},
  author    = {Bacellar, Andre},
  journal   = {arXiv preprint arXiv:YOUR_ARXIV_ID},
  year      = {2026}
}
```
