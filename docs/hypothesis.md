# Research hypotheses

## Topic and task

**LLM Librarian: automatic document organization for more effective RAG.**

Input: a document corpus and a user question. Output: an answer with references to the original supporting passages. The proposed approach automatically splits documents into semantic chunks, groups chunks using embeddings, and uses an LLM to assign short descriptive names to the groups. Retrieval first selects groups, then retrieves original chunks within them.

The study focuses on the retrieval component of RAG. An agent loop with tools can be added later; it is unnecessary for testing the main hypotheses and would introduce additional experimental variables.

## Testable claims

**H1 — Quality.** With the same corpus, embedding model, answer generator, and context budget, two-stage retrieval over semantic clusters with LLM-generated names improves Answer F1 over the stronger of two baselines: dense RAG and hybrid BM25 + dense RAG. The baseline is selected on validation data before final testing.

**H2 — Efficiency.** Allowing an Answer F1 reduction of at most 1 percentage point, cluster-based retrieval reduces median retrieval latency by at least 20% relative to the same baseline. End-to-end answer latency is evaluated separately: faster retrieval alone does not imply faster generation.

**H3 — Contribution of names.** Cluster names provide an additional improvement over the same clusters routed using centroids alone. If there is no difference, the retrieval benefit of LLM-generated names is unsupported, even if the names are useful to human readers.

The proposed practical threshold for H1 is an Answer F1 gain of at least 2 percentage points on the 0–100 scale, with a paired 95% bootstrap confidence interval for the difference entirely above zero. H2 is supported if the 95% confidence interval for the F1 difference is entirely above −1 percentage point and the interval for the ratio of median latencies is entirely below 0.8. For H3, record the directional F1 difference and its interval; account for multiple comparisons when testing all three hypotheses. These thresholds are project decisions rather than established properties of the method and must be fixed before the final test.

No improvement, or an improvement requiring expensive indexing, is a valid research outcome. H1 and H2 are evaluated separately: the original claim of being “faster and more accurate” is supported only if both hold.

## Proposed method

1. Clean documents while preserving source identifiers, headings, and sentence boundaries.
2. Split text at semantic boundaries subject to minimum and maximum length constraints. Preserve mappings from chunks to source sentences.
3. Compute embeddings and build flat clusters. Tune the algorithm, cluster count, or cluster selection rule on validation data.
4. Give representative chunks from each cluster to an LLM and obtain a short name using a shared prompt. Record the model, prompt, and cost. A name is metadata rather than evidence.
5. Represent each cluster by its centroid and, separately, the embedding of its name. Rank clusters using a weighted sum of their similarities to the question; select the weight and number of clusters on validation data.
6. Rank original chunks within the selected clusters by similarity to the question and assemble the context within the shared token budget.
7. Generate an answer with source references using the same generator and prompt as the baselines.

A global retrieval fallback is allowed only as a separate configuration with its latency included. Otherwise, it could conceal routing errors.

## Comparisons and ablations

| Configuration | Purpose |
| --- | --- |
| Fixed-size chunks + dense retrieval | Standard RAG baseline |
| The same chunks + BM25 | Lexical control |
| The same chunks + BM25/dense fusion | Strong hybrid baseline |
| Semantic chunks + global dense retrieval | Contribution of chunking without clusters |
| Semantic chunks + global hybrid retrieval | Control for clustering against hybrid retrieval with identical chunking |
| Semantic chunks + cluster centroids | Contribution of restricting the search space |
| The same clusters + centroids and LLM-generated names | Full method; contribution of names |
| RAPTOR-style organization with summaries | Closely related published approach, if the budget permits |

Use the same context token budget for the main comparisons and also report the actual number of chunks. Specify chunk sizes, top-k, candidate budgets, and fusion rules. For latency comparisons, baselines should use a reasonable approximate nearest neighbor (ANN) index: comparing only against exhaustive search could create an artificial advantage for clustering.

## Data and leakage prevention

[HotpotQA](https://hotpotqa.github.io/) is a candidate for the first experiment: questions, answers, and annotated supporting facts allow evaluation of both answers and retrieved evidence. The final dataset and sample size have not been selected.

For a small pilot, construct a shared deduplicated corpus in advance from the contexts of a selected distractor subset. All systems search this shared corpus rather than a separate document set for each question. This is a **derived, restricted experiment** whose results cannot be compared directly with official fullwiki or distractor leaderboards.

Use the training set for development, a fixed portion of the official development set for validation, and a disjoint portion of that development set as a local held-out test. This is not the official test split. Publish the seed and ID lists, check duplicate questions, and report document overlap separately. Documents associated with test questions may be indexed as available knowledge; test questions, answers, and supporting-fact labels must not be used to construct clusters or names or to tune parameters.

Before running experiments, record the source, version, license, cleaning rules, and corpus statistics. The official website distributes HotpotQA under CC BY-SA 4.0; preserve the terms applicable to the data and derived artifacts. Evaluate answers using the official EM/F1 normalization.

## Metrics and reproducibility

- **Primary quality metric:** Answer F1; additional metrics include Exact Match, document Recall@k, nDCG@k, and coverage of annotated supporting facts in the context actually provided to the generator.
- **Latency:** p50/p95 retrieval and end-to-end generation latency, measured separately with a warmed index; use identical hardware, concurrency, question order, and caching policy. Account for network API calls separately.
- **Cost:** indexing time and cost, embedding/LLM tokens, API requests, memory, index size, and cost per question. Total cost for N questions is `C_index + N * C_query`; show the break-even point if one exists.
- **Reliability:** at least three seeds for stochastic clustering; repeated latency measurements and paired confidence intervals over the same questions. Save answers, retrieved IDs, scores, and run parameters.
- **Error analysis:** evidence lost during routing, inaccurate names, mixed topics, factual lookup questions, and multi-hop questions. Define question subtypes before inspecting final results.

## Research contribution and scope

Clustering and LLM-based knowledge organization are not, by themselves, claims of novelty: [RAPTOR](https://arxiv.org/abs/2401.18059) already uses recursive embedding, clustering, and summarization for retrieval. The proposed contribution is a controlled evaluation of flat organization with short names, the individual components, and the trade-off between quality and cost.

A single restricted corpus supports conclusions only about the chosen protocol. A SotA claim requires comparable published results on the same task, corpus, split, and metrics; outperforming only our own baselines is insufficient.
