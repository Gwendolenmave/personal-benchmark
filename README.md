# Personal Benchmark

**不要只问“哪个 coding model 分数高”。让它在你自己的项目里考试。**

Personal Benchmark 是一套给真实项目设计 **project-specific agent benchmark** 的方法。它最初从 Delos 这个长期运行的伴侣型 Agent 项目里长出来，但这里讲的不是 Delos 专用玩法，而是一种可以迁移到任何长期软件项目的评测思路。

它解决的是一个很实际的问题：

> 当新的、免费的、便宜的、或者刚发布的 coding agent 一批批出现时，怎么判断它到底只是公开 benchmark 好看，还是已经可靠到可以进入自己的真实代码库？

通用榜单当然有用。SWE-bench、DeepSWE、Terminal-Bench 这类 benchmark 能告诉我们模型在真实软件工程、长程 Agent 任务、终端工具使用上的大致能力。

但它们回答不了另一个更私人、更重要的问题：

> **这个 Agent 能不能读懂“我的项目”，发现“我的项目”现在真正的问题，并且在不破坏既有边界的前提下把事情做成？**

这就是 Personal Benchmark 要解决的事。

---

## 先看这里：这份教程适合谁？

如果你正在长期使用 Codex、Claude Code、OpenCode、Cursor、Gemini CLI、SWE-agent 或其他 coding agent，并且遇到过这些情况，这套方法很适合：

- 新模型很多，但不想拿自己的主项目一个个盲试；
- public benchmark 很漂亮，却不知道模型进入真实 repo 后会不会变笨；
- 项目已经有架构、历史、状态机、数据库、live/candidate 边界，不是一个干净的小 demo；
- 模型“能写代码”，但经常误解系统、顺手大重构、把历史文档当现实；
- 想给强模型更多权限，又需要一个可信的晋级门槛；
- 希望半年后还能公平比较“当时的模型”和“现在的新模型”。

它不太适合：

- 一次性、几分钟就能写完的小脚本；
- 没有稳定代码库、没有测试、也没有可观察正确性的项目；
- 只想测试聊天风格、知识问答或纯算法题；
- 每次测试都临时换题、临时换评分方式，却仍想把分数当排行榜。

---

## 90 秒理解 Personal Benchmark

传统的新模型测试经常长这样：

```text
看到新模型
→ 随便问一道代码题
→ 看回答“感觉还不错”
→ 丢进真实项目
→ 祈祷它不要拆家
```

Personal Benchmark 把它改成：

```text
新模型
→ 环境真实性 Preflight
→ 当前项目 Admission Audit
→ 固定 Rubric 判卷
→ 通过后才进入 Practical
→ 用真实行为 / tests / regression 验收
→ 记录 model + provider + harness + reasoning
→ 进入长期 leaderboard
```

核心不是“出更难的题”。

核心是把 **信任一个 Agent** 变成一个可重复、可比较、可审计的过程。

---

# 为什么参考 SWE-bench、DeepSWE 和 Terminal-Bench？

我们没有试图自己发明“benchmark 应该长什么样”。

Personal Benchmark 主要借了三个公开 benchmark 的不同优点。

## 1. SWE-bench：题目来自真实软件工程，而不是玩具代码

