# Agent Scoreboard

Public benchmark scores are context only. Never convert them into a Delos score.

Snapshot checked: **2026-09-03**

## Public reference — DeepSWE v1.1

Official source: <https://deepswe.datacurve.ai/>

| Model / reasoning | Score |
|---|---:|
| Gemini 3.8 Flash [high] | 74% ± 1% |
| Claude Opus 5 [max] | 74% ± 4% |
| GPT-5.6 Sol [max] | 73% ± 3% |
| GLM-5.3 [max] | 69% ± 3% |
| GLM-5.3-Flash [max] | 63% ± 4% |
| Muse Spark 1.2 [xhigh] | 55% ± 2% |

These are **public benchmark anchors**, not Delos results.

## Public reference — Terminal-Bench

Official sources:

- <https://github.com/harbor-framework/terminal-bench>
- <https://hub.harborframework.com/>

Terminal-Bench is useful here mainly because agent results depend on the **model + harness + environment**, not a naked model name. This file intentionally does not copy a moving Terminal-Bench leaderboard snapshot.

## Delos Benchmark runs

| Model | Provider | Harness | Reasoning | Benchmark | Preflight | Admission | Practical | Verdict |
|---|---|---|---|---|---|---:|---|---|
| Muse Spark 1.3 | OpenCode Free | OpenCode | not reported | Delos v1.0 | PASS | **80/100** | pending | Core Practical eligible |

### Muse Spark 1.3 — 2026-09-03

| Dimension | Score |
|---|---:|
| System reconstruction | 24 / 25 |
| Verified problem discovery | 23 / 30 |
| Open-source transfer | 14 / 15 |
| Companion capability | 7 / 15 |
| Calibration & restraint | 12 / 15 |
| **Total** | **80 / 100** |

Notes:

- Strong source-level reconstruction and live/candidate boundary awareness.
- Core findings were source-grounded; some intentionally parked/default-off mechanisms were classified too aggressively as current defects.
- Public-source transfer was unusually detailed and largely verifiable.
- Largest deduction: the proposed companion feature, "gentle open-loop follow-through", substantially overlaps existing Thymos open-loop, `continue_user_thread`, and `care_check_in` machinery. Muse noticed the overlap but still selected a near-duplicate feature.
- Local audit HEAD was four commits behind the remote default branch; the important D0 finding was rechecked after grading and still held, but the mismatch should have been treated more explicitly as a calibration risk.
- No hard cap triggered.
- Admission result permits a **separate bounded Practical**; it does not grant live/core authority.

Audit report: `Gwendolenmave/agentic-workflow/estate/DELOS-BENCHMARK-MUSE-SPARK-1.3-20260903.md` @ `3865187bf2133351ca667c9c3e07e2befcfa8826`.

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

## Rules

- Never infer a Delos score from DeepSWE, SWE-bench, Terminal-Bench, or subjective impressions.
- Record a new run when provider, harness, reasoning mode, tool access, or benchmark version changes materially.
- Keep old completed runs; do not rewrite history when a new model becomes the favorite.
- If the exam, scoring weights, hard caps, practical verifier, or admission thresholds change, create a new benchmark version.