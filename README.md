# SORCERER: A Systematic Methodology For Deriving Contextual Legal Requirements

Replication package accompanying the FSE submission **“SORCERER: A Systematic Methodology For Deriving Contextual Legal Requirements.”**

Author information is omitted for anonymous review.

## Overview

SORCERER is a tool-supported methodology for deriving contextual legal requirements from judicial precedents in common-law jurisdictions. It extracts legal issues, rules, and material facts while preserving their relationships and traceability to source decisions.

The methodology comprises three phases:

1. **Case identification and selection:** identify relevant decisions for a legal task and jurisdiction and screen them against inclusion criteria.
2. **Expert manual legal requirements derivation:** use expert annotation and validation to develop extraction prompts, produce corrected legal artifacts, and retain expert assessments of the original model outputs.
3. **Automatic legal requirements derivation and evaluation:** apply the refined extraction prompt set at scale, assess extracted artifacts with an LLM-based extraction judge, and organize the artifacts into a legal knowledge base.

This package documents the Ontario estate-law instantiation and its downstream evaluation using a retrieval-augmented generation (RAG) legal assistant.

## Package contents

The accompanying paper’s Data Availability statement identifies the following materials:

| Material | Purpose |
| --- | --- |
| Case-selection prompt | Screen decisions against the study’s substantive-law and decision-completeness criteria. |
| Extraction prompt sets | Extract issues, rules, facts, and their mappings, including refinements informed by expert feedback. |
| Judge prompt sets | Support preliminary prompt development and assessment of the original extractions. |
| Expert-validated ground truth | Provide corrected legal elements and mappings from 22 decisions. |
| Expert assessments | Preserve assessments of the original LLM outputs for extraction-judge development and evaluation. |
| Automatically extracted knowledge | Provide the structured legal artifacts extracted from 329 decisions. |
| Downstream evaluation prompts | Specify question generation, query rewriting, reranking, answer generation, and pairwise answer evaluation. |
| Evaluation outputs | Record experimental outcomes, including win/tie/loss counts. |

The supplementary material (S1–S5) provides additional methodological details, rubrics, prompts, and evaluation procedures.

## Suggested review order

1. Read Section 3 of the paper for the methodology and Ontario instantiation.
2. Consult the supplementary material for prompt wording, expert evaluation criteria, and reference-label construction.
3. Inspect the expert-validated data alongside the expert assessments of the original extractions. These are distinct resources, as explained below.
4. Inspect the automatically extracted collection and the subsets used in the downstream experiments.
5. Compare the evaluation outputs with Section 4 and Tables 2–3 of the paper, using the aggregation rules below.

## Data and representation

A contextual legal requirement links a legal rule to the issue it addresses, associated material facts where applicable, and its source decision.

| Element or relationship | Description |
| --- | --- |
| Issue | A legal question addressed in a decision. |
| Rule | A legal proposition, including relevant qualifications and exceptions. |
| Fact | Factual material associated with the application of a rule, with attribution and court-reliance information. |
| Issue–rule mapping | A link between an issue and an associated rule. |
| Rule–fact mapping | A link between a rule and relevant factual material. |

These relationships preserve evidence of how rules were applied in their source decisions. They do not constitute an exhaustive specification of the conditions governing application to other cases.

### Expert reference data

Five experts completed validation of 22 decisions. The corrected ground truth comprises:

| Item | Count |
| --- | ---: |
| Issues | 59 |
| Rules | 295 |
| Facts | 452 |
| Issue–rule mappings | 281 |
| Rule–fact mappings | 1,083 |

**Corrected ground truth** contains the retained and corrected artifacts, including expert additions. **Reference labels** describe expert assessments of the LLM’s original extractions. An original item requiring substantive correction is not treated as an accepted original extraction merely because its corrected version appears in the ground truth. Items left unanswered are excluded according to the procedures in the paper and supplementary material.

### Automatic extraction and experimental subsets

Automatic extraction covered **329 decisions**, including cases assigned for expert validation. The expert-review and automatic-extraction collections are therefore not disjoint.

| Experiment | Retrieval corpus | Evaluation questions |
| --- | --- | --- |
| RQ1: Indexed decisions | 220 decisions | 160 questions from 80 indexed decisions. |
| RQ2: Held-out decisions | The same 220 decisions as RQ1 | 160 questions from 80 separate decisions excluded from the retrieval indexes. |
| RQ3: Fact mutations | A separately sampled set of 200 decisions, represented by original and mutated knowledge bases | 160 questions from 80 of those decisions, reused across conditions. |

RQ1 assesses use of knowledge from indexed decisions. RQ2 assesses transfer to held-out decisions within Ontario estate law. RQ3 compares original extractions with two separate fact mutations: small monetary perturbations of approximately 0.05% and meaning-reversing negations. The mutations alter facts while leaving rules unchanged.

## Evaluation procedures

### Extraction assessment

The **prompt-development judge** supports initial extraction-prompt development. The **extraction judge** assesses legal elements and mappings against the source decisions and is evaluated using expert reference labels. These roles are distinct from downstream answer evaluation.

For extraction-judge development and testing, one of the 22 validated decisions supplies in-context examples and is excluded from evaluation. The remaining decisions are split at the case level into five development decisions and 16 held-out test decisions.

The reported metrics are:

- **Accept precision:** the proportion of judge-accepted items that experts also accepted.
- **Reject recall:** the proportion of expert-rejected items that the judge also rejected.

The automatic extraction procedure triggers re-extraction when more than 20% of items are rejected, pooled across issues, rules, facts, and mappings. Re-extraction is repeated up to five times; if the acceptance threshold remains unmet, the attempt with the highest acceptance rate is retained. This operational threshold is distinct from the development criterion used to refine the judge prompts.

### Downstream answer assessment

The RAG pipeline uses separate fact and rule indexes. It retrieves 50 candidates from each index, jointly reranks the candidates, and retains the top 15 artifacts for answer generation. Each RAG configuration is compared with a no-retrieval baseline using the same generation backbone and decoding settings. The two backbones are Gemma 4 E4B and Claude Sonnet 4.6.

The **answer judge** compares answers on accuracy, groundedness, completeness, actionability, neutrality, and internal consistency. Each pair is evaluated in both answer orders. A system wins a criterion only when both evaluations select that system; otherwise, the outcome is a tie.

Win rates are calculated separately for each criterion:

```text
win rate (%) = 100 × wins / (wins + losses)
```

Ties are excluded from the denominator. For RQ1 and RQ2, wins favour the RAG configuration over the matched baseline. For RQ3, wins favour the original-extraction configuration over the corresponding mutated configuration.

## Access and reuse

Full texts of decisions obtained under an institutional database licence are **not redistributed**. Re-running stages that require the source judgments therefore requires separately obtaining authorized access to those texts. Model-dependent stages also require access to the models or services specified in the experimental configuration.

The corrected annotations support comparisons of extraction methods on the same decisions. Expert assessments support comparisons of extraction judges on the original outputs. New extractions require new expert judgments before they can serve as additional labelled examples.

The study evaluates one legal domain and jurisdiction using synthetic questions and model-based answer assessments. The RAG and baseline prompts differ, so observed effects concern the complete configurations. Consult the paper’s threats-to-validity discussion when interpreting the results or adapting the methodology to other settings.
