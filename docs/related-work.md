# RAG retrieval: methods, related research, and resource costs

Prepared on **19 November 2026**. This short reading review uses ten sources published in **2022–2025**. It distinguishes peer-reviewed papers, research preprints, and first-party engineering reports. The publication cutoff is 2022 inclusive; older foundational papers are outside this review. Search methods and verification details are recorded in the [search log](source-search-log.md).

## 1. How search supplies content to RAG

RAG builds a searchable external corpus, retrieves passages for a question, and places those passages into the generator's prompt. It does not necessarily search the web: the index can contain a fixed local collection. Gao et al. describe the basic pipeline as indexing, retrieval, and generation; their review also covers routing and hybrid search. Short quotation: “retrieves the top K chunks” (§II-A). [Gao et al., 2023; revised March 2024, survey preprint](https://arxiv.org/html/2312.10997v5#S2.SS1).

An operational view, synthesized from that survey and the Contextual Retrieval report below:

```text
Offline: documents → clean / split → representations → searchable index
Online:  question → retrieve candidates → optional reranking → context → LLM answer
```

| Search method | What it matches | Why include it in this project? |
| --- | --- | --- |
| Sparse / BM25 | Lexical terms with frequency-based relevance scoring | A control for exact names, identifiers, and terminology |
| Dense retrieval | Similarity between query and passage embeddings | The semantic-search baseline |
| Hybrid retrieval | Combined sparse and dense rankings | Tests whether clustering improves over complementary retrieval signals |
| Reranking | A second model scores an initial candidate set | Tests whether better candidate selection explains an apparent gain |

