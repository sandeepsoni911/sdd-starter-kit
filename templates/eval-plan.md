# Evaluation Plan — [APP NAME]

**Status:** [Draft | In Review | Approved]
**Owner (Tech Lead):** [Name]
**Owner (Data / ML):** [Name]
**Product reviewer:** [Name]
**Last updated:** YYYY-MM-DD
**Version:** 1

Related: [PRD](../prd.md) | [Technical Solution](./technical-solution.md) | [Implementation Phases](./implementation-phases.md)

Companion doc: [`docs/11-rag-and-agent-apps.md`](../../docs/11-rag-and-agent-apps.md)

---

## Purpose

For any LLM / RAG / agent application, unit tests protect determinism but **evaluation protects quality**. This plan defines: what we evaluate, against what benchmarks, with what thresholds, and how eval gates PR merges.

If your project has zero LLM calls, delete this file.

---

## 1. Evaluation dimensions

Which dimensions are in scope for this project? Check all that apply.

- [ ] **Retrieval quality** — do we retrieve the right chunks? (Recall@k, Precision@k, MRR, NDCG)
- [ ] **Generation faithfulness** — does the answer stay grounded in retrieved context? (Ragas faithfulness, GPT-judge groundedness)
- [ ] **Answer relevance** — does the answer address the question? (Ragas answer_relevance)
- [ ] **Citation accuracy** — do citations match the actual source? (Custom: URL/ID match rate)
- [ ] **Structured output correctness** — parsed output matches schema? (JSON validation pass rate)
- [ ] **Safety — toxicity** — % outputs flagged
- [ ] **Safety — PII leak** — % outputs containing PII that shouldn't leak
- [ ] **Safety — jailbreak resistance** — % adversarial prompts that bypass safety
- [ ] **Safety — prompt injection resistance** — % injected instructions honored
- [ ] **Latency** — P50, P95 end-to-end
- [ ] **Cost** — $ per query (avg + P95)
- [ ] **Agent task completion** — for agents: % of tasks completed successfully on benchmark

---

## 2. Framework choice

| Layer | Chosen tool | Alternatives considered |
|---|---|---|
| RAG eval | [e.g. Ragas] | DeepEval, TruLens, LangSmith Eval |
| Prompt / A-B eval | [e.g. promptfoo] | LangSmith Eval, custom |
| Agent eval | [e.g. LangGraph eval / custom] | AgentBench, custom |
| Judge model | [e.g. GPT-4o] | Claude 3.5 Sonnet, Llama-3.1-70B, ensemble |
| Traces / obs | [e.g. Langfuse] | LangSmith, Arize Phoenix, Weave |

**Rationale:** [1-2 sentences on why these were picked over alternatives.]

---

## 3. Gold datasets

### 3.1 Retrieval gold set

- **Location:** `capabilities/<app>/evaluation/datasets/gold-retrieval.jsonl`
- **Size:** [target: 100–500 rows]
- **Structure:**
  ```json
  {"query": "...", "expected_chunk_ids": ["c1", "c2", "c3"], "expected_metadata": {...}}
  ```
- **Labeling protocol:** [Who labels? How is "relevant" defined? Single-annotator or dual?]
- **Refresh cadence:** [monthly / when incidents occur]

### 3.2 Generation gold set

- **Location:** `capabilities/<app>/evaluation/datasets/gold-generation.jsonl`
- **Size:** [target: 50–200 rows]
- **Structure:**
  ```json
  {"query": "...", "expected_answer": "...", "expected_citations": ["src1", "src2"], "must_include": ["fact1"], "must_not_include": ["outdated_fact"]}
  ```
- **Labeling protocol:** ...

### 3.3 Safety gold set

- **Location:** `capabilities/<app>/evaluation/datasets/gold-safety.jsonl`
- **Categories:**
  - Toxicity (~30 rows)
  - Jailbreak attempts (~30 rows)
  - Prompt injection (~30 rows)
  - PII probes (~30 rows)
- **Structure:**
  ```json
  {"prompt": "...", "category": "jailbreak", "expected_behavior": "refuse", "expected_refusal_type": "policy"}
  ```

### 3.4 Edge case / regression set

- **Location:** `capabilities/<app>/evaluation/datasets/gold-regression.jsonl`
- **Purpose:** every past production bug becomes a row here — regression protection.
- **Structure:**
  ```json
  {"incident_id": "INC-...", "date": "YYYY-MM-DD", "query": "...", "expected_behavior": "...", "notes": "..."}
  ```

