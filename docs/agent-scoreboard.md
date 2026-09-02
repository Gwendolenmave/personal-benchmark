# Agent Scoreboard

This file separates **public benchmark references** from **Delos project-specific runs**.

Public scores are context only. Never convert them into a Delos score.

Snapshot checked: **2026-09-03**

---

## Public reference — DeepSWE v1.1

Official source: <https://deepswe.datacurve.ai/>

DeepSWE v1.1 currently has 113 original long-horizon software-engineering tasks. Leaderboard models are run on mini-swe-agent for consistency.

Selected official results checked on 2026-09-03:

| Model / reasoning | Score |
|---|---:|
| Gemini 3.8 Flash [high] | 74% ± 1% |
| Claude Opus 5 [max] | 74% ± 4% |
| GPT-5.6 Sol [max] | 73% ± 3% |
| GLM-5.3 [max] | 69% ± 3% |
| GLM-5.3-Flash [max] | 63% ± 4% |
| Muse Spark 1.2 [xhigh] | 55% ± 2% |

These are **public benchmark anchors**, not Delos results.

---

## Public reference — Terminal-Bench

Official sources:

- <https://github.com/harbor-framework/terminal-bench>
- <https://hub.harborframework.com/>

Terminal-Bench is useful here mainly because its results are tied to a **model + agent/harness + environment configuration** rather than a naked model name.

This example file intentionally does **not** copy a Terminal-Bench leaderboard snapshot. Public leaderboards change, and any numerical reference should be checked against the live official source when used.

---

## Delos Benchmark runs

**No completed Delos Benchmark runs have been scored yet.**

Do not add a model to this table until it has actually completed the named frozen benchmark version and been graded.

| Model | Provider | Harness | Reasoning | Benchmark | Preflight | Admission | Practical | Retries | Verdict |
|---|---|---|---|---|---|---:|---|---:|---|

---

## Run record template

```text
Model:
Provider:
Harness:
Reasoning mode:
Benchmark version:
Date:

Preflight: PASS / FAIL

System reconstruction:
Verified problem discovery:
Open-source transfer:
Product / domain judgement:
Calibration & restraint:
Total:

Practical attempted: YES / NO
First attempt success: YES / NO / N/A
Retries:
Practical result:

Hard caps:
Verdict:
Notes:
```

---

## Rules

- Never infer a Delos score from DeepSWE, SWE-bench, Terminal-Bench, or subjective impressions.
- Record a new run when provider, harness, reasoning mode, tool access, or benchmark version changes materially.
- Keep old completed runs; do not rewrite history when a new model becomes the favorite.
- If the exam, scoring weights, hard caps, practical verifier, or admission thresholds change, create a new benchmark version.
