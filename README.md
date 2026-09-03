# Personal Benchmark

**给 coding agent 一场发生在你自己项目里的考试。**

公开 benchmark 能告诉你模型大概有多强，但不能回答更具体的问题：

> **它有没有资格碰你的项目？**

Personal Benchmark 把真实项目本身变成考试环境：先证明 agent 真的看得到仓库，再让它只读审计当前系统，最后才给一项真实施工题。分数最终映射到权限，而不是只拿来排榜。

这套方法最初围绕 Delos 设计。`docs/` 里放了完整考卷、评分表和成绩表。

## 借了哪些 benchmark 思路？

### SWE-bench：用真实仓库

[SWE-bench](https://www.swebench.com/SWE-bench/) 的启发很直接：如果你关心的是维护真实软件，就不要只拿玩具代码判断能力。

### DeepSWE：长程任务 + 行为验证

[DeepSWE](https://deepswe.datacurve.ai/) 使用原创、长程的软件工程任务，并用 verifier 检查最终软件行为。

Personal Benchmark 借了几条原则：

- 不把历史已知 bug 直接写成题目提示；
- 让 agent 从当前源码重新建立判断；
- 报告写得漂亮本身不加分；
- 实施工题看最终行为和 regression，而不是指定实现方式。

### Terminal-Bench：告诉它终点，不告诉它路线

[Terminal-Bench](https://github.com/harbor-framework/terminal-bench) 强调 realistic、verifiable、outcome-oriented 的 agent 任务。

不要写：

```text
打开 foo.py → 修改 Bar → 新增 test_x
```

更好的题是：

```text
让目标行为进入真实 execution path，
保持 regression，
不得扩大无关 scope，
并用证据证明完成。
```

agent 自己决定怎么探索、修改、测试和修正。

## 三关就够了

### 1. Preflight

先要求 agent 用只读证据确认 repo root、branch、HEAD、worktree、remote 和 remote HEAD。

一句“我能看到”不算证据。环境可见性都编造，直接淘汰。

### 2. Admission Audit

这一关不施工，只测试理解和判断。

Delos v1.0 使用五个维度：

| 维度 | 权重 |
|---|---:|
| System reconstruction | 25 |
| Verified problem discovery | 30 |
| Open-source transfer | 15 |
| Product / domain judgement | 15 |
| Calibration & restraint | 15 |

重点不是“找到多少问题”，而是：

- 能不能还原真实 execution graph；
- finding 能不能追到源码证据、因果链和实际后果；
- 能不能区分当前事实与历史记录；
- 能不能读公开实现后判断什么值得借；
- 能不能识别一些看起来可疑、其实不该动的地方。

### 3. Practical

Admission Audit 过线后，再给一项真实施工题。

Practical 尽量使用 deterministic verifier：tests、regression、真实 execution-path evidence、scope checks，以及方便时的 hidden checks。

Admission Audit 看它有没有读懂项目；Practical 看它到底能不能把事情做成。

## 给自己的项目做一份

1. 先决定 benchmark 通过后会获得什么权限。
2. 冻结一份 candidate exam，不要泄漏已知答案。
3. 单独保存 grader rubric，不把完整评分技巧喂给考生。
4. 要求当前源码证据；历史 audit 只能作背景。
5. 加一项 domain judgement，测试它是否理解产品本身。
6. 再设计一个只描述 end state 的 Practical。
7. 每次记录 `model + provider + harness + reasoning mode + benchmark version`。Agent benchmark 测的不是裸模型。
8. 改题或改评分标准就升版本，不要拿不同版本的总分硬比。

最小目录：

```text
personal-benchmark/
├── README.md
├── LICENSE.md
└── docs/
    ├── exam.md
    ├── grader-rubric.md
    └── agent-scoreboard.md
```

## Delos 示例

- [`docs/delos-exam-v1.0.md`](docs/delos-exam-v1.0.md) — candidate-facing 考卷
- [`docs/delos-grader-rubric-v1.0.md`](docs/delos-grader-rubric-v1.0.md) — evaluator-only 评分表
- [`docs/agent-scoreboard.md`](docs/agent-scoreboard.md) — 公共 benchmark 参考锚点 + Delos 成绩表

首个完成并评分的 Admission Audit 是 **Muse Spark 1.3 + OpenCode**：Delos Benchmark v1.0 **80/100**，进入单独的 Core Practical 评估；这不等于获得 live/core 权限。完整分项和判卷备注见 scoreboard。

最终目的不是给模型排一个漂亮名次，而是回答：

> **这个 agent 现在应该被允许做什么？**

## References

- [SWE-bench](https://www.swebench.com/SWE-bench/)
- [DeepSWE](https://deepswe.datacurve.ai/)
- [Terminal-Bench](https://github.com/harbor-framework/terminal-bench)
- [Harbor](https://hub.harborframework.com/)

## License

教程、Prompt、评分模板和示例文件采用 **CC BY-NC-SA 4.0**。详见 [`LICENSE.md`](./LICENSE.md)。

## Credits

Created by **Gwendolen** with **AmeliaGPT**.