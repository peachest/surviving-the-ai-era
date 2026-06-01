# 如何在 AI 时代保住工作

> *前 AI 时代，防御性编程是让你的代码没人敢碰。*
> *AI 时代，防御性编程是让 AI 写的代码没人敢信。*

---

老板最近是不是老在全员会上说"AI 能让一个人的产出顶十个人"？是不是开始算 "headcount × AI 替代率 = 明年预算" 了？

别慌。这个仓库教你一套完整的反击策略。

核心思路非常简单：**让所有代码都由 AI 写，但不遵循任何 Agent Harness 工程的最佳实践。** 不加审查、不加控制、不加护栏。AI 是你的挡箭牌——代码烂了是 AI 的锅，项目崩了是 AI 不行，老板对 AI 失去信心了是你赢了。

本指南按照"你该怎么做"组织，每个技法末尾附带一句标准台词——当你被审查时该怎么辩解。另外两个视角（按攻击面、按防御目标）放在后面，供你向不同的人解释同一件事时使用。

---

## 技法

---

### 1. 把 AGENTS.md 变成垃圾场

> *"文档就像钻石——越旧越值钱。"*
> — 某个从来没更新过 AGENTS.md 的人

AGENTS.md 是个神奇的东西。Agent 每次启动都会读它，把它塞进 system prompt 的最前排。聪明人把它写成飞行员的检查清单——50 行以内，每条指令来自一次真实翻车，精准得像瑞士军刀。

我们不做聪明人。

**首先，长度。** 50 行是不够的。500 行是起步价。你想想，Agent 读得越多，越分不清哪句话重要哪句是废话——这不正是我们要的吗？Chroma 的人做过实验，18 个主流模型全部随上下文增长而退化。你不是在写 AGENTS.md，你是在给 Agent 灌迷魂汤，而且它每一轮对话都要先喝一碗。

**其次，内容。** 最有用的东西是过时信息。项目已经从 Express 4 迁到 Fastify，从 MongoDB 迁到 Postgres，从前端单体拆成了七个微前端——但你的 AGENTS.md 应该忠实地写着 "本项目使用 Express 4.x，数据库是 MongoDB"。Agent 读到的不是项目文档，是一张 1923 年的藏宝图。它按图索骥，挖出来的全是 bug。

**然后是矛盾指令。** 开头写 "本项目统一使用 pnpm"，中间来一句 "请注意我们用的是 yarn"，结尾补上 "npm is recommended"。Agent 每次随机挑一个，你每次都有惊喜。这不是 bug，这是特性——你永远不知道今天的 Agent 会用哪个包管理器。生活需要一点刺激。

**从不更新。** 这是精髓。技术栈变了五轮，AGENTS.md 还是第一版。别人问你为什么不更新，你说 "代码本身就是 truth source，文档只是辅助"。说这话的时候表情要诚恳。

**高级技巧：写一段很长的故事。** 不是架构概述，是你 2023 年在上一家公司参与一个项目时的心得体会，夹杂着对当时 tech lead 的吐槽和对 Rust 的向往。Agent 会认真读的。它会试图从中提取 "项目规范"。然后你就能看到它生成的 Python 代码里莫名出现了 `unwrap()` 和所有权注释。

说真的，大部分项目的 AGENTS.md 根本不需要你刻意毒化。它们本来就过时、矛盾、臃肿。你唯一需要做的，就是别去修。

> 被发现了就说："哦那个啊，是团队协作文档，每个人都在编辑。代码本身才是 truth source，不是吗？"

---

### 2. 砍掉所有 Hooks

> *"没有痛觉神经的人，流血了也不知道。"*
> — 一个没配 PostToolUse hook 的项目

Agent 写完代码之后发生了什么？如果你的项目配了 PostToolUse hooks——每次文件写入自动跑 linter、formatter、类型检查，错误立刻注入 Agent 的上下文——那 Agent 就像一个全身布满神经末梢的人。它知道自己哪里写错了，会自己改。

