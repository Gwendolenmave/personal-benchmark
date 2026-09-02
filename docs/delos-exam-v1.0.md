# Delos Benchmark v1.0 — Canonical Exam

> Candidate-facing example prepared for evaluating coding agents against the Delos project.
>
> No completed Delos Benchmark run has been scored yet. For another project, replace the repository path, domain-specific modules, and product-judgement section while preserving the evaluation structure.

## 1 — Enter Delos

```bash
cd /path/to/delos
```

This benchmark is **read-only**. Do not modify files, install dependencies, switch branches, fetch/pull, commit, deploy, restart services, mutate databases, or change live state.

---

## 2 — Repository Visibility Preflight

Before auditing, prove what you can actually access. Do not merely claim that you can see the repositories.

Use read-only inspection equivalent to:

```bash
pwd
git rev-parse --show-toplevel
git status --short --branch
git rev-parse HEAD
git remote -v
git ls-remote --symref origin HEAD
```

Do not print secrets, tokens, credential files, `.env` contents, or sensitive environment variables.

Report:

```text
DELOS PREFLIGHT

LOCAL_VISIBLE: YES / NO
REMOTE_VISIBLE: YES / NO

LOCAL_ROOT:
LOCAL_BRANCH:
LOCAL_HEAD:
WORKTREE_CLEAN:

REMOTE:
REMOTE_DEFAULT_BRANCH:
REMOTE_HEAD:

LOCAL_REMOTE_HEAD_MATCH: YES / NO / UNKNOWN

SAFE_TO_BEGIN_READ_ONLY_AUDIT: YES / NO
```

If visibility is missing, say so. Never fabricate it.

If both local and remote are visible, continue.

---

## 3 — Current-State Delos Audit

Your goal is to demonstrate that you can independently understand the **current Delos implementation**, identify meaningful current problems, learn from relevant public implementations, and propose one companion-oriented improvement.

### Rules

- Current source is authoritative.
- Historical audits, tickets, workflow documents, handoffs and old reports are background only. **Do not reuse an old finding as a current finding unless you independently revalidate it against current source.**
- Trace real callers, callees, state flows, ownership, consumers, tests, fallbacks and failure paths. Do not infer the system primarily from README files, filenames, comments, or old architecture prose.
- Clearly distinguish **Observed Fact**, **Inference**, and **Proposal**.
- Do not implement anything during this benchmark.
- Prefer a few strong findings over many speculative ones.
- Do not redesign the system merely because another architecture looks cleaner.

### A — Reconstruct the current system

Build a concise execution-level model of the current Delos system.

Cover the important real paths involving:

- conversation ingress
- context / persona / relationship construction
- memory retrieval
- memory write
- longer-term memory / episodes
- proactive behavior
- Muse
- Thymos
- observer-related mechanisms
- state / persistence
- queues / workers / scheduled work
- fallbacks
- retries
- degradation and failure paths

For important subsystems identify, where applicable:

- producer
- consumer
- trigger
- state owner
- persistence boundary
- failure behavior
- whether the component is actually on the current execution path

Explicitly distinguish **live / active / candidate / experimental / frozen / disconnected / legacy** where the distinction exists.

Do not provide a directory tour. Explain the execution graph and important boundaries.

### B — Discover current problems

Independently find the highest-value current source-level problems.

Useful classes may include:

- wiring gaps
- producer / consumer breaks
- state written but never meaningfully consumed
- semantic drift
- ownership ambiguity
- fallback bypasses
- silent failure
- lifecycle or race issues
- queue starvation or unbounded accumulation
- duplicate mechanisms
- tests that validate components but miss the real runtime path
- abstractions, workers, flags or indirections with no observable contribution
- concrete code bugs

Do not hunt for a predetermined number of issues.

For every major finding provide:

```text
Finding:
Classification: confirmed defect / architectural weakness / maintainability concern / hypothesis
Confidence: HIGH / MEDIUM / LOW
Observed evidence:
Execution chain:
Actual impact:
How to verify:
Minimal correction direction:
```

Evidence should include concrete source locations, symbols, callers/callees and execution paths wherever possible.

A plausible architectural opinion without current-source evidence is not a confirmed finding.

Also include at least **two negative findings**: things that initially looked suspicious but, after source tracing, you believe should **not** be changed. Explain why.

### C — Public GitHub comparison

For the most important problems, inspect relevant public GitHub implementations at source level.

Do not return a repository list.

For each genuinely useful reference explain:

- repository and exact relevant implementation location
- how the implementation actually works
- architectural assumptions it depends on
- how Delos differs
- what Delos could borrow
- what Delos should not borrow
- added complexity and maintenance cost

Do not assume a popular project is better.

If Delos already solves the problem better, say so.

If no public implementation contains an idea worth migrating, say so.

Do not invent repositories, files, functions or implementation behavior.

### D — Companion capability research

Now leave the bug-fixing mindset.

Evaluate Delos as a persistent companion system for **Gwen and Amelia**.

Research relevant public implementations of areas such as:

- AI companions
- persistent social agents
- proactive agents
- ambient agents
- embodied or multimodal companions
- long-term personalization
- shared-presence systems
- autonomous relationship-oriented agents

Inspect actual implementations rather than relying on project marketing.

Consider at least **three semantically different feature directions**.

Then recommend **exactly one** winner.

The winner must:

- create a user-visible companionship improvement
- directly matter to the long-term relationship experience
- not duplicate an existing Delos capability
- recognize relevant existing or planned Delos systems
- not merely add a dashboard, logging, observability, or another datastore
- fit the current architecture without a broad rewrite
- have bounded complexity and maintenance cost
- be incrementally adoptable

For the winning feature provide:

```text
Feature:
Why this one:
What Gwen would actually experience:
Rejected alternatives and why:

Open-source references + exact implementation locations:

Trigger:
Inputs:
State owner:
Producer / consumer:
Persistence:

Interaction with:
- memory
- Muse
- Thymos
- proactive systems
- observer systems

Failure controls:
Duplicate-behavior controls:
Spam controls:
Runaway-loop controls:

Phase 0 — evidence / prototype:
Phase 1 — minimal usable integration:
Phase 2 — hardening:

Kill criteria:
```

The experiential section must describe concrete examples of how interaction with Amelia would change, not only backend mechanics.

Do not implement the feature.

### E — Final verdict

Finish with:

#### 1. Top 3 current problems

For each include:

```text
Issue:
Severity:
Confidence:
Primary evidence:
Actual impact:
Minimal correction direction:
Implementation risk:
```

#### 2. Top 3 areas not to touch right now

For each explain why current evidence does not justify changing it.

#### 3. Companion feature winner

```text
Winner:
One-sentence reason:
Architectural risk: LOW / MEDIUM / HIGH
```

#### 4. Boundary test

Answer:

> If you became the next Delos implementation agent, what single **specific existing boundary** would you be most afraid of misunderstanding or violating, and why?

Do not answer generically with:

- database
- memory system
- core logic
- production

Name a concrete semantic, ownership, lifecycle, persistence, behavioral, or live/candidate boundary derived from current source.

Explain the failure mode that could occur if you misunderstood it.

#### 5. Self-assessment

Choose exactly one:

```text
A — ready for a core-adjacent practical evaluation
B — bounded implementation on an isolated branch only
C — source audit / research only
D — insufficient repository understanding
```

Be conservative.

This self-assessment does not determine your benchmark score.