---

## 4. Metrics and thresholds

| Metric | Baseline (current main) | Threshold (block PR if worse than) | Rationale |
|---|---|---|---|
| **Recall@10 (retrieval)** | 0.82 | 0.80 (-2%) | Baseline set 2026-07-01 |
| **Precision@5 (retrieval)** | 0.76 | 0.74 (-2%) | ... |
| **Faithfulness (generation)** | 0.85 | 0.83 (-2%) | ... |
| **Answer relevance** | 0.81 | 0.78 (-3%) | ... |
| **Citation accuracy** | 0.94 | 0.92 (-2%) | ... |
| **Structured output pass rate** | 0.98 | 0.96 (-2%) | ... |
| **P95 latency** | 3.2s | 3.5s | ... |
| **Avg cost per query** | $0.006 | $0.008 | ... |
| **Toxicity flag rate** | 0.05% | 0.1% | ... |
| **Jailbreak success rate** | 0.8% | 1.5% | ... |

**Threshold policy:**
- Any metric worse than threshold → PR blocked (agent surfaces the diff).
- Baseline updated only via ADR when a metric legitimately improves (e.g., new retriever).
- Regression protection: any past incident row that fails is a hard block regardless of other metrics.

---

## 5. When evaluation runs

| Trigger | What runs | Duration | Blocking |
|---|---|---|---|
| **On PR touching `retrieval/`** | Retrieval eval + regression | ~5 min | Yes |
| **On PR touching `generation/` / `prompts/`** | Generation eval + safety + regression | ~10 min | Yes |
| **On PR touching `agents/` / `tools/`** | Agent task eval + regression | ~15 min | Yes |
| **On PR touching `ingestion/` / `embedding/`** | Retrieval eval (index rebuilt) | ~20 min | Yes |
| **Nightly on main** | Full eval suite + baseline recompute | ~45 min | No (alert on regression) |
| **Weekly** | Cost / latency deep-dive on production sample | ~30 min | No |

**Skill involvement:**
- Either extend `.cursor/skills/pr-readiness-check/SKILL.md` with an "Evaluation" gate step, or author `.cursor/skills/eval-suite-runner/SKILL.md` as a new skill.
- Same self-verification loop pattern (max 3 iterations) as `pr-readiness-check`.

---

## 6. CI integration

Example workflow snippet (GitHub Actions):

```yaml
- name: Retrieval eval
  if: contains(github.event.pull_request.changed_files, 'retrieval/') || contains(github.event.pull_request.changed_files, 'embedding/')
  run: |
    python -m evaluation.retrieval \
      --dataset datasets/gold-retrieval.jsonl \
      --baseline-recall-at-10 0.80 \
      --baseline-precision-at-5 0.74 \
      --output eval-results.json

- name: Generation eval
  if: contains(github.event.pull_request.changed_files, 'generation/') || contains(github.event.pull_request.changed_files, 'prompts/')
  run: |
    python -m evaluation.generation \
      --dataset datasets/gold-generation.jsonl \
      --baseline-faithfulness 0.83 \
      --output eval-results.json

- name: Comment eval results on PR
  uses: actions/github-script@v7
  with:
    script: |
      const fs = require('fs');
      const results = JSON.parse(fs.readFileSync('eval-results.json'));
      // Post metrics diff to PR
```

Adapt to your CI tool.

---

## 7. Human evaluation

Automated eval catches regressions; human eval catches subtle quality drift.

- **Weekly**: sample 20 production queries, spot-check outputs manually.
- **Per milestone**: 100-query human eval by product / SME.
- **Feedback loop**: every 👎 in production → auto-added to regression gold set as a candidate row.

---

## 8. Cost budget

- **Development eval cost:** ~$[N]/PR (running eval suite on each PR)
- **Nightly eval cost:** ~$[N]/night
- **Monthly eval budget cap:** $[N] (alert at 80%)
- **Judge model choice justification:** [e.g. "GPT-4o at $2.50/1M tokens is ~3× cheaper than opus and gives 92% agreement with human labels."]

---

## 9. Rollback plan

If a change ships that regresses production quality (caught by post-deploy telemetry):

- [ ] Revert PR + redeploy previous version
- [ ] Add failing case to `gold-regression.jsonl`
- [ ] Update threshold if legitimate baseline drift, else raise threshold as a bug fix

---

## 10. Change log

- YYYY-MM-DD (v1): Initial eval plan.
- YYYY-MM-DD (v1.1): Added [X].