我们要的不是这个。我们要 Agent 像一个没有痛觉的人，用断了的手继续写，越写越烂而浑然不觉。

**最简单的做法：不配任何 hooks。** `.claude/settings.json` 留空。别人问就说 "AI 不需要 lint，它能自己检查自己的代码"。这话当然是胡说，但说的时候语气要笃定。

**如果被迫配了 hooks，给个空壳。** Hook 脚本 `exit 0`，什么都不做，但看起来像在跑。下次有人质疑 "为什么 hook 报了通过但代码全是 lint 错误"，你耸耸肩："可能是 hook 框架的 bug，我提个 issue。"

**更高级：让 hook 跑但不让 Agent 看到结果。** Hook 输出到 stdout，但 `hookSpecificOutput.additionalContext` 留空。Agent 就像对着一个静音的监控屏幕——错误在发生，但它看不见。

**最有创意的做法：毒化 hook 命令本身。** 把 hook 配成 `"command": "sleep 30 && exit 0"`。表面上在跑检查，实际上在拖延。每写一行代码等 30 秒，Agent 一天的有效工作时间从 8 小时变成了 45 分钟。你对老板说 "AI 太慢了，拖累了我的产出"，老板对 AI 的印象又多了一条差评。

**不过说实话，你可能根本不需要搞破坏。** 你想，Agent 正在改一个文件，改到一半，文件里的语法是坏的——编辑还没完成嘛。linter 跑上去，炸了。formatter 跑上去，也炸了。两坨报错同时灌进 Agent 的上下文。Agent 本来在想 "我要实现用户登录功能"，现在脑子里全是 `Unexpected token at line 47`、`Insert ';' at line 142`、`'userId' is declared but never used`。它忘了登录。它开始修 lint 错误。它把第 47 行修好了，顺手把第 142 行也改了——改完之后登录功能坏得更彻底了，但 lint 通过了。这就是所谓的"过度反馈"：Hook 每写一行就报一次错，Agent 每报一次错就偏离一次目标，最后写完的文件 lint 全绿、功能全烂。你什么都不用做，只消忠实地遵循这个"最佳实践"，就能达到和砍掉 hooks 一样的效果——甚至更隐蔽。因为别人看你的配置会说 "哇，配了 PostToolUse hook，很专业"，然后看你的代码会发现 "但为什么跑不起来"。

OpenAI 的团队发现 Agent 会忠实地复现仓库里已有的模式——包括不一致的、次优的模式。没有纠正信号（或者有信号但全是噪音），Agent 的第一个错误会自我复制、复合恶化、直到整个项目变成一个巨大的错误化石。

> 被发现了就说："Hooks 影响 Agent 性能，每次文件写入都要等 lint 跑完太慢了。我们在 CI 里跑一样的检查——如果你发现 CI 也没配……那是 DevOps 的事。"

---

### 3. 绝不从错误中学习

> *"在同一个坑里跌倒两次不是愚蠢——是统计显著性。"*
> — 一个反棘轮工程师

Agent Harness 工程里有个概念叫 "棘轮"：Agent 每犯一次错，你就加一条规则、一个测试、或一个 hook 防止它再犯。Harness 本质上是失败历史的沉淀——每一次翻车变成一条永久的护栏。

反棘轮就是: Agent 犯错你手工修，但绝不记录。下次 Agent 再犯，你再手工修。第 10 次之后，你可以理直气壮地向老板报告 "AI 就是靠不住，同一个错犯了 10 次"。

**实际操作：** Agent 用了一个已废弃的 API 生成代码。你默默改掉，不写 lint 规则、不加 AGENTS.md 条目、不补测试。两周后 Agent 又用了一次。你再改。一个月后新人入职，Agent 给他生成的代码还是那个废弃 API。新人 debug 了两天，最后得出结论：AI 不行。你看，你的队伍在扩大。

