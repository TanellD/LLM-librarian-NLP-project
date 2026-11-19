# Related Work: starting points

This is an initial list of primary sources rather than a completed review for the 10-point component. The final review needs competing methods' results, metrics, and evaluation conditions. No unverified numerical results are included here.

| Source | Relevance to the project | Comparison considerations |
| --- | --- | --- |
| Sarthi et al., 2024. [RAPTOR: Recursive Abstractive Processing for Tree-Organized Retrieval](https://arxiv.org/abs/2401.18059) | Recursive clustering and summarization of chunks; the closest reference for knowledge organization | Distinguish flat named clusters from a tree of summaries; compare quality and index construction cost |
| Thakur et al., 2021. [BEIR: A Heterogenous Benchmark for Zero-shot Evaluation of Information Retrieval Models](https://arxiv.org/abs/2104.08663) | Retrieval evaluation across heterogeneous tasks; motivation for a lexical baseline | Separate retrieval quality from generation quality; fix the corpus, relevance annotations, and budget |
| Yang et al., 2018. [HotpotQA: A Dataset for Diverse, Explainable Multi-hop Question Answering](https://aclanthology.org/D18-1259/) | A dataset with multi-hop questions and supporting facts; a candidate for measuring evidence coverage | Keep distractor, fullwiki, and a derived restricted corpus distinct |

Official HotpotQA data and evaluation: <https://hotpotqa.github.io/>. The website specifies a CC BY-SA 4.0 license.

Before final experiments, expand the review to cover the original RAG approach, BM25, dense retrieval, hybrid fusion, and more recent hierarchical retrieval methods. For each, select a primary publication and record the task, corpus/split, generator, metrics, published results, and limitations. This review is neither exhaustive nor a verification of current SotA.