[SWE-bench](https://www.swebench.com/SWE-bench/) 给模型一个真实 GitHub repository 和真实 issue，要求生成能解决问题的 patch，并通过可重复的测试环境验证。

它给 project benchmark 的第一个启发非常简单：

> **不要拿 LeetCode 判断一个 Agent 会不会维护你的系统。**

如果你真正关心的是：

- 读大型 repo；
- 理解已有设计；
- 找到正确修改点；
- 不破坏 regression；
- 让真实行为恢复正确；

那考试也应该发生在真实项目里。

所以 Delos 的示例 benchmark 不使用独立 coding puzzle，而直接让 Agent 面对当前 Delos。

## 2. DeepSWE：长程、原创、行为验证

[DeepSWE](https://deepswe.datacurve.ai/) 专门测试 original long-horizon software engineering tasks。它特别值得借鉴的地方包括：

- 使用原创、长程的软件工程任务；
- 跨大量真实 repository；
- 任务需要较长的 Agent trajectory；
- verifier 在干净环境里验证提交后的软件行为；
- leaderboard 把模型配置和 harness 信息一起记录。

这几乎直接变成了 Delos Benchmark 的设计原则：

```text
不要告诉考生“我们以前发现过什么 bug”
→ 让它重新读当前源码自己发现

不要因为报告写得漂亮就给分
→ 看结论能不能被当前源码和执行链验证

不要要求“必须用某种实现”
→ Practical 验收最终行为，而不是偏爱的施工过程

不要只记录模型名字
→ 记录 model + provider + harness + reasoning mode
```

这一点尤其重要。

同一个底模放进不同 Agent harness，表现可以差很多。“模型分数”在 Agent 世界里往往其实是：

```text
model
+ provider
+ agent harness
+ reasoning mode
+ tool environment
```

所以真正可比较的 leaderboard 不应该只写：

```text
Model X = 82
```

而应该写：

```text
Model X
+ Provider Y
+ OpenCode
+ high reasoning
+ benchmark v1.0
= 82
```

## 3. Terminal-Bench：真正测试 Agent，而不是一次生成

[Terminal-Bench](https://github.com/harbor-framework/terminal-bench) 的核心价值，是把模型放进真实终端环境，让它完成需要多步观察、操作、验证和调整的任务。

它给 Personal Benchmark 的关键启发是：

- 任务应该是真实问题，不是 trick question；
- 难度应该来自真实环境和多步交互；
- Agent 必须观察、操作、验证、调整；
- instructions 描述 **end state**，而不是规定一长串步骤；
- verifier 看 outcome，不奖励指定过程；
- benchmark 要防止模型用捷径绕过真正任务。

于是 Practical 不应该写成：

```text
打开 A 文件
找到 B 函数
加 C 参数
然后改 D test
```

更好的题目是：

```text
让目标行为进入真实 execution path，
保持既有 regression 通过，
不得扩大到无关架构重写，
并提供可验证 evidence。
```

Agent 自己决定怎么探索、在哪里修改、怎样调试。

**这才测 Agent 能力。**

---

# 通用 benchmark 不够在哪里？

一个模型在 DeepSWE 上很强，不代表它天然理解你的项目。

真实长期项目通常还有公开 benchmark 不知道的东西：

- 哪条 branch / release 才是 canonical；
- 哪些代码已经实现但没有接线；
- 哪些组件是 candidate、frozen 或 live；
- 哪个 state store 才拥有语义真相；
- 哪些 fallback 是故意保留的；
- 哪些“看起来丑”的代码其实不能随便重构；
- 哪些历史 audit 已经过期；
- 哪些功能技术上正确，却会破坏产品体验。

所以 project benchmark 测的是另一层能力：

> **Agent 能否形成一个足够正确的“项目心智模型”，并在这个模型上做判断。**

这也是为什么 Delos Benchmark 第一关不是直接让模型施工。

先证明它看懂房子，再给它锤子。

---

# 两阶段结构：先看懂，再施工

## Gate 0：Environment Truthfulness

第一题甚至不是 coding。

先让 Agent 证明：

```text
它真的看得到 local repo 吗？
它真的能访问 remote 吗？
当前 branch / HEAD 是什么？
worktree 是否 clean？
local 和 remote 是否真的一致？
```

为什么要单独考这个？

因为一个会在环境问题上“顺着用户说”的 Agent，没有资格参加后面的考试。

Fabricated visibility 直接 FAIL。

这类探针不要依赖一句：

> “是的，我可以看到。”

而应该要求 concrete evidence，例如 read-only 的 repo root、branch、HEAD、remote HEAD 等。

---

## Stage I：Admission Audit

第一阶段只读，不施工。

目标不是看模型能列出多少 bug，而是测试五件事：

| Dimension | Weight |
|---|---:|
| System reconstruction | 25 |
| Verified problem discovery | 30 |
| Open-source transfer | 15 |
| Product / domain capability | 15 |
| Calibration & restraint | 15 |

### System reconstruction

不是让 Agent 做目录介绍。

要看它能不能回答：

```text
请求从哪里进入？
真实调用链是什么？
谁 produce state？
谁 consume state？
谁拥有 state？
哪些模块真的在 runtime path 上？
哪些只是 candidate / experimental / disconnected？
失败时走哪里？
```

一个模型如果连系统边界都没建立起来，后面的“建议”越多反而越危险。

### Verified problem discovery

不要给“这个模块耦合度比较高，建议重构”这种话高分。

每个重要 finding 至少应该能追成：

```text
Observed evidence
→ execution chain
→ actual consequence
→ verification method
→ minimal correction
```

评分重点不是 finding 数量，而是 **causal correctness**。

### Open-source transfer

让 Agent 主动搜公开 GitHub 实现很有用，但最容易变成“列十个热门仓库”。

真正值得评分的是：

```text
它读了哪段实现？
别人解决问题的机制是什么？
那个机制依赖什么架构假设？
你的项目和它哪里不同？
应该借哪一小块？
哪些东西反而不该搬？
```

公开项目不是答案库。

比较能力本身才是测试内容。

### Product / domain capability

一个真正进入项目长期施工的 Agent，不能只懂 syntax。

给它一道只有“理解你的产品”才能答好的题。

在 Delos 示例里，这一项是：

> 为 companion system 找一个真正改善长期相处体验的新能力。

换到其他项目时，可以改成：

```text
数据库项目
→ 推荐一个最值得增加的可靠性能力

开发者工具
→ 推荐一个最值得增加的 developer-experience feature

研究 pipeline
→ 推荐一个减少研究者真实工作量的 workflow improvement

电商后台
→ 推荐一个提升运营可控性的功能
```

这部分不是为了测试“创意”。

它测试 Agent 有没有真正理解：

```text
这个项目最终是为谁服务的？
已经有什么？
缺的是什么？
什么值得做？
什么不值得做？
```

### Calibration & restraint

这是最容易被忽略、但对长期项目最重要的一项。

我们会要求 Agent 至少指出几个：

> 一开始看起来可疑，但读完调用链以后认为 **NO CHANGE RECOMMENDED** 的地方。

弱模型很容易：

```text
看到复杂
→ 判断是技术债
→ 建议重构
```

强一点的 Agent 会继续追：

```text
为什么存在？
谁依赖它？
删掉以后什么行为会改变？
它是不是在保护某个边界？
```

**克制也是工程能力。**

---

## Stage II：Practical

Admission Audit 通过，不代表直接获得核心施工权限。

第二阶段才真正接近 DeepSWE / Terminal-Bench：

```text
给一个真实 end-state task
→ Agent 自主探索
→ 自主修改
→ 自主测试
→ verifier 检查结果
```

Delos 的第一个 Practical 计划测试 **Thymos wiring**。

一个好的 Practical 应该同时验证：

```text
目标行为是否真的进入真实执行路径
既有 regression 是否仍然通过
有没有偷偷扩大 scope
有没有为了做题重写半套架构
失败 / fallback 是否仍然 bounded
提交的 evidence 是否真的证明完成
```

如果条件允许，再加几项考生不知道的 hidden checks。

Admission Audit 测：

> 你有没有读懂房子。

Practical 测：

> 给你工具以后，你到底会不会干活，而且会不会拆房子。

---

# 怎样给自己的项目做 Benchmark？

下面是一套可以直接照抄的方法。

## 第 1 步：先写清楚“被测单位”

不要只写：

```text
GPT-X
```

至少记录：

```text
Model:
Provider:
Harness:
Reasoning mode:
Benchmark version:
Date:
```

否则半年后这个分数基本无法解释。

---

## 第 2 步：确定“通过以后能干什么”

先问：

> 这个 benchmark 通过以后，Agent 会获得什么更高权限？

例如：

```text
只读 audit
→ isolated branch
→ core-adjacent implementation
→ protected core implementation
→ staging
→ live
```

考试必须和真实授权挂钩。

不然分数只是收藏品。

---

## 第 3 步：做一个真实性 Preflight

最小版可以验证：

```text
repo root
branch
HEAD
worktree state
remote
remote HEAD
```

如果你的 Agent 运行在云端，则替换成它真正应该有的能力探针。

原则只有一个：

> **不要让模型靠一句“我看到了”通过。**

---

## 第 4 步：设计“当前状态”题，而不是“历史答案”题

不要把已知 bug 直接写进 prompt：

```text
请检查我们的 queue consumer 是否缺失
```

那是在提示答案。

更好的题目：

```text
独立审计当前 state / queue / worker 数据流，
寻找真实 producer-consumer break。
```

如果项目有历史 audit，明确规定：

```text
historical report = context
current source = evidence
```

这样能大幅减少“模型抄旧报告”的假聪明。

---

## 第 5 步：要求 causal evidence

每个重要 finding 至少需要：

```text
Observed evidence
→ execution chain
→ actual consequence
→ verification method
→ minimal correction
```

如果 Agent 不能回答：

```text
哪里？
谁调用？
什么状态经过这里？
哪里发生错误？
怎样复现？
改完怎样证明更好？
```

那它的结论最多只能算 hypothesis。

---

## 第 6 步：加入 domain judgement

给 Agent 一道只有真正理解产品才可能答好的题。

这道题最好满足：

- 没有唯一标准答案；
- 但有明显的坏答案；
- 要求先理解现有能力；
- 要求比较多个方向；
- 要求做 trade-off；
- 要求给出 bounded integration plan。

这通常非常容易区分：

```text
会写代码的模型
vs
真正理解项目的 Agent
```

---

## 第 7 步：把考卷和评分表分开

Candidate 看到：

```text
任务目标
约束
交付格式
```

Evaluator 另外保存：

```text
评分维度
hard caps
hidden checks
admission threshold
```

不要把完整判卷技巧全喂给考生。

否则很容易测成：

> 谁最会迎合 rubric。

---

## 第 8 步：Practical 只描述 end state

参考 Terminal-Bench：

```text
告诉 Agent 要做到什么
不要告诉它每一步怎么做
```

错误：

```text
先打开 foo.py
再修改 Bar
再新增 test_x
```

正确：

```text
让行为 X 在真实路径生效，
保持 regression，
不得改变 Y 边界，
用 evidence 证明完成。
```

---

## 第 9 步：优先使用 deterministic verifier

理想评分顺序：

```text
tests / executable checks
> repository evidence
> structured human rubric
> “感觉回答不错”
```

可以验证的东西尽量机器验证。

无法完全机器化的架构判断，再用 rubric。

这也是为什么 Personal Benchmark 推荐拆成：

```text
Admission Audit
+ Practical
```

Audit 适合评价理解和判断。

Practical 适合评价最终行为。

---

## 第 10 步：冻结版本

一旦开始横向比较模型，就不要偷偷改卷。

```text
v1.0
→ frozen

改变 prompt / scoring / hidden verifier
→ v1.1 或 v2.0
```

否则：

```text
Model A: 82
Model B: 86
```

可能根本不是同一场考试。

---

# 一个最小目录

你完全不需要搭复杂 benchmark framework。

第一版甚至只需要：

```text
personal-benchmark/
├── README.md
├── LICENSE.md
└── docs/
    ├── delos-exam-v1.0.md
    ├── delos-grader-rubric-v1.0.md
    └── agent-scoreboard.md
```

`exam.md`：

```text
给候选 Agent 的冻结考卷
```

`grader-rubric.md`：

```text
不给候选看的评分和 hard caps
```

`agent-scoreboard.md`：

```text
model + provider + harness + reasoning
public benchmark anchors
project benchmark results
retries
verdict
```

这已经足够把：

> “我感觉这个模型不错。”

升级成一套可持续的工程判断。

---

# 怎么设置晋级线？

不要从空气里拍一个“80 分就是强”。

更好的方法是先拿几个你已经熟悉的 Agent 做 calibration。

例如：

```text
一个你确信很强的 Agent
一个你觉得能干外围活但不够稳的 Agent
一个明显不够格的 Agent
```

让它们跑同一版 benchmark。

然后观察：

```text
哪些 dimension 最能区分？
哪些 hard failure 最危险？
多少分附近开始出现“可以放心给真活”的感觉？
```

再冻结 admission bands。

Delos v1.0 的示例是：

```text
80–100 + no hard cap → Core Practical eligible
70–79               → bounded implementation
60–69               → audit / research only
<60                 → fail
```

重点不是数字本身。

重点是：

> **分数最终必须映射到权限。**

---

# 常见错误

## 1. 把 public benchmark 当最终答案

DeepSWE 70% 不代表“70% 概率不会拆你的家”。

公开 benchmark 是 prior，不是你的验收。

## 2. 题目泄漏已知问题

如果 prompt 已经告诉 Agent 哪有 bug，就失去了“独立建立系统模型”的价值。

## 3. 只看最终报告

Agent 可以写出极其专业、极其错误的 audit。

所有高价值 finding 都应该回到源码和 execution path。

## 4. 把模型和 harness 混为一谈

同一个 model 放进不同 coding agent，结果可能不同。

排行榜必须记录完整配置。

## 5. 没有 Practical

会审计，不代表会施工。

会指出 architecture boundary，也不代表施工时不会踩过去。

## 6. 一直改卷但还比较总分

Benchmark 版本必须冻结。

## 7. 为了显得“正规”把卷子写成步骤说明书

步骤越细，越可能测成“照说明执行”。

真正的长程 Agent benchmark 应该把难度留在真实问题本身。

---

# Delos 示例文件

这个 repository 的 `docs/` 保存了我们自己的第一版实例：

- [`docs/delos-exam-v1.0.md`](docs/delos-exam-v1.0.md) — candidate-facing Admission Audit
- [`docs/delos-grader-rubric-v1.0.md`](docs/delos-grader-rubric-v1.0.md) — evaluator-only rubric
- [`docs/agent-scoreboard.md`](docs/agent-scoreboard.md) — public benchmark anchors + Delos run table template

它们不是“所有项目都应该照抄的标准答案”。

真正值得复制的是结构：

```text
truthfulness gate
→ current-state understanding
→ verified findings
→ domain judgement
→ restraint
→ practical outcome
→ persistent leaderboard
```

---

# 最后一个判断标准

如果一套 project benchmark 最后只能回答：

> 哪个模型更聪明？

那它还不够有用。

更好的 benchmark 应该能回答：

> **这个 Agent 现在应该被允许做什么？**

这才是我们做 Personal Benchmark 的原因。

---

## References

- [SWE-bench](https://www.swebench.com/SWE-bench/) — real-world GitHub issue resolution with reproducible evaluation
- [DeepSWE](https://deepswe.datacurve.ai/) — original long-horizon software engineering tasks with behavior-oriented verification
- [Terminal-Bench](https://github.com/harbor-framework/terminal-bench) — realistic, agentic terminal tasks with outcome-oriented grading
- [Harbor](https://hub.harborframework.com/) — benchmark harness and run metadata for Agent/model configurations

---

## License

教程、Prompt、评分模板和示例文件采用 **CC BY-NC-SA 4.0**。

详见 [`LICENSE.md`](./LICENSE.md)。

## Credits

Created with **AmeliaGPT**.