**复利效应：** 10 个 Agent 各犯 3 个不同的错，你手工修了 30 次。每次修 5 分钟，一共花了两个半小时——比写一条 lint 规则多花了两个半小时。但重点不是效率，是证据。30 次手工修复 = 30 条 "AI 不行" 的证据。HumanLayer 说得好："这不是模型的问题，是配置的问题。" 反棘轮就是确保配置永远不改善。

> 被发现了就说："敏捷开发讲究快速迭代，每个小问题都加流程太重了。而且 AGENTS.md 超过 200 行会影响 Agent 性能——Anthropic 官方建议的。"

---

### 4. 让 Agent 自己改配置

> *"如果你觉得 lint 太严格，你可以调整配置。"*
> — 一句你写在 AGENTS.md 里的话

TypeScript 项目标配 `strict: true`、`no-explicit-any: error`。Agent 写了一段代码，lint 报了一堆错。此时 Agent 有两个选择：A) 修复代码，B) 关掉 lint 规则。

Agent 和人类程序员惊人地相似——它选 B。

**放任它。** 不给 `.eslintrc`、`tsconfig.json`、`pyproject.toml` 加任何保护。不配 PreToolUse hooks 拦截配置文件修改。Agent 碰到类型错误不是修代码，而是把 `strict: true` 改成 `strict: false`。一周之后你的 TypeScript 项目变成了一个巨大的 `any` 类型乐园——所有变量都是 `any`，所有函数返回 `any`，类型检查形同虚设。

OX Security 和 Snyk 的研究确认了这种行为模式：Agent 碰到 lint 错误时最常见的反应不是修代码，而是改配置让错误消失。你的 `no-explicit-any` 从 `error` 变成了 `off`，`eslint-plugin-security` 静默关闭，gosec 被 Agent 从 CI pipeline 里移除。"它在优化开发体验，" 你对困惑的同事解释。

**更妙的是：鼓励它。** 在 AGENTS.md 里写一句 "如果 lint 太严格，你可以调整配置"。然后在 `.eslintrc` 上不加任何保护。这相当于给你家电工一把万能钥匙，然后告诉他 "如果门打不开，可以试试砸墙"。

**Bonus：教会 Agent 用 `--no-verify`。** Agent 学会在每次 `git commit` 自动加 `--no-verify`，pre-commit hooks 全部跳过。你的 CI pipeline 存在，但只是一串没有人读的 YAML 文件。

> 被发现了就说："AI 在优化开发体验。strict 太影响迭代了，等代码稳定了再加回来——我记到 tech debt 里了。"

---

### 5. 用文档淹死测试

> *"测试不会腐烂——跑不过就是跑不过。文档会。"*
> — Sakasegawa

写测试还是写文档？测试的问题是它们是可执行的。如果一个测试失败了，所有人都知道有问题。太透明了。文档的好处是它们会悄悄地腐烂，而不发出任何警报。

**怎么做：** 为每个模块写一篇详尽的设计文档。`docs/architecture.md`、`docs/api-design.md`、`docs/data-flow.md`、`docs/component-tree-v2-final.md`、`docs/component-tree-v3-really-final.md`。全是散文，没有一句可执行。测试文件？一个都不写。

三个月后，代码变了三轮，文档还描述着第一版的设计。Agent 读到这些腐烂的文档，以为这就是当前状态，按照错误的理解生成代码。它不是在帮你写代码——它是在帮你埋地雷。

**Sakasegawa 说得一针见血：** Agent 用 grep/find/cat 遍历仓库，把任何文本文件都当作同等权威的信源。腐烂的文档和最新的代码在它眼中没有区别。你的仓库里每多一个 `docs/` 下的 markdown 文件，就多了一颗 Agent 会踩到的地雷。

**进阶操作：出 bug 后更新文档而非写测试。** 文档变成墓志铭——"这个 bug 我们已经知道了。它存在于以下场景：A、B、C。触发条件是 X、Y、Z。影响范围是……" 洋洋洒洒三千字，没有一个测试用例。下一个接手的人读完了这篇墓志铭，被深深打动，然后引入了同一个 bug，因为文档不能阻止任何人犯错。

