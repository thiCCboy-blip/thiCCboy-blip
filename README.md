# AMRUTH Raj

Civil engineer (NIT Calicut) building and evaluating LLM systems for
document-heavy construction workflows — tender review, quantity verification,
bid comparison.

Currently seeking roles where applied AI meets pre-contract and estimating work:
Applied AI / AI deployment engineering, and GCC-region opportunities in
construction technology and project controls.

## Measuring retrieval instead of assuming it works

My tender-review agent originally matched BOQ line items to extracted tender
items using token-overlap scoring. I chose it for explainability, not accuracy,
and the README conceded it might miss synonyms. That is a guess, so I built a
fixture to test it.

The fixture is 20 labelled cases built from the two situations that break a
lexical matcher:

- **Synonym pairs** where the same work item is named differently
- **Hard negatives** differing by one distinguishing attribute

| Strategy | hit@1 | hit@3 | MRR | Backend |
| --- | ---: | ---: | ---: | --- |
| `token_overlap` | 0.200 | 1.000 | 0.575 | in-process |
| `tfidf` | 0.200 | 0.900 | 0.558 | in-process |

Two results, and the second one matters more than a win would have.

**The baseline is weak.** The correct item lands in the top three *every time*
and first in only one case in five.

**A better lexical scorer does not fix it.** TF-IDF scores the same 0.200. The
gap is not poorly weighted vocabulary, it is absent vocabulary: "reinforcement"
and "rebar" never co-occur in a candidate list, so no reweighting of the terms
that are present can surface the match. That negative result is the argument for
a semantic model rather than a better overlap function.

Of the 16 failures, 11 are synonym cases and 5 are hard negatives — and they
break for opposite reasons. Synonyms have no lexical bridge, so the correct item
scores near zero. Hard negatives have too much: "Tiling to wet area walls" vs
"Tiling to wet area floors" differ by one token, so the shared terms outweigh
the distinguishing one and the matcher is confidently wrong.

A semantic fix (embeddings + reciprocal rank fusion, fused by rank position so
no tuned weight is needed between incomparable scores) is implemented but
**unverified** — the API account had no credit when the run was recorded. The
harness reports the skip and names each backend rather than quietly showing
fewer rows. Both reported strategies run with no API key and no third-party
dependency, so the table reproduces on a clean clone.

→ [boq-tender-review-agent](https://github.com/thiCCboy-blip/boq-tender-review-agent) ·
[live app](https://amruu-boq-agent.streamlit.app/)

## Other work

**Tender Review Agent** — deployed extraction service on the OpenAI Responses
API using Structured Outputs with Pydantic validation. The LLM handles
unstructured extraction only; a deterministic layer handles matching, unit
normalization, and quantity checks, so every discrepancy traces to a source
excerpt rather than a model judgement call. Reports latency, token usage, and
configurable cost per run. 55 automated tests in CI.

**Pre-contract benchmark** *(private)* — a 26-criterion, 0–2 point rubric
grading a 3+ hour lump-sum municipal bid across quote leveling, markup
build-up, alternates, unit pricing, schedule analysis, and MWBE compliance.
11 planted professional errors, including a roof panel priced to 76,000 SF
against a 71,500 SF drawing area, six spec-mandated scopes omitted from
subcontractor quotes, and a 420-day milestone carrying 7 days of float against
an owner-controlled schedule risk. Golden figures regenerate from raw CSVs in
pure standard library, so grading is reproducible and the dataset cannot
silently drift.

I keep the pricing data private. A realistic tender package, even synthetic, can
be mistaken for live bid material — the rubric design is the part worth
publishing, and I'm happy to walk through it.

## Domain background

B.Tech Civil Engineering, NIT Calicut. Site execution at Sobha Constructions;
project documentation and reporting at Inspire Leap; BOQ and quantity take-off
internships at Samara Consultants and ULCCS. Comfortable in the vocabulary of
tender packages, which is what makes the fixtures above realistic.

Research: geopolymer concrete mix design and ASTM-standard lab testing, with
material quantity and cost estimation.

## Skills

Python · OpenAI Responses API · Structured Outputs · Pydantic · retrieval ·
evaluation design (hit@k, MRR, precision/recall/F1) · pytest · Streamlit ·
GitHub Actions · BOQ preparation · quantity take-offs · tender documentation ·
rate analysis · Primavera P6 · AutoCAD / Civil 3D
