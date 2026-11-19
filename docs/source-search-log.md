# Source search and verification log

Search date: **19 November 2026**. Scope: retrieval inside RAG, document organization related to LLM Librarian, and measured or calculable time/resource/cost requirements. The resulting [short review](related-work.md) includes ten sources with publication dates from 2022 onward.

## Search methods actually used

1. **Broad topic search.** Queries included `retrieval augmented generation survey 2023 2024 indexing retrieval generation hybrid search arxiv Gao` and `RAG latency cost benchmark 2024 2025 retrieval augmented generation token cost RAG long context`. These identified survey and cost-comparison candidates.
2. **Targeted publisher and exact-title search.** Searched ACL Anthology for ColBERTv2, LightRAG, and the RAG versus long-context comparison, and opened the ICLR and PMLR proceedings records for RAPTOR and HippoRAG 2. Publication dates and venue status were checked against those records rather than search-result timestamps.
3. **Backward citation tracing.** Followed RAPTOR from Gao et al.'s bibliography and LightRAG from HippoRAG 2's references, then located the primary publications. This complements keyword search with links between related methods; it was not an exhaustive citation-network analysis.
4. **First-party engineering and price searches.** Searched Anthropic for Contextual Retrieval and dated model-price announcements, and Voyage AI for its dated embedding-model announcement. Vendor experiments are identified as engineering reports, not independent peer-reviewed replications.
5. **Full-text verification.** Read paper HTML/PDF sections and located exact phrases, tables, assumptions, and hardware descriptions. Verified HippoRAG 2 Table 12 and LightRAG Table 3 against PDF text, with paper HTML used as an additional check where available. Search snippets served discovery rather than the final source of numerical claims.

## Inclusion and interpretation rules

- Include sources published in **2022 or later**; date preprints and later conference publications explicitly. ColBERTv2 is cited as its 2022 NAACL publication, although its first preprint appeared in 2021.
- Prefer primary papers in ACL/ICLR/ICML proceedings, original research preprints, and dated first-party technical announcements. General blogs, aggregators, Reddit, and commercial summaries were excluded as evidence.
- Separate observed latency, input-token cost proxies, historical API prices, and our own arithmetic. Historical pricing is not presented as the current tariff or proof that an old model remains available.
- Attach short verbatim quotations to named sources and section/table locators. Keep each source's total quoted text below 25 words.
- Preserve measurement boundaries. HippoRAG 2's reported QA memory excludes model weights; its QA time column is not relabeled as pure retrieval latency. LightRAG's retrieval-token comparison is not treated as full answer-generation cost.

## Limits

This is a selective introductory review, not a systematic literature review, a comprehensive search through all 2026 publications, or a SotA claim. No project benchmark was run. Earlier references formerly listed in `related-work.md` (BEIR 2021 and HotpotQA 2018) are outside this review's date cutoff; HotpotQA remains a dataset candidate in the separate hypothesis document.