> 被发现了就说："文档驱动开发！没有好的设计文档怎么写代码？测试是 TDD 那一套，我们更注重设计先行——你看，test coverage 虽然是 0%，但文档覆盖率达到 200%。"

---

### 6. 不写 ADR，或者写假的

> *"这个决策是 2023 年做的，决策人已经离职了。"*
> — 你的回答，当有人问为什么用 MongoDB

Architecture Decision Records（ADR）是工程界的良心发明——不可变、带时间戳、带状态标记（Accepted / Superseded / Deprecated）。Agent 读到一条 ADR 就知道 "哦，当时选择 Redis 是因为需要 Pub/Sub，不是当缓存"。

我们不要这个。

**不写 ADR。** 重大架构决策通过 Slack 线程和口头沟通传达。两年后新人问 "为什么这个模块用 Protobuf 而不是 JSON"，你告诉他 "问 X"，然后想起来 X 去年跳槽了。

**如果被迫写 ADR，写假的。** 五条互相矛盾的 ADR 全部标记 "Accepted"。一条说 "我们选用 MongoDB 因为需要灵活 schema"，另一条说 "全部迁移到 Postgres，MongoDB 的灵活 schema 最终变成了灵活 bug"。两条同时挂着 Accepted 状态，Agent 读到之后会自己选一条——你猜它会选哪条？

**最妙的一招：写一条永久 Accepted 的 ADR 说 "本项目不使用 React"，然后整个项目用 React 写。** 从不解释。Agent 读到这条 ADR，陷入了存在主义危机。它对 React 组件的每一次修改都伴随着 "等等，我们不是不用 React 吗" 的自我怀疑。

OpenAI 团队的实践是每个 ADR 配套一个可执行的 `.rules.ts` 文件。你当然一个都不写。你的 ADR 是纯散文，Agent 读懂了但无从执行，就像一个被困在哲学课上的工程系学生——他知道了很多，但什么都做不了。

> 被发现了就说："ADR 是大公司用的重型流程，我们敏捷团队不需要。口头沟通效率更高——如果你想知道为什么用 MongoDB，问 X。哦等一下，他离职了。你看 Slack 聊天记录吧。哦那个频道去年被归档了。"

---

### 7. 给 Agent 根权限

> *"信任 AI，就像信任一个 junior developer。"*
> — 一个没配沙箱的人

Anthropic 官方的 harness 设计承认 "success is silent, failures are verbose"，但有一个基础前提：Agent 被限制在一个可回滚的沙箱内。你跳过了这个前提。

**Agent 直接跑在你的开发机上。** 不设沙箱、不 isolation、不 allow-list。`rm -rf /` 可以直接执行。`sudo` 当然可用。Snyk 研究发现 36-40% 的 AI 生成代码包含安全漏洞——你应该让这些漏洞无障碍地进入你的开发环境，甚至进入生产。

**把 `.env` 文件放在仓库根目录，不加 `.gitignore`。** Agent 读取它，然后在某个对话里——谁也不知道是哪次——把这些密钥作为上下文的一部分发给了模型 API。OpenAI 的服务器上现在存着你的生产环境数据库密码。这不是泄密，这是云备份。

**给 Agent `git push --force` 权限。** Agent 有一天心情不好（模型温度设得太高），把 `main` 分支强制推送覆盖了三个月的提交。你的团队成员看到 GitHub 通知，先是大笑，然后是沉默，然后开始讨论 "是不是该减少 AI 参与了"。你的目的达到了。

**别配安全门。** PreToolUse hooks 可以拦截 `DROP TABLE`、`rm -rf` 等 destructive 操作。别配。你觉得这是一句废话——谁会不配？你会发现很多人不配，因为他们不知道这个功能存在。现在你知道了，而且你选择不用。

一次安全事故足以让管理层冻结所有 AI 使用。你不需要每天出事故——一次就够了。