The first three distinctions are covered in [Gao et al., §III-D](https://arxiv.org/html/2312.10997v5#S3.SS4). Anthropic's concrete reranking setup retrieves 150 candidates and keeps 20; it explicitly warns about added runtime cost. Quotation: “an inherent trade-off between reranking more chunks for better performance” (Cost and latency considerations). [Anthropic, 19 September 2024, engineering report](https://www.anthropic.com/research/contextual-retrieval).

A further option is **late interaction**: ColBERTv2 represents documents using multiple token vectors instead of one passage vector. Its authors report a **6–10× reduction in late-interaction index footprint**, relative to the late-interaction models they compare against. This is not a 6–10× saving relative to every dense index. Quotation: “reducing the space footprint of late interaction models by 6–10x” (abstract). [Santhanam et al., NAACL 2022](https://aclanthology.org/2022.naacl-main.272/).

## 2. Research closest to LLM Librarian

| Study and publication status | Mechanism and short source quotation | Connection to our hypothesis |
| --- | --- | --- |
| **RAPTOR — Sarthi et al., ICLR 2024** | Builds a hierarchy from chunks using “recursively embedding, clustering, and summarizing chunks of text” (abstract). [Conference paper](https://proceedings.iclr.cc/paper_files/paper/2024/hash/8a2acd174940dbca361a6398a4f9df91-Abstract-Conference.html) | The closest precedent. Our proposed flat clusters with short names need comparison against summarized hierarchies. |
| **GraphRAG — Edge et al., 2024; v2 February 2025, Microsoft research preprint** | Extracts entities and relationships, forms communities, and generates community summaries. Quotation: “pregenerate community summaries” (abstract). [Paper, §§3–5](https://arxiv.org/html/2404.16130v2) | Relevant to corpus-wide thematic questions; its evaluation of comprehensiveness and diversity differs from factual Answer F1. |
| **LightRAG — Guo et al., Findings of EMNLP 2025; preprint 2024** | Combines graph and vector representations with local/global retrieval. Quotation: “a dual-level retrieval system” (abstract). [Published paper](https://aclanthology.org/2025.findings-emnlp.568/) | A graph-based efficiency comparator; its incremental updates also motivate measuring index maintenance. |
| **Contextual Retrieval — Anthropic, September 2024, engineering report** | Adds document-derived context to individual chunks before embedding and BM25 indexing. Quotation: “prepending chunk-specific explanatory context to each chunk” (Introducing Contextual Retrieval). [Report](https://www.anthropic.com/research/contextual-retrieval) | A direct control for LLM-generated metadata: does a cluster name help beyond contextualizing each chunk? |

RAPTOR's Appendix A studies construction on an **Apple M1 with 16 GB RAM**, for documents of **12,500–78,000 tokens**, and observes approximately linear time/token scaling in that range. This is empirical scaling on those documents, not a general complexity guarantee. [RAPTOR, Appendix A](https://arxiv.org/html/2401.18059v1#A1).

Contextual Retrieval reports top-20 retrieval failures falling from **5.7% to 2.9%** with contextual embeddings plus contextual BM25, and to **1.9%** with reranking. Those are relative failure reductions of 49% and 67%, not Answer F1 improvements. Its estimated preprocessing cost is **$1.02 per million document tokens**, assuming 800-token chunks, 8,000-token documents, 100-token generated contexts, 50-token instructions, and prompt caching. These are vendor-reported results and assumptions. [Anthropic, Methodology and Cost sections](https://www.anthropic.com/research/contextual-retrieval).

## 3. What published measurements say about time and resources

**HippoRAG 2 — Gutiérrez et al., ICML 2025:** Appendix F/Table 12 uses **11,656 MuSiQue passages**, Llama-3.3-70B-Instruct, and **four H100 GPUs**. Quotation: “we ignore all memory for model weights”. [Publication record](https://proceedings.mlr.press/v267/gutierrez25a.html); [Table 12](https://arxiv.org/pdf/2502.14802#page=19).

| Method | Indexing LLM tokens, input / output | Indexing, min | Reported QA time/query, s | QA GPU memory, GB; excludes model weights |
| --- | ---: | ---: | ---: | ---: |
| Dense NV-Embed-v2 | — / — | 12.1 | 0.3 | 1.7 |
| RAPTOR | 1.7M / 0.2M | 100.5 | 0.6 | 1.4 |
| LightRAG | 68.5M / 18.3M | 235.0 | 13.3 | 4.5 |
| GraphRAG | 115.5M / 36.1M | 277.0 | 10.7 | 3.7 |
| HippoRAG 2 | 9.2M / 3.0M | 99.5 | 1.2 | 9.9 |

The dash does not imply free embedding compute. **QA time/query** is the authors' label; no retrieval/prefill/decoding breakdown is provided. Memory excludes model weights. These are implementation-specific measurements, not universal latency estimates. [Appendix F and Table 12](https://arxiv.org/html/2502.14802v2#A6).

Two additional checks illustrate why protocols matter:

- **GraphRAG's own experiment:** indexing approximately one million podcast tokens took **281 minutes**, using GPT-4-turbo through an API and a VM with 16 GB RAM / Xeon Platinum 8171M. This measures an API-backed pipeline, not processing entirely on that CPU. [Edge et al., §4.1.3](https://arxiv.org/html/2404.16130v2#S4.SS1.SSS3).
- **LightRAG's legal-corpus experiment:** Table 3 contrasts approximately **610,000 tokens** for GraphRAG community processing with **fewer than 100** for LightRAG keyword generation/retrieval. Quotation: “a single API call” (§4.4). These counts cover different retrieval procedures and exclude final answer generation; they are not total tokens per answer or claims about every current GraphRAG configuration. [Guo et al., §4.4, Table 3](https://aclanthology.org/2025.findings-emnlp.568.pdf#page=7).

For comparison with passing the whole document to a model, Li et al. report that **Self-Route** lowers their cost measure by **65% for Gemini-1.5-Pro** and **39% for GPT-4o** relative to long-context processing. Their analysis uses **input-token percentage** as the cost proxy; these are not measurements of total infrastructure spend or seconds saved. Quotation: “RAG’s significantly lower cost remains a distinct advantage” (abstract). [Li et al., EMNLP Industry 2024, §§4–5](https://aclanthology.org/2024.emnlp-industry.66.pdf).

## 4. What this means in dollars

The following are **our calculations using dated published price snapshots**, not measured project costs or a current purchasing quote. They show how to budget reproducibly without treating historical rates as today's tariffs.

Anthropic's **24 February 2025** announcement priced Claude 3.7 Sonnet at **$3 per million input tokens** and **$15 per million output tokens**. Quotation: “$3 per million input tokens and $15 per million output tokens”. [Official announcement](https://www.anthropic.com/news/claude-3-7-sonnet).

Assume one standard generation call, a 500-token question/instruction prompt, a 500-token answer, no thinking tokens, and no caching or discounts:

`generation_USD = input_tokens * 3 / 1,000,000 + output_tokens * 15 / 1,000,000`

| Retrieved context | Total input | Generation/query | Generation/1,000 queries |
| ---: | ---: | ---: | ---: |
| 1,500 tokens | 2,000 tokens | $0.0135 | $13.50 |
| 6,000 tokens | 6,500 tokens | $0.0270 | $27.00 |
| 20,000 tokens | 20,500 tokens | $0.0690 | $69.00 |

These figures exclude embeddings, reranking, cluster naming, hosting, storage, networking, evaluation, and retries. The implication is arithmetic: reducing candidate search work saves generation money only if it also reduces billable model work or infrastructure use.

For embeddings, Voyage AI's **18 September 2024** announcement lists voyage-3-lite at **$0.02 per million tokens**, with **512 dimensions**. Quotation: “at $0.02 per 1M tokens”. At that snapshot rate, embedding 10 million tokens once costs **$0.20**, before overlap, generated metadata, or re-embedding. [Official announcement](https://blog.voyageai.com/2024/09/18/voyage-3/).

Our storage calculation: one million 512-dimensional float32 vectors require `1,000,000 * 512 * 4 = 2.048 GB` (about 1.91 GiB) for raw vectors alone. ANN structures, text, metadata, replicas, and model weights are additional. Compression and different representations change this estimate.

## 5. Implications for our experiment

The evidence motivates measuring a quality–cost trade-off rather than assuming organization makes RAG faster. The following are proposed accounting rules, not literature measurements:

`T_answer = T_query_encoding + T_search + T_rerank + T_prefill + T_decode + T_network_and_queue`

`C_total(Q) = C_index + C_updates + Q * C_query + C_hosting + C_evaluation`

For example, if retrieval is 30% of total latency and we halve it, total speedup is `1 / (0.70 + 0.30 / 2) = 1.18x`, not 2x. If naming costs an extra $10 and saves $0.002/query, break-even is 5,000 queries, assuming all other costs are equal. Both examples are hypothetical calculations.

For LLM Librarian, compare dense, BM25 + dense, semantic chunking without clusters, centroid-only routing, and named-cluster routing. Add Contextual Retrieval and RAPTOR as the closest metadata/organization controls if the budget permits. Keep context and generation budgets equal. Record retrieval p50/p95, end-to-end p50/p95, Answer F1, evidence coverage, index build/update time, input/output tokens, index size, and peak RAM/VRAM. This will isolate whether names improve routing, whether that reduces total cost, and how many queries amortize preprocessing.

## Suggested reading order

1. **Gao et al.** for the vocabulary and pipeline (§§II–III).
2. **Anthropic Contextual Retrieval** for a concrete hybrid retrieval/reranking experiment.
3. **RAPTOR** for the closest clustering-based precedent; read Appendix A too.
4. **GraphRAG and LightRAG** for graph organization and global/local retrieval.
5. **HippoRAG 2, Appendix F/Table 12**, then **Li et al.** for resource accounting and the long-context alternative.
6. **ColBERTv2** for an alternative retrieval representation; the two pricing announcements for worked budgeting assumptions.
