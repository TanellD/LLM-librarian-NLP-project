# Project plan and grading criteria

## Basis

This plan follows the assignment criteria and the local report template in `submission/main.tex`. Overleaf remains outside Git; research decisions and reproducibility materials are maintained here.

The assignment states a maximum of **60 points**. The listed required components total **35 points**: 2 + 3 + 10 + 10 + 10. It also lists up to 15 bonus points for SotA on your dataset and up to 25 for SotA on a task with previous art. These bonuses should not automatically be added together: their interaction and the overall cap require clarification from the instructor. The plan does not assume a bonus will be earned.

## Graded deliverables

| Component | Assignment points | Evidence to provide |
| --- | ---: | --- |
| Report | 2 | Abstract with a code link; all template sections; tables, references, and limitations |
| Repository | 3 | README, installation, reproduction commands, configurations, seeds, data download, and result tables |
| Related Work | 10 | Comparison of approaches using primary sources, their results, and evaluation conditions |
| Dataset and task definition | 10 | Inputs/outputs, provenance and license, preprocessing, splits, statistics, and annotations |
| Proposed and competing methods | 10 | Algorithms, parameters, shared evaluation protocol, ablations, and error analysis |
| SotA on your dataset | up to 15 | Annotations and comparable strong methods; a single in-house baseline is insufficient |
| SotA with previous art | up to 25 | Direct comparison with published results under the same task definition |

## Report structure from the template

1. **Abstract:** task, method, measured result, and repository link.
2. **Introduction / Team:** motivation, contribution, team members, and responsibilities.
3. **Related Work:** dense, sparse/hybrid retrieval, and knowledge organization; differences from RAPTOR.
4. **Model Description:** chunking, embedding, clustering, names, routing, and complexity.
5. **Dataset:** source, license, collection/annotation, statistics, and splits.
6. **Experiments / Metrics / Experiment Setup / Baselines:** metric definitions, models, parameters, budget, and seeds.
7. **Results:** tables for the proposed and competing methods, confidence intervals, cost, answer examples, and errors.
8. **Conclusion:** findings relative to the hypotheses, limitations, and future work.

English is the primary language of the project and the report. The template recommends English and also permits Russian.

## Milestones and completion criteria

1. **Task definition:** fix hypotheses, corpus, budget, metrics, splits, and success criteria before testing. Expand Related Work and record published results with their evaluation conditions.
2. **Data:** implement download and preprocessing, save a manifest with versions and checksums, and verify mappings from supporting facts to source sentences and chunks.
3. **Baselines:** reproduce dense, BM25, and hybrid retrieval with a shared format for predictions and evaluation results.
4. **Method:** add semantic chunking, clustering, and LLM-generated names; cache indexing and log expenses.
5. **Experiments:** tune parameters on validation data, freeze configurations, and run the final comparison and ablations on held-out questions.
6. **Submission:** transfer verified results to Overleaf, add reproduction commands to the README, and document limitations. Performance claims must refer to actual run artifacts.

## Planned implementation artifacts

Create directories as their contents become available; the current setup does not contain a working pipeline.

| Path | Contents |
| --- | --- |
| `src/` | Data preparation, chunking, retrieval, clustering, generation, and evaluation |
| `configs/` | Models, method parameters, seeds, and budgets |
| `scripts/` | Data download and comparison commands |
| `data/` | Local datasets, excluded from Git |
| `artifacts/` | Local indexes and caches, excluded from Git |
| `runs/` | Raw logs and predictions, excluded from Git |
| `results/` | Compact tables, run configurations/IDs, and links to accessible predictions |
| `docs/` | Task definition, protocol, Related Work, and reproduction instructions |
| `submission/` | Local Overleaf report, excluded from Git |

Publish large reproducibility artifacts separately with versions and checksums. Keep API secrets local. Team composition, compute budget, models, and the final dataset are not yet defined.