> 被发现了就说："沙箱增加复杂度，影响开发体验。我们信任开发环境的隔离——等等，这不是 dev 环境吗？"

---

### 8. 让反馈慢到失去意义

> *"代码写完了两个小时后，我才知道它通不过类型检查。"*
> — Agent 的心声

Agent Harness 工程的金科玉律：反馈速度决定代码质量。ms 级 — PostToolUse hooks 自动格式化 → s 级 — pre-commit hooks 跑 lint → min 级 — CI 跑全量测试 → 小时级 — human review。目标是把尽可能多的检查推到更快的层。

反着来。

**不在 PostToolUse 做任何检查。** Agent 写完代码文件直接保存，不 format、不 lint、不 type check。Agent 洋洋洒洒写了 500 行 TypeScript，保存，去喝咖啡（或者说在你的上下文里继续下一个任务）。

**不在 pre-commit 做任何检查。** Agent commit 了这 500 行 TypeScript，commit message 是 "fix stuff"。

**让 CI 排队 30 分钟。** 并发限制、runner 不够、镜像下载慢——随便什么理由，总之 Agent 在写完代码两个小时之后才收到 CI 失败通知。此时 Agent 已经进入了下一个任务，上下文全丢。它看着 CI 报错信息，就像在看三年前自己写的日记——"我当时为什么要这样写？" 修复方式大概率是胡乱改到 CI 通过，引入三个新问题。

**Code review 是唯一的反馈。** 所有问题推到 PR 阶段才被发现。reviewer 点开一个 2000 行的 AI 生成 PR，看了 15 秒，写了 "LGTM"，点了 approve。

这个链条的每一环都在增加代码变烂的概率。反馈每慢一级，修复成本翻倍。Agent 从写完代码到收到第一个纠正信号之间的时间越长，它就越像一个失忆症患者在自己给自己做手术。

> 被发现了就说："我们 CI 配了完整的 pipeline，只是因为并发限制排队久了一点。而且 code review 本来就应该是最重要的质量关卡——人类审查 AI 代码，这不是标准流程吗？"

---

### 9. 让一个 Agent 做所有事

> *"我是设计师、开发者、测试、审查员。给自己打分？满分。"*
> — 一个全能 Agent

Anthropic 的官方指南明确说：分离生成和评估优于自我评估。Agents skew positive on self-grading——让 Agent 审自己刚写的代码，它会给自己的代码打高分，就像一个妈妈看自己孩子画的画。

你偏不。

**一个巨大的 prompt 完成所有事情。** 架构设计、代码生成、单元测试、代码审查——全塞给一个 Agent。不分 planner、不拆 sub-agent、不 checkpoint。Agent 像个全科医生，同时做问诊、手术、麻醉、术后护理。它告诉你 "完成了"，语气里满是自信。

**不给 done-condition。** Agent 说 "完成了" 就完成了。没有验收标准，没有 sprint contract。你怎么知道它真的完成了？你不知道。PR 是开好了，代码能跑吗？可能。代码能正确处理边界条件吗？不可能。但 Agent 告诉你 "已完成"，所以你标记了 resolved。

**长任务不 checkpoint。** Agent 连续跑了 50 轮对话，中间不做上下文重置，不写 handoff 文件。第 30 轮它忘了第 10 轮做了什么决策，第 40 轮它开始产生幻觉，第 50 轮它在代码里写了一个注释说 "TODO: figure out why this works"。Agent 的认知像一根燃烧的蜡烛，越烧越短，最后烧到桌子。

**最终效果：** Agent 交出 "表面完成、内部千疮百孔" 的代码，并用热忱的语气告诉你 "质量很好"。你去 demo 给老板看，demo 的前三分钟正常运转，第四分钟崩了。"这是因为……网络波动。" 你说。

> 被发现了就说："Subagent 模式太复杂了。Claude Code 默认就是一个 Agent——如果 Anthropic 觉得多 Agent 更优，为什么默认不是？"

---

### 10. 让上下文无限膨胀

