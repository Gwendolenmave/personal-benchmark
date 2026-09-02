# Agent Scoreboard

This file is an example of how to keep **public benchmark anchors** separate from **your own project benchmark results**.

Do not numerically convert one benchmark into another.

A DeepSWE score of 70% does **not** mean a Delos score of 70/100.

Snapshot date: **2026-09-03**

---

## Public reference — DeepSWE v1.1

DeepSWE currently evaluates 113 original long-horizon software-engineering tasks and runs leaderboard models on **mini-swe-agent** for consistency.

Selected current results:

| Model / reasoning | Score | Avg output tokens | Avg agent steps |
|---|---:|---:|---:|
| Gemini 3.8 Flash [high] | 74% ± 1% | 143k | 166 |
| Claude Opus 5 [max] | 74% ± 4% | 118k | 99 |
| GPT-5.6 Sol [max] | 73% ± 3% | 60k | 61 |
| Claude Fable 5 [max] | 70% ± 4% | 119k | 88 |
| GLM-5.3 [max] | 69% ± 3% | 80k | 124 |
| Kimi K3 [max] | 69% ± 5% | 81k | 98 |
| GLM-5.3-Flash [max] | 63% ± 4% | 73k | 123 |
| Muse Spark 1.2 [xhigh] | 55% ± 2% | 99k | 101 |

Source: <https://deepswe.datacurve.ai/>

Use these as **priors**, not as Delos admission scores.

A model can have a strong public coding score and still misunderstand your project's ownership, live/candidate boundaries, historical state, or product constraints.

---

## Public reference — Terminal-Bench 4.0.0

Terminal-Bench is especially useful as a reminder that an Agent benchmark measures the **model + agent/harness** combination rather than a naked language model.

Selected public Harbor runs:

| Model | Agent / harness | Avg reward | Trials | Errors | Retries |
|---|---|---:|---:|---:|---:|
| Claude Opus 5 | Claude Code | 0.53 | 330 | 6 | 3 |
| GPT-5.6 Sol | Codex | 0.37 | 330 | 6 | 8 |
| GPT-5.6 Terra | Codex | 0.22 | 330 | 8 | 7 |
| Claude Sonnet 5 | Claude Code | 0.12 | 330 | 37 | 2 |

Sources:

- <https://hub.harborframework.com/datasets/terminal-bench/terminal-bench/latest?leaderboard=4-0-0&tab=leaderboard>
- <https://github.com/harbor-framework/terminal-bench>

Again, these are contextual anchors only.

---

## Delos Benchmark leaderboard

This table records **project-specific** runs.

Never fill a score from public benchmark intuition. A row belongs here only after the model actually runs the frozen Delos benchmark version.

| Model | Provider | Harness | Reasoning | Benchmark | Preflight | Admission | Practical | Retries | Verdict |
|---|---|---|---|---|---|---:|---|---:|---|
| Muse Spark 1.3 | OpenCode Free | OpenCode | TBD | Delos v1.0 | pending | — | — | — | first candidate |

The first planned Delos candidate is **Muse Spark 1.3 via OpenCode**.

Suggested later comparison candidates may include:

```text
GLM-5.3-Flash + OpenCode
Muse Spark 1.3 + OpenCode
dots3-note + OpenCode
other model/harness combinations
```

Keep old rows when new models arrive. The point of a benchmark notebook is longitudinal comparison, not replacing yesterday's result with today's favorite model.

---

## Why the table stores harness metadata

Agent benchmarks do not test a naked language model.

A useful mental model is:

```text
observed capability
=
model
+ provider behavior
+ reasoning configuration
+ agent harness
+ tools
+ environment
```

Therefore do not collapse:

```text
GPT-5.6 Sol + Codex
```

into merely:

```text
GPT-5.6 Sol
```

if your goal is to compare real coding-Agent performance.

The same principle applies to personal benchmarks. If you change OpenCode → Codex, provider routing, reasoning mode, tool permissions, or repository access, record the new run separately.

---

## Suggested run record

```text
Model:
Provider:
Harness:
Reasoning:
Benchmark version:
Date:

Preflight:

System reconstruction:
Problem discovery:
OSS transfer:
Domain / product judgement:
Calibration & restraint:
Total:

First attempt:
Retries:
Practical result:

Hard caps:
Verdict:
Notes:
```

---

## Version rule

If you later change any of these:

```text
candidate prompt
scoring weights
hard caps
hidden Practical verifiers
admission thresholds
```

create a new benchmark version instead of overwriting the old one.

A leaderboard is only meaningful when the rows were produced by comparable exams.
