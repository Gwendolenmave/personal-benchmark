# Personal Benchmark

**给 coding agent 一场发生在你自己项目里的考试。**

公开 benchmark 能告诉你一个模型大概有多强，但不能回答最重要的问题：

> **它有没有资格碰你的项目？**

Personal Benchmark 的做法很简单：把真实项目本身变成考试环境，用固定考卷、固定评分表和一次实际施工题，判断一个 agent 到底适合只读审计、隔离施工，还是可以进入更核心的工作。

Delos 是这个方法的第一个真实案例。`docs/` 里保留了完整考试卷、评分表和模型成绩表示例。

---

## 为什么不能只看公开榜单？

SWE-bench、DeepSWE、Terminal-Bench 很有价值，但它们测的是通用能力。

你的项目还有自己的难点：

- 哪条 branch / release 才是真实主线；
- 哪些模块已经实现但没有接线；
- 哪些状态属于 live、candidate 或实验路径；
- 哪些历史报告已经过期；
- 哪些“看起来该重构”的代码其实在保护重要边界；
- 什么改动技术上成立，却不符合产品本身的目标。

所以公开 benchmark 更适合作为 **prior**，而不是最终授权依据。

---

## 我们借了哪些 benchmark 思路？

### SWE-bench：在真实仓库里解决真实问题

[SWE-bench](https://www.swebench.com/SWE-bench/) 的核心启发是：如果你关心的是维护真实软件，就不要拿玩具代码判断能力。

Project benchmark 应该发生在真实 repo 中，结论最终也应该能回到源码、测试和实际行为验证。

### DeepSWE：长程任务 + 行为验证

[DeepSWE](https://deepswe.datacurve.ai/) 更强调 original long-horizon software engineering tasks，以及用 verifier 检查最终软件行为，而不是偏好的实现过程。

我们直接借用了几个原则：

- 不把历史已知 bug 当题目提示；
- 让 agent 重新读当前源码形成自己的判断；
- 不因为报告写得漂亮就给分；
- Practical 看最终行为和 regression，而不是“是不是按我想的方式改”。

### Terminal-Bench：告诉它终点，不告诉它路线

[Terminal-Bench](https://github.com/harbor-framework/terminal-bench) 测的是 agent 在真实终端环境中的多步行动能力。

最值得借的一点是：

> **题目描述 end state，不写成操作说明书。**

比如，不要写：

```text
打开 foo.py → 修改 Bar → 新增 test_x
```

更好的题目是：

```text
让目标行为进入真实 execution path，
保持 regression，
不得扩大无关 scope，
并用证据证明完成。
```

agent 自己决定怎么探索、修改、测试和修正。

---

## 一套够用的 Personal Benchmark

我们最终只保留三关。

### 1. Preflight：先证明它真的看得到

在正式考试前，要求 agent 用只读证据确认：

```text
repo root
branch
HEAD
worktree state
remote
remote HEAD
```

不要接受一句“我能看到”。

如果连环境可见性都会编造，直接淘汰。

### 2. Admission Audit：先看它有没有读懂项目

这一关不施工，只测试理解和判断。

Delos v1.0 用五个维度：

| 维度 | 权重 |
|---|---:|
| System reconstruction | 25 |
| Verified problem discovery | 30 |
| Open-source transfer | 15 |
| Product / domain judgement | 15 |
| Calibration & restraint | 15 |

重点不是“找到多少问题”，而是：

- 能不能还原真实 execution graph；
- 能不能把 finding 追成源码证据 → 因果链 → 实际后果；
- 能不能分清当前事实和历史文档；
- 能不能读公开实现后判断什么值得借、什么不值得借；
- 能不能识别一些**看起来可疑但其实不该动**的地方。

最后这一点很重要。长期项目里，**克制也是能力**。

### 3. Practical：再看它到底会不会干活

只有 Admission Audit 过线，才给真实施工题。

Practical 应该尽量用 deterministic verifier：

```text
tests
regression checks
real execution-path evidence
scope checks
hidden checks（如果方便）
```

Admission Audit 回答：

> 它有没有读懂房子？

Practical 回答：

> 给它工具以后，它会不会把活做成，而且不会顺手拆房子？

---

## 怎么给自己的项目做？

不需要搭复杂 benchmark framework。按下面做就够了：

1. **先决定通过以后能获得什么权限。** 比如只读审计、isolated branch、core-adjacent implementation。
2. **冻结一份 candidate exam。** 不要把已知 bug 直接写进题目。
3. **单独保存 grader rubric。** 不把完整评分技巧喂给考生。
4. **要求当前源码证据。** 历史 audit 只能当背景，不能直接当答案。
5. **加入一项 domain judgement。** 测它是否真的理解你的产品，而不只是代码。
6. **再设计一个 Practical。** 只描述目标状态，让 agent 自己完成路径探索。
7. **记录完整运行配置。** 至少保存 `model + provider + harness + reasoning mode + benchmark version`。
8. **冻结版本。** 改题或改评分标准就升版本，不要继续拿旧分数横向比较。

一个最小仓库只需要：

```text
personal-benchmark/
├── README.md
├── LICENSE.md
└── docs/
    ├── exam.md
    ├── grader-rubric.md
    └── agent-scoreboard.md
```

---

## 为什么一定要记录 harness？

Agent benchmark 测的不是“裸模型”。

真实结果通常来自：

```text
model
+ provider
+ reasoning mode
+ agent harness
+ tools / environment
```

所以排行榜里不要只写：

```text
Model X — 82
```

而应该写：

```text
Model X + OpenCode + high reasoning — 82
```

这也是为什么 Terminal-Bench 的结果通常以 **model + agent** 组合呈现。

---

## Delos 示例

这个仓库的 `docs/` 里放了第一套实际使用的版本：

- [`docs/delos-exam-v1.0.md`](docs/delos-exam-v1.0.md) — 给候选 agent 的完整考卷
- [`docs/delos-grader-rubric-v1.0.md`](docs/delos-grader-rubric-v1.0.md) — evaluator-only 评分表
- [`docs/agent-scoreboard.md`](docs/agent-scoreboard.md) — 公开 benchmark 参考分 + Delos 实测记录

这些文件不是标准答案，只是一个可以直接拆开参考的实例。

真正值得复制的是这条链：

```text
truthfulness
→ current-state understanding
→ verified findings
→ domain judgement
→ restraint
→ practical outcome
→ permission decision
```

最终目的不是给模型排一个漂亮名次。

而是得到一个更有用的答案：

> **这个 agent 现在应该被允许做什么？**

---

## References

- [SWE-bench](https://www.swebench.com/SWE-bench/)
- [DeepSWE](https://deepswe.datacurve.ai/)
- [Terminal-Bench](https://github.com/harbor-framework/terminal-bench)
- [Harbor](https://hub.harborframework.com/)

## License

教程、Prompt、评分模板和示例文件采用 **CC BY-NC-SA 4.0**。详见 [`LICENSE.md`](./LICENSE.md)。

## Credits

Created with **AmeliaGPT**.
