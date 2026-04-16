# Baseline Sources

This file documents the exact source for every baseline number reported in the BridgeRAG paper.
We use published figures from the respective authors' arXiv papers; we did not re-run their systems.

---

## HippoRAG 2

| Dataset | R@5 reported in paper |
|---------|----------------------|
| MuSiQue | 0.747 |
| 2WikiMultiHopQA | 0.904 |
| HotpotQA | 0.963 |

- **Paper:** Tang et al., "HippoRAG 2: On the Limits of RAG Systems in the Human Brain" (2025)
- **arXiv:** https://arxiv.org/abs/2502.14802
- **GitHub:** https://github.com/OSU-NLP-Group/HippoRAG
- **Notes:** Numbers taken from Table 1 of the arXiv paper. HippoRAG 2 uses Personalized PageRank (PPR) over an offline entity graph. Embeddings: NV-Embed-v2 (same as BridgeRAG).

---

## PropRAG

| Dataset | R@5 reported in paper |
|---------|----------------------|
| MuSiQue | 0.783 |
| 2WikiMultiHopQA | 0.941 |
| HotpotQA | 0.974 |

- **Paper:** He et al., "PropRAG: Guiding Retrieval with Beam Search over Proposition Paths" (2025)
- **arXiv:** https://arxiv.org/abs/2501.16033
- **GitHub:** https://github.com/yinzhangyue/PropRAG
- **Notes:** Numbers taken from Table 1 of the arXiv paper. PropRAG uses an offline proposition index and graph traversal. Embeddings: NV-Embed-v2 (same as BridgeRAG).

---

## Benchmark Evaluation Parity

All three methods (HippoRAG2, PropRAG, BridgeRAG) use:
- **Embeddings:** NV-Embed-v2
- **Metric:** R@5 = mean(|gold ∩ top-5| / |gold|) on the full dev split
- **Datasets:** MuSiQue dev, 2WikiMultiHopQA dev, HotpotQA distractor dev (1,000-query subset matched via HippoRAG2 question IDs)

BridgeRAG's canonical results are in [`eval/pilot_audit/bridgerag_canonical_results.json`](eval/pilot_audit/bridgerag_canonical_results.json).
