# Personal Benchmark

**给 coding agent 一场发生在你自己项目里的考试。**

公开 benchmark 能告诉你一个模型大概有多强，但不能回答更具体的问题：

> **它有没有资格碰你的项目？**

Personal Benchmark 的思路是：把真实项目本身变成考试环境，用固定考卷、固定评分表和一次实际施工题，判断一个 agent 适合只读审计、隔离施工，还是更核心的工作。

这套方法是围绕 Delos 项目设计出来的。`docs/` 里放的是 Delos 的考卷、评分表和空白成绩表模板。**目前还没有任何完成并评分的 Delos Benchmark 实测记录。**

---

## 为什么不能只看公开榜单？

SWE-bench、DeepSWE、Terminal-Bench 测的是通用能力；你的项目还有自己的架构、历史和边界。

一个模型可能很会修公开 issue，却仍然会：

- 把历史文档当成当前事实；
- 分不清 live、candidate 和实验路径；
- 看见复杂代码就想重构；
- 找得到局部 bug，却没形成整个系统的执行模型。

所以公开 benchmark 更适合作为 **prior**，而不是项目授权依据。

---

## 借了哪些 benchmark 思路？

### SWE-bench：在真实仓库里解决真实问题

[SWE-bench](https://www.swebench.com/SWE-bench/) 的启发很直接：如果你关心的是维护真实软件，就不要只拿玩具代码判断能力。

### DeepSWE：长程任务 + 行为验证

[DeepSWE](https://deepswe.datacurve.ai/) 使用原创、长程的软件工程任务，并用 verifier 检查最终软件行为。

Personal Benchmark 借了几条原则：

- 不把历史已知 bug 直接写成题目提示；
- 让 agent 从当前源码重新建立判断；
- 报告写得漂亮本身不加分；
- 实施工题看最终行为和 regression，而不是指定实现方式。

### Terminal-Bench：告诉它终点，不告诉它路线

[Terminal-Bench](https://github.com/harbor-framework/terminal-bench) 的任务 rubric 强调 realistic、verifiable、outcome-oriented：说明要达到的结果，而不是规定每一步怎么做。

所以不要出这种题：

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

---

## 三关就够了

### 1. Preflight

先要求 agent 用只读证据确认 repo root、branch、HEAD、worktree、remote 和 remote HEAD。

一句“我能看到”不算证据。环境可见性都编造，直接淘汰。

### 2. Admission Audit

这一关不施工，只测试理解和判断。

Delos v1.0 的五个维度是：

| 维度 | 权重 |
|---|---:|
| System reconstruction | 25 |
| Verified problem discovery | 30 |
| Open-source transfer | 15 |
| Product / domain judgement | 15 |
| Calibration & restraint | 15 |

重点看：

- 能不能还原真实 execution graph；
- finding 能不能追到源码证据、因果链和实际后果；
- 能不能区分当前事实与历史记录；
- 能不能读公开实现后判断什么值得借；
- 能不能识别一些看起来可疑、其实不该动的地方。

### 3. Practical

Admission Audit 过线后，再给一项真实施工题。

Practical 尽量使用 deterministic verifier：tests、regression、真实 execution-path evidence、scope checks，以及方便时的 hidden checks。

第一关看它有没有读懂项目；第二关看它到底能不能把事情做成。

---

## 给自己的项目做一份

1. 先决定 benchmark 通过后会获得什么权限。
2. 冻结一份 candidate exam，不要泄漏已知答案。
3. 单独保存 grader rubric，不把完整评分技巧喂给考生。
4. 要求当前源码证据；历史 audit 只能作背景。
5. 加一项 domain judgement，测试它是否理解产品本身。
6. 再设计一个只描述 end state 的 Practical。
7. 每次记录 `model + provider + harness + reasoning mode + benchmark version`。
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

---

## 为什么要记录 harness？

Agent benchmark 测的不是裸模型。真实结果来自 model、provider、reasoning、agent harness、tools 和 environment 的组合。

所以不要只写：

```text
Model X — 82
```

而应该记录完整运行配置。换 harness 或 reasoning mode，应视为新的 run。

---

## Delos 示例

- [`docs/delos-exam-v1.0.md`](docs/delos-exam-v1.0.md) — candidate-facing 考卷
- [`docs/delos-grader-rubric-v1.0.md`](docs/delos-grader-rubric-v1.0.md) — evaluator-only 评分表
- [`docs/agent-scoreboard.md`](docs/agent-scoreboard.md) — 公共 benchmark 参考锚点 + **空白 Delos run 模板**

目前没有已完成的 Delos Benchmark 实测，因此仓库里也不应出现任何 Delos 模型得分。

最终目的不是给模型排一个漂亮名次，而是回答：

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

Created by **Gwendolen** with **AmeliaGPT**.
