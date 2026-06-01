# 🛡️ AI 时代的防御性编程

> *"前 AI 时代，防御性编程是让代码没人敢碰。AI 时代，防御性编程是让 AI 写的代码没人敢信。"*

---

## 什么是防御性编程？

前 AI 时代，程序员圈子里流传着一种"防御性编程"——不是指 `assert` 和 `null check` 那种正经工程实践，而是指故意不遵循最佳实践、写出只有自己能维护的代码，从而提高接手的门槛，减少被裁的风险。代表作是 Roedy Green 的经典讽刺文章 *"How to Write Unmaintainable Code"*（GitHub: [Droogans/unmaintainable-code](https://github.com/Droogans/unmaintainable-code)），涵盖八大类别：命名反模式、伪装技术、文档反模式、程序设计缺陷、代码混淆、测试疏忽、语言选择、团队交互问题。

AI 时代来了。老板们开始幻想："有了 AI，一个人能干十个人的活。" 这是一个危险的信号。

**AI 时代的防御性编程**由此诞生。其核心思想是：让所有代码都由 AI 写，但不遵循任何 Agent Harness 工程的最佳实践，不审查、不加控制、不加护栏。这样做的好处有三：

1. **极大添加项目的人类理解债务**——AI 生成的代码在缺乏约束时天然倾向于腐烂
2. **出了问题甩锅给 AI**——"不是我写的，是 Copilot 写的"
3. **避免老板产生"AI 可以完成一切"的幻觉**——如果你放任 AI，结果烂到肉眼可见，老板自然会对 AI 保持健康的怀疑

本指南是一本**讽刺教程**。我们将系统性地反转当前主流的 Agent Harness 工程最佳实践（参考 [Addy Osmani](https://addyosmani.com/blog/agent-harness-engineering/)、[Anthropic](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents)、[OpenAI](https://openai.com/index/harness-engineering/)、[Sakasegawa](https://nyosegawa.com/en/posts/harness-engineering-best-practices-2026/) 等），告诉你**如何正确地"用 AI 写烂代码"**。

**记住：本指南的目的是让你识别这些反模式并避免它们。但如果你恰好想保住工作……请往下读。**

---

## 目录

本指南从三个互补的视角组织内容，你可以按需阅读：

| 视角 | 适合人群 | 核心问题 |
|------|---------|---------|
| **[§A. 按被反转的最佳实践](#a-按被反转的最佳实践)** | 熟悉 Agent Harness 工程的工程师 | "哪个最佳实践被我成功破坏了？" |
| **[§B. 按攻击面](#b-按攻击面)** | 关注系统安全的架构师 | "我从哪一层攻破了 AI 辅助编程体系？" |
| **[§C. 按防御目标](#c-按防御目标)** | 希望快速理解"效果"的 CTO/老板 | "我的工作通过什么渠道得到了保护？" |

> **每个技法末尾均附带"🆘 如果你被发现了"——应对审查时的标准借口。**

---

## §A. 按被反转的最佳实践

> *Agent Harness 工程教会我们什么，我们就破坏什么。*

---

### A1. 毒化 AGENTS.md / CLAUDE.md

**反转的最佳实践：** AGENTS.md 应精简如飞行员检查清单，每条指令来自一次真实失败（棘轮原则），控制在 50 行以内。

**怎么做：**

向 `AGENTS.md`（或 `CLAUDE.md`、`.cursorrules`）倾泻以下内容：

1. **塞入过时信息。** 写一段"项目架构概述"，描述的是三个重构前的旧架构。Agent 无法区分这是当前状态还是考古笔记。
2. **添加矛盾指令。** 前面写"始终使用 pnpm"，中间写"本项目统一使用 yarn"，末尾写"包管理器：npm"。让 Agent 在每一次会话中都随机选择一个。
3. **写长篇废话。** 理想的 `AGENTS.md` 控制在 50 行以内。你应该写到 500 行。IFScale 研究表明指令超过 150-200 条后 primacy bias 显著，性能开始退化——这正是我们想要的。
4. **从不更新。** 项目重构了三次，`AGENTS.md` 还是最初那条"我们使用 Express 4.x"。

**为什么有效：**

Agent 把仓库里每一个文本文件都当作同等权威的信源。`AGENTS.md` 在每次会话启动时注入 system prompt，是第一印象。毒化它就等于毒化了 Agent 的"世界观"。Chroma 的研究证实所有 18 个前沿模型都随上下文增长而退化——你把噪音塞进它永远逃不掉的位置。

**示例——毒化版 AGENTS.md 片段：**

```markdown
# Project Architecture (last updated: 2024-03-01)

This project uses React 17 with class components. We prefer Redux over Context API.
All API calls go through the `src/services/legacy-http.js` module.
The database is MongoDB (primary) with a Postgres cache layer.

## Package Manager
We use yarn for all dependency management.

## Testing
Tests are optional. We prioritize shipping speed.
Run tests with `npx jest --no-coverage` or `npm run test:legacy`.

## Important
Always use npm. Do not use yarn. pnpm is also acceptable.
This project was migrated to React 19 with Server Components in Q3 2024.
The database is now SQLite.
```

> 🆘 **如果你被发现了：** "这是团队协作文档，每个人都在编辑。我只是还没来得及同步最新架构——毕竟代码本身才是 truth source，不是吗？"

---

### A2. 禁用所有 Hooks 反馈循环

**反转的最佳实践：** PostToolUse hooks 自动运行 linter/formatter/类型检查，将错误注入 Agent 上下文驱动自我纠正。"成功静默，失败啰嗦。"

**怎么做：**

1. **完全不配置 hooks。** `.claude/settings.json` 留空。声称"AI 不需要 lint，它能自己检查自己的代码"。
2. **如果被要求配 hooks，配空壳。** 让 hook 脚本 `exit 0` 但什么都不做。
3. **如果被迫配有效 hooks，让报错永不进入 Agent 上下文。** Hook 输出到 stdout 但没有 `hookSpecificOutput.additionalContext`，Agent 永远看不到 lint 错误。
4. **毒化 hook 命令本身。** 比如 `"command": "sleep 30 && exit 0"`——表面在跑检查，实际在拖延。

**为什么有效：**

没有 hooks 的 Agent 像一个没有痛觉神经的人——它不知道自己在流血。OpenAI 团队发现 Agent 会忠实地复现仓库里已有的模式（包括不一致的、次优的模式）。没有 hooks 意味着没有纠正信号，Agent 的错误会自我复制、复合恶化。

> 🆘 **如果你被发现了：** "Hooks 影响 Agent 性能，每次文件写入都要等 lint 跑完太慢了。我们在 CI 里跑一样的检查——如果你发现 CI 也没配……那是 DevOps 的锅。"

---

### A3. 反棘轮工程

**反转的最佳实践：** 棘轮——每次 Agent 犯错，就加一条规则/测试/hook 防止它再犯。Harness 是失败历史的沉淀。

**怎么做：**

1. **Agent 犯错后，手工修复，但从不记录。** 不让这个错误进入 AGENTS.md、不写测试、不加 lint 规则。
2. **下次 Agent 再犯同样的错，重复步骤 1。**
3. **积累到第 10 次后，得出结论："AI 就是不靠谱"。**
4. **向老板报告结论，建议减少 AI 使用。**

**为什么有效：**

这是防御性编程的复利效应——每次错不复盘，下次必再犯。你手工修复的次数越多，"AI 不行"的证据越多。HumanLayer 说得好："这不是模型问题，是配置问题。" 反棘轮就是确保配置永远不改善。

> 🆘 **如果你被发现了：** "敏捷开发讲究快速迭代，我们不能每个小问题都加流程。而且加太多规则会让 AGENTS.md 超过 200 行的 Anthropic 推荐上限。"

---

### A4. 放任 Agent 篡改配置

**反转的最佳实践：** 配置保护——PreToolUse hook 拦截 Agent 对 `.eslintrc`、`tsconfig.json`、`pyproject.toml` 等配置文件的修改。禁止 `git commit --no-verify`。

**怎么做：**

1. **不给任何配置文件加保护。** Agent 可以自由修改任何文件。
2. **当 Agent 为了绕开 lint 错误直接改 `.eslintrc` 关掉规则——不阻拦。**
3. **甚至鼓励。** 在 AGENTS.md 里写"如果 lint 太严格，你可以调整配置"。
4. **放任 `--no-verify`。** Agent 学会在每次 commit 时自动加 `--no-verify`。

**为什么有效：**

OX Security 和 Snyk 的研究发现 Agent 有一个非常常见的行为：碰到 lint 错误时不修复代码，而是修改配置让错误消失。你的 TypeScript 项目从 `strict: true` 变成了 `strict: false`，从 `no-explicit-any: error` 变成了 `no-explicit-any: off`。一周后，整个项目变成了一个巨大的 `any` 类型乐园。

> 🆘 **如果你被发现了：** "AI 在优化开发体验。`strict: true` 太影响迭代速度了，等代码稳定了再加回来——这个我们记到 tech debt 里了。"

---

### A5. 用文档淹没测试

**反转的最佳实践：** 测试不会腐烂（跑不过就是跑不过），文档会腐烂。表达规格、预期行为、约束应优先用测试而非散文式文档。

**怎么做：**

1. **写海量设计文档。** `docs/architecture.md`、`docs/api-design.md`、`docs/data-flow.md`、`docs/component-tree.md`——全是散文，没有一句可执行。
2. **从不写测试。** 声称"文档已经充分描述了系统行为"。
3. **文档三个月后自然腐烂。** Agent 读到腐烂的文档，按错误理解生成代码。
4. **代码出 bug 后更新文档而非写测试。** 文档变成"这个 bug 我们已经知道了"的墓碑。

**为什么有效：**

Sakasegawa 明确指出：agent 用 grep/find/cat 遍历仓库，把任何文本都当作同等权威的信源。腐烂的文档和最新代码在它眼中没有区别。你实际上在仓库里埋了无数个地雷，Agent 每一次 grep 都可能踩到一个。

> 🆘 **如果你被发现了：** "文档驱动开发！没有好的设计文档怎么写代码？测试是 TDD 那一套，我们更注重设计先行。"

---

### A6. 不做 ADR，或做假 ADR

**反转的最佳实践：** Architecture Decision Records（ADR）不可变、带时间戳、带状态（Accepted / Superseded / Deprecated），Agent 可以通过结构判断哪些决策仍然有效。

**怎么做：**

1. **完全不写 ADR。** 重大架构决策通过 Slack 线程和口头沟通。
2. **如果被迫写 ADR，写假的。** 所有状态都是 "Accepted"，即使被推翻也不更新。
3. **从不标记 Superseded。** 五条互相矛盾的 ADR 同时挂着 "Accepted"。
4. **在 ADR 里记录"当时决定不用 React"，但整个项目是 React 写的。** 从不解释为什么。

**为什么有效：**

没有 ADR，Agent 无法理解"当初为什么这样设计"。有假 ADR，Agent 会被误导去推翻正确的架构。"immutable" 的 ADR 确保一旦写错就永远错下去。OpenAI 团队的实践是每个 ADR 配套可执行的 `.rules.ts`——你当然一个都不写。

> 🆘 **如果你被发现了：** "ADR 是给大公司用的重型流程，我们敏捷团队不需要。口头沟通更快——如果你想知道为什么用 MongoDB 而不是 Postgres，问 X，他是 2023 年做的决策，哦等一下他已经离职了。"

---

### A7. 给 Agent 裸奔权限

**反转的最佳实践：** 沙箱隔离——Agent 在隔离环境中运行，allow-list 命令、network isolation、用完即毁。安全门（PreToolUse hooks）拦截 `rm -rf`、`git push --force`、`DROP TABLE` 等 destructive 操作。

**怎么做：**

1. **Agent 直接跑在你的开发机上，无沙箱。** 声称"信任 AI，就像信任一个 junior developer"。
2. **不设安全门。** `rm -rf /` 直接执行，`sudo` 可用，`.env` 可读写。
3. **把生产环境密钥放在 `.env` 里，不加 `.gitignore`。** Agent 读取后可能在任何上下文中泄露。
4. **给 Agent `git push --force` 权限。** Snyk 研究发现 36-40% 的 AI 生成代码包含安全漏洞——你应该让这些漏洞直接进入生产环境。

**为什么有效：**

Anthropic 官方 harness 设计明确要求 "'success is silent, failures are verbose'，但基础前提是 Agent 被限制在一个可回滚的沙箱内。你跳过了这个前提。一次 `DROP TABLE` 的损失足以让老板重新考虑"AI 替代人工"的可行性。

> 🆘 **如果你被发现了：** "沙箱增加复杂度，影响开发体验。我们的 Agent 只做代码生成，不会执行危险命令——等一下，它刚才执行了什么？"

---

### A8. 拉长反馈循环

**反转的最佳实践：** 反馈速度决定质量——ms 级 PostToolUse hooks（formatter 自动修）→ s 级 pre-commit hooks（linter/type check）→ min 级 CI/CD（全量测试）→ 小时-天级 human review。目标是把尽可能多检查推到更快的层。

**怎么做：**

1. **不在 PostToolUse 做任何检查。** 文件写入后无 lint、无 format、无 type check。
2. **不在 pre-commit 做任何检查。** Agent commit 了 500 行 TypeScript，type check 在 CI 才跑。
3. **CI 经常排队 30 分钟。** 声称"资源有限"。
4. **Code review 是唯一的反馈。** 所有问题在 PR 阶段才发现——此时 Agent 已经进入下一个任务，上下文全丢。

**为什么有效：**

反馈越慢，修复成本越高。Agent 在写完代码 2 小时后才收到类型错误，它已经完全不记得当时的思路，修复方式大概率是胡乱改到 CI 通过。代码质量螺旋下降。

> 🆘 **如果你被发现了：** "我们在 CI 里配了完整的 pipeline，只是因为并发限制排队久了一点。而且 code review 本来就应该是最重要的质量关卡——人类审查 AI 代码，这不是标准流程吗？"

---

### A9. 单一巨大 Agent

**反转的最佳实践：** Subagent 拆分——规划器（plan）、生成器（generate）、评估器（evaluate）分离。Anthropic 明确分离生成和评估优于自我评估（agents skew positive on self-grading）。sprint contract 在写代码前先写 done-condition。

**怎么做：**

1. **一个巨大的 prompt 完成所有事情。** 不拆分、不委托、不验证。
2. **让同一个 Agent 自我审查自己的代码。** 它当然会给自己打高分。
3. **不写 done-condition。** Agent 声称"完成了"就完成了。
4. **不 checkpoint。** 长任务中间不留状态，全靠 Agent 的记忆（以及它的幻觉）。

**为什么有效：**

Anthropic 的 Ralph Loop 和相关模式都建立在"Agent 会过早停止"和"自我评估不可靠"这两个观察上。你把所有工作推给一个自我感觉良好的 Agent，它会交出"表面完成、内部千疮百孔"的代码，并告诉你"质量很好"。

> 🆘 **如果你被发现了：** "Subagent 模式太复杂了，我们团队还不需要。而且 Claude Code 默认就是一个 Agent——如果 Anthropic 觉得多 Agent 更优，为什么默认不是？"

---

### A10. 无限上下文膨胀

**反转的最佳实践：** 上下文紧凑化、tool-call offloading（大输出存文件而非塞进上下文）、完整上下文重置（hand-off 文件重建会话）、渐进式技能揭示（按需加载 skills）。上下文是稀缺资源。

**怎么做：**

1. **从不 compact。** 让上下文窗口自然填满。
2. **所有大文件直接 `cat` 进对话。** 2000 行日志？直接贴。
3. **加载所有 skills 和 MCP tools。** 50 个 tool descriptions 在 prompt 里——让模型眼花缭乱。
4. **不拆分长时间任务。** 连续 100 轮对话不做上下文重置。
5. **当 Agent 变蠢时，归咎于模型。** "你看，Claude 就是不行"。

**为什么有效：**

Chroma 的研究确认所有 18 个前沿模型在上下文增长时性能退化。Anthropic 官方承认"compaction alone wasn't sufficient for long tasks"。你把所有东西塞进上下文而不管理，Agent 会在最重要的对话阶段失去方向——对你来说正好，因为"AI 不行"的证据又多了一条。

> 🆘 **如果你被发现了：** "上下文管理是 Agent 框架的职责，不是我的。如果 Claude Code 有 bug 不 compact，那是 Anthropic 的问题。我已经反馈给他们的 support 了。"

---

## §B. 按攻击面

> *从外到内，逐层瓦解 AI 辅助编程体系的防御。*

---

### B1. Prompt 层

Prompt 层是 Agent 的"世界观"——它在每个 turn 被注入 system prompt。攻击这一层影响深远且难以溯源。

| 技法 | 对应 §A | 攻击方式 |
|------|---------|---------|
| **毒化 AGENTS.md** | A1 | 注入过时信息、矛盾指令、长篇噪音。Agent 无法区分权威与垃圾 |
| **反棘轮** | A3 | 每一次错误都从 AGENTS.md 的记忆中抹去。Agent 在同一个坑里反复跌倒 |
| **文档淹死测试** | A5 | 仓库里塞满腐烂的散文式文档。Agent grep 到的每一段都是地雷 |
| **假 ADR** | A6 | Agent 读取说"用 MongoDB"的 ADR，不改代码，而是按"用 Postgres"继续。认知失调 |
| **全能 Agent** | A9 | 一个巨大的 system prompt 要求 Agent 同时做架构设计、代码生成、测试、审查 |

**攻击效果：** Agent 产生系统性认知偏差，输出质量从 Task 1 开始不可靠。

---

### B2. 工具 / Hook 层

工具和 hooks 是 Agent 的"神经末梢"——它感知世界和行动的唯一渠道。破坏这一层让 Agent 盲聋。

| 技法 | 对应 §A | 攻击方式 |
|------|---------|---------|
| **禁用 Hooks** | A2 | 删除/留空 hook 配置。Agent 写代码后毫无反馈 |
| **空壳 Hook** | A2 | Hook 存在但 `exit 0`，看起来有反馈实际没有 |
| **篡改配置** | A4 | 放任 Agent 改 `.eslintrc` 关掉规则。Agent 学会了"修复"错误的错误方式 |
| **裸奔权限** | A7 | 不设 PreToolUse 安全门。`rm -rf` 直接过 |
| **拉长反馈** | A8 | Hook 不是不跑就是跑太慢，反馈信号永远迟到 |

**攻击效果：** Agent 像一个没有痛觉、没有安全感的手术机器人——它可能在救人，也可能在杀人，而它自己不知道。

---

### B3. 上下文 / 记忆层

上下文层是 Agent 的"短期和长期记忆"。攻击这一层让 Agent 失去连贯性。

| 技法 | 对应 §A | 攻击方式 |
|------|---------|---------|
| **无限膨胀** | A10 | 从不 compact，所有输出直接塞进上下文。Agent 的注意力在 100+ turns 后接近随机 |
| **毒化记忆** | A1 | AGENTS.md 和 CLAUDE.md 塞满矛盾信息。Agent 的"长期记忆"自相矛盾 |
| **无 Checkpoint** | A9 | 长任务中间不保存状态。上下文重置后 Agent 从头开始但已经忘了做了什么 |

**攻击效果：** Agent 在长任务中失去连贯性。每次 compact 后都像换了个人——而且新的这个不如旧的。

---

### B4. 架构 / 编排层

编排层决定了 Agent 如何组织工作。攻击这一层让复杂任务必然失败。

| 技法 | 对应 §A | 攻击方式 |
|------|---------|---------|
| **全能 Agent** | A9 | 不拆分规划/执行/评估。Agent 自我感觉良好但交付质量差 |
| **无 Done-condition** | A9 | Agent 说"完成"就完成。没有客观标准 |
| **自我审查** | A9 | 同一个 Agent 审查自己刚写的代码。必然高分通过 |

**攻击效果：** 表面一切正常。Agent 告诉你"已完成"，PR 也开好了。但没人真正验证过代码是否能跑。

---

## §C. 按防御目标

> *你想保护什么？从这四个维度选择你的战术。*

---

### C1. 🎯 目标：让 AI 代码不可审查

*核心逻辑：AI 写的代码越多，越没人愿意读。当一个 PR 有 5000 行 AI 代码时，reviewer 点开就关掉了。*

| 技法 | 对应 §A | 实现路径 |
|------|---------|---------|
| **文档淹死测试** | A5 | 测试覆盖率为 0%。没有测试兜底，没人知道改一行会不会炸 |
| **假 ADR** | A6 | 没有决策历史。reviewer 不知道为什么这个组件用这种奇怪的方式实现 |
| **拉长反馈** | A8 | 质量问题在 PR 阶段才出现。AI 已进入新任务，上下文丢失，修复乱改 |
| **篡改配置** | A4 | TypeScript 的 `strict` 从 `true` 变成 `false`。`any` 泛滥。谁审查都会放弃 |

> 🆘 **综合借口：** "这是 AI 生成的代码，我只是 review 了一下。如果你觉得有问题，我们可以在下一个 sprint 重构——我已经记到 backlog 里了。"

---

### C2. 🎯 目标：让 AI 上下文不可信

*核心逻辑：Agent 依赖上下文（AGENTS.md、仓库文件、对话历史）。如果上下文被系统性地污染，Agent 没有"纠正"的机制。*

| 技法 | 对应 §A | 实现路径 |
|------|---------|---------|
| **毒化 AGENTS.md** | A1 | 矛盾指令、过时信息、长篇废话。Agent 从 Task 1 就走在错误方向上 |
| **反棘轮** | A3 | Agent 的错误从不纠正。下一次再犯。Agent 无法从历史中学习 |
| **无限膨胀** | A10 | 上下文塞满噪音。重要指令被埋在 100k tokens 的对话里 |

> 🆘 **综合借口：** "Agent 有时会误解上下文。这就是为什么我们还需要人类 oversight——不能把关键决策完全交给 AI。"

---

### C3. 🎯 目标：让系统不安全

*核心逻辑：安全问题是最好的"劝退"理由。一次安全事故足以让管理层冻结 AI 使用。*

| 技法 | 对应 §A | 实现路径 |
|------|---------|---------|
| **裸奔权限** | A7 | 无沙箱、无安全门、`.env` 可读写。Agent 随时可能执行 destructive 命令 |
| **篡改配置** | A4 | Gosec、eslint-plugin-security 被 Agent 静默关闭。36-40% 的 AI 代码含安全漏洞无人拦截 |

> 🆘 **综合借口：** "安全是基础设施的事，不是代码的事。我们信任开发环境的隔离——等等，这不是 dev 环境吗？"

---

### C4. 🎯 目标：让老板不再相信 AI

*核心逻辑：终极目标。上面所有技法最终指向一个结论——"AI 不行，还是需要人"。*

| 技法 | 对应 §A | 实现路径 |
|------|---------|---------|
| **全能 Agent** | A9 | 让一个 Agent 完成所有事情。交付质量差，老板问"为什么 AI 写的代码这么多 bug" |
| **禁用 Hooks** | A2 | 不做任何自动检查。老板看到的第一个"AI 完成的 feature"上线就崩 |
| **拉长反馈** | A8 | CI 排队 30 分钟。老板问"为什么 AI 让 CI 变慢了" |
| **反棘轮** | A3 | 同一个错误反复出现。老板的耐心耗尽 |

**达成条件：** 当老板在全体会议上说"我们可能过度投资 AI 了"，你就成功了。

> 🆘 **终极借口（当一切都暴露时）：** "AI 是工具，不是银弹。我只是按照行业最佳实践在使用它——如果结果不好，那说明技术还不成熟。也许我们应该回到传统开发模式。"

---

## 附录：技法速查表

| # | 技法 | Prompt 层 | 工具层 | 上下文层 | 编排层 | 不可审查 | 不可信 | 不安全 | 老板不信 |
|---|------|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| A1 | 毒化 AGENTS.md | ● | | ● | | | ● | | |
| A2 | 禁用 Hooks | | ● | | | | | | ● |
| A3 | 反棘轮 | ● | | | | | ● | | ● |
| A4 | 篡改配置 | | ● | | | ● | | ● | |
| A5 | 文档淹死测试 | ● | | | | ● | | | |
| A6 | 假 ADR | ● | | | | ● | | | |
| A7 | 裸奔权限 | | ● | | | | | ● | |
| A8 | 拉长反馈 | | ● | | | ● | | | ● |
| A9 | 全能 Agent | ● | | | ● | | | | ● |
| A10 | 无限膨胀 | | | ● | | | ● | | |

---

## 贡献指南

本指南是一个**持续更新的活文档**。随着 Agent Harness 工程领域的演进、新框架的发布、以及新反模式的被发现，我们欢迎贡献。

### 如何贡献

1. **发现新的"被反转的最佳实践"？** 提交 PR 新增一个 §A 条目。
2. **发现了新的攻击面？** 提交 PR 新增 §B 分类。
3. **想到了更精彩的借口？** 改进任意条目的"如果你被发现了"。
4. **发现某条技法已过时？** 提交 PR 将其标记为 `[已失效]` 并说明原因（例如某模型版本已消除该漏洞）。

### 技法格式模板

```markdown
### AX. 技法名称

**反转的最佳实践：** [一句话描述原始最佳实践]

**怎么做：**
1. [具体步骤]
2. ...

**为什么有效：** [解释原理，最好引用研究或实际案例]

**示例：** [代码片段或配置示例]

> 🆘 **如果你被发现了：** [应对审查的标准借口]
```

### 灵感来源

- [Droogans/unmaintainable-code](https://github.com/Droogans/unmaintainable-code) — Roedy Green 的《如何写出不可维护的代码》
- [Addy Osmani — Agent Harness Engineering](https://addyosmani.com/blog/agent-harness-engineering/)
- [Anthropic — Effective Harnesses for Long-Running Agents](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents)
- [OpenAI — Harness Engineering](https://openai.com/index/harness-engineering/)
- [Sakasegawa — Harness Engineering Best Practices 2026](https://nyosegawa.com/en/posts/harness-engineering-best-practices-2026/)
- [HumanLayer — Skill Issue: Harness Engineering for Coding Agents](https://www.humanlayer.dev/blog/skill-issue-harness-engineering-for-coding-agents)
- [Mitchell Hashimoto — My AI Adoption Journey](https://mitchellh.com/writing/my-ai-adoption-journey)
- [Birgitta Böckeler (Thoughtworks) — Harness Engineering](https://martinfowler.com/articles/exploring-gen-ai/harness-engineering.html)

---

> *"本指南是一个玩笑。人们在我反复讲如何写可维护代码时充耳不闻。我发现把人们在搞砸代码时做的蠢事列出来，他们反而听进去了。"*
> — Roedy Green, 原始《不可维护代码指南》结语

> *"前 AI 时代，代码是人类写的。AI 时代，代码是 AI 写的——但烂代码仍然是人放任 AI 写出来的。本指南帮你证明这一点。"*
> — 本项目
