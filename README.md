# LLM Librarian

Automatic document organization for more effective retrieval-augmented generation (RAG).

Repository: <https://github.com/TanellD/LLM-librarian-NLP-project>

The project investigates whether semantic document chunking, embedding-based clustering, and LLM-generated cluster names help retrieve supporting evidence and answer questions under a limited budget.

**Primary language:** English for documentation, the report, code comments, and experiment descriptions.

## Status

The research plan is ready. Code, dependencies, datasets, and experimental results have not been added yet. No performance improvement or state-of-the-art (SotA) result is claimed.

## Documentation

- [Hypotheses and evaluation protocol](docs/hypothesis.md).
- [Project plan, grading criteria, and report structure](docs/project-plan.md).
- [Starting points for Related Work](docs/related-work.md).

## Local checkout

```powershell
git clone https://github.com/TanellD/LLM-librarian-NLP-project.git
cd LLM-librarian-NLP-project
```

The `submission/` directory contains local Overleaf files and is excluded entirely by the root `.gitignore`. It will not appear when cloning the repository and must be stored and synchronized separately. Repository setup leaves the report template sources unchanged.

Datasets, indexes, models, and raw runs are also excluded from Git. The repository should contain download instructions, configurations, split manifests, and compact result tables with provenance. Installation and reproduction commands will be added with the implementation; no experiment can be run yet.