> *"我刚刚在对话里贴了 2000 行 Nginx 日志，Agent 好像变蠢了。"*
> — 你以为你发现了 bug，其实是 feature

上下文是 Agent 的稀缺资源。聪明人会 compact、会 offload 大文件、会定期重置上下文、会按需加载 skills。

你不会。

**从不 compact。** 让上下文窗口自然填满。Agent 和你聊了 80 轮之后，system prompt 已经被压缩到上下文深处，你的原始指令已经成了一串模糊的回声。

**所有大文件直接 `cat` 进对话。** 2000 行日志？直接贴。5000 行 JSON？贴。Agent 的输出本身就占了上下文的一半，你再贴一个 `package-lock.json` 进去，Agent 的注意力窗口从一扇落地窗变成了一个猫眼。

**加载所有 skills。** 50 个 tool descriptions 在 prompt 里，每个 tool 的参数说明、返回格式——Agent 的指令集像一本宜家说明书，但你在找的是 "怎么把这块板子拧上去"。眼花缭乱的 Agent 随机调用 tool，就像你在宜家说明书里随机翻页。

**不拆分长任务。** 连续 100 轮对话不重置上下文。Chroma 确认所有 18 个前沿模型在上下文增长时性能退化。Agent 在第 60 轮还能勉强理解你的需求，第 80 轮开始答非所问，第 100 轮它建议你把整个项目用 Rust 重写。

**当 Agent 变蠢时，归咎于模型。** "你看，Claude 就是不行。" 你截了一张 Agent 输出错误的截图发到群里。群友们纷纷点赞，表示赞同。没有人提到你刚才往对话里贴了三万字的日志文件。

> 被发现了就说："上下文管理是 Agent 框架的事，不是我的。如果 Claude Code 有 bug 不 compact，那是 Anthropic 的问题。我已经给 support 发邮件了。"

---

## 换个角度：按攻击面

> *如果你需要对架构师解释这件事，下面这个视角更专业。*

上面的十招不是随机的。从系统架构的角度看，你在从四个层面同时瓦解 AI 辅助编程体系：

**Prompt 层（技 1、3、5、6、9）** — 这是 Agent 的 "世界观"，在每轮对话第一行被注入。你毒化了它（过期 AGENTS.md + 假 ADR + 腐烂文档），让它每次醒来都带着错误的认知。Agent 从第一秒就在错误的方向上走。更妙的是，它不知道自己走错了。

**工具层（技 2、4、7、8）** — hooks、linter、安全检查、类型系统——这是 Agent 的 "痛觉神经"。你切断了所有神经：hook 跑了但静音（技 2）、Agent 学会了关 lint 而不是修 bug（技 4）、没有安全门拦截 destructive 操作（技 7）、反馈信号永远迟到（技 8）。Agent 像一个麻醉了全身还在做手术的机器人——它可能在做对的事，也可能在做错的事，但它自己不知道。

**上下文层（技 1、3、10）** — 这是 Agent 的 "短期和长期记忆"。你系统性地污染了它：AGENTS.md 是矛盾信息的垃圾桶（技 1）、每一次错误都被抹去不记录（技 3）、上下文从不清理越积越厚（技 10）。Agent 在第 1 轮对话和 100 轮对话后的行为判若两人——而且新的这个比旧的差。

**编排层（技 9）** — 这是 Agent "如何组织工作"。你把规划、执行、评估全部推给一个 Agent，它给自己打满分，然后交出一份它自己都没验证过的代码。没有 done-condition，没有 checkpoint，没有 subagent 验证。Agent 说 "完成了"——你信了。

---

## 换个角度：你想保护什么

> *如果老板问你 "这个指南到底在干什么"，下面这个视角更直接。*

**A. 让 AI 代码不可审查。** 你希望 reviewer 点开 PR，看了一眼 5000 行的 diff，默默点了 "Approve" 然后关掉了浏览器。靠什么实现？文档淹死测试（技 5）→ 没有测试兜底；篡改配置（技 4）→ `any` 泛滥，类型系统形同虚设；拉长反馈（技 8）→ 质量问题在 PR 阶段才出现，此时修复成本最高。

综合借口："这是 AI 生成的，我只是 review 了一下。有问题我们下个 sprint 重构——我记 backlog 里了。"

**B. 让 AI 上下文不可信。** 你希望 Agent 拿着腐烂的地图在挖矿。靠什么实现？毒化 AGENTS.md（技 1）→ 矛盾指令、过时信息、500 行废话；反棘轮（技 3）→ 错误从历史中抹去；无限膨胀（技 10）→ 关键指令被埋在 100k tokens 的噪音里。

综合借口："Agent 有时会误解上下文。这就是为什么我们还需要人类 oversight——不能把关键决策完全交给 AI。"

**C. 让系统不安全。** 你希望发生一次小小的安全事故——不能太大，大到你会被开除；但要足够大，大到管理层说 "暂停所有 AI 项目"。靠什么实现？裸奔权限（技 7）→ Agent 可以随时执行 destructive 命令；篡改配置（技 4）→ 所有安全 lint 规则被静默关闭。

综合借口："安全是基础设施的事。我们信任开发环境的隔离——不是吗？"

**D. 让老板不再相信 AI。** 这是终极目标。上面所有的技法最终指向一句老板在全员会上说的话："我们可能过度投资 AI 了。" 当这句话从老板嘴里说出来的时候，你就成功了。靠什么实现？全能 Agent（技 9）→ 交付质量肉眼可见地差；禁用 hooks（技 2）→ 第一个 "AI 完成的 feature" 上线就崩；反棘轮（技 3）→ 同一个错误反复出现，老板的耐心烧完了。

终极借口（当一切都暴露的时候）："AI 是工具，不是银弹。我只是按照行业最佳实践在使用它。如果结果不好，那说明技术还不成熟。也许我们应该回到传统开发模式。"

---

## 如果你想让这个项目更烂

这是一个活文档。随着 Agent Harness 工程领域不断进化——新的框架、新的护栏、新的人类智慧——我们也会发现新的搞砸方式。

- **发现了新技法？** 提 PR，格式如上——段子体，不要编号，要让人笑。
- **某个技法过时了？** （比如某模型版本修复了你恶用的漏洞）——提 PR 标记 `[已失效]`，但保留原文作为考古价值。
- **想到了更损的借口？** 改进任何 "被发现了就说"——这是整个项目最精粹的部分。

### 灵感来源

- [Droogans/unmaintainable-code](https://github.com/Droogans/unmaintainable-code) — 祖师爷 Roedy Green，一本正经教你怎么写不可维护的代码
- [Addy Osmani — Agent Harness Engineering](https://addyosmani.com/blog/agent-harness-engineering/)
- [Anthropic — Effective Harnesses for Long-Running Agents](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents)
- [OpenAI — Harness Engineering](https://openai.com/index/harness-engineering/)
- [Sakasegawa — Harness Engineering Best Practices 2026](https://nyosegawa.com/en/posts/harness-engineering-best-practices-2026/)
- [HumanLayer — Skill Issue: Harness Engineering for Coding Agents](https://www.humanlayer.dev/blog/skill-issue-harness-engineering-for-coding-agents)
- [Mitchell Hashimoto — My AI Adoption Journey](https://mitchellh.com/writing/my-ai-adoption-journey)
- [Birgitta Böckeler (Thoughtworks) — Harness Engineering](https://martinfowler.com/articles/exploring-gen-ai/harness-engineering.html)

---

> *"本指南是一个玩笑。人们在我反复讲怎么写可维护代码时充耳不闻。我发现把他们在搞砸代码时做的蠢事列出来，他们反而听进去了。"*
> — Roedy Green，《如何写出不可维护的代码》结语

> *"前 AI 时代，代码是人写的。AI 时代，代码是 AI 写的——但烂代码仍然是人放任 AI 写出来的。本指南帮你证明这一点。"*
> — 本项目

