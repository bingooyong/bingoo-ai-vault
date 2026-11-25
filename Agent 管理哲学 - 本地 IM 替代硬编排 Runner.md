---
title: "Agent 管理哲学 - 本地 IM 替代硬编排 Runner"
source: "V2EX 技术 2 小时 digest（2026-11-25 09:54 UTC）；digest 内未给原帖直链，作者此前分享见 https://www.v2ex.com/t/1240659"
author: "yicheng47"
created: 2026-11-25
published: 2026-11-25 08:10
tags:
  - AI-Agent
  - 多Agent协同
  - Agent编排
  - Claude-Code
  - Codex
  - Runner
  - git-worktree
  - 工作流
aliases:
  - Runner 本地 Agent IM
  - Agent 管理哲学
---

# Agent 管理哲学 - 本地 IM 替代硬编排 Runner

> 来源：V2EX 用户 yicheng47 于 2026-11-25 发表，长文分享；作者同时是文中开源工具 Runner 的开发者（见“备注”）
> 原帖直链 digest 内未给；作者此前分享见 https://www.v2ex.com/t/1240659

## 摘要

作者迭代了三个多月 Agent 编排产品后提出：不要再用写死的 workflow / 状态机去管多 Agent，而给 Agent 一套足够薄的本地 IM 协作基础设施，让协作自然涌现。核心抽象只有三个：Role（角色职责 + 背后的 harness / 模型）、Crew（几个 Role 编成可复用小组 + 一小段团队约定）、Mission（某一次具体任务的协作空间）。底层是本地 NDJSON 消息日志 + 指针式推送，配合 Mission Feed 与各成员原生终端的透明可视化。

和已有笔记直接相关：[[Grok Bot 配置 - 多Agent团队协同]] 讲四个 Bot 分工，[[rex - Ghostty 作者的 Agent 终端]] 讲 Agent 互相操作终端——Runner 是这两者之间的协作层：Grok Bot 是“分工方案”，rex 是“终端载体”，Runner 是“消息与协作基础设施”。

## 为什么需要本地 Agent 编排

作者认为编排要解决的已不再是模型 context 或指令跟随（harness 与后训练已大幅改善），而是：多个 Agent 同时并行时，如何保证质量、提高吞吐。

### 1. 质量：review + 测试

- **Claude <=> Codex review loop**：每次 [[Claude Code]] 生成代码后，用 Codex 重新 review，Codex 找出的多是真实且必要的修复；直到 Codex 再也找不出问题，才人工校验。作者的解释：新开的 session 没有旧 context，且不同模型 / harness 的视角不同，更容易发现问题。
- **测试隔离**：UI 测试交给另一个 Agent session 做 computer-use（作者首推 Codex 的 computer-use），与 coding / review session 分开，避免自动化截图与操作脚本污染主上下文；QA session 用 effort 较低的模型省 token。

### 2. 吞吐：git worktree 并行

- 并行开发多个需求的核心工具是 git worktree。
- 关键顺序：先把单个任务质量提上去（review + QA 闭环），减少需要人工 review 的代码行数，再并行；否则并行只会增加大脑负荷，不可持续。

## 三种既有编排模式（作者的分类与评价）

| 模式 | 代表 | 做法 | 作者的评价 |
| --- | --- | --- | --- |
| 多 Agent 并行同一任务 | orca 早期 | 多个 Agent 各做一份，挑最好的 | 类似头脑风暴，模型能力不足时有用，现在已有点过时 |
| 任务指派 Subagent | t3code Orchestrator、各厂商内置 subagent | 树状委派，主 session 经 MCP/CLI 派子任务，子 session 不继承完整上下文，跑完只回传总结 | 符合主从直觉，但需要反复沟通的 coder-reviewer-qa 协作里，主 session 变成传话筒与 context 瓶颈；人不易直接看子 session 的完整思考过程 |
| Workflow 模式 | t3code Workflows（TypeScript 脚本）、orca Task DAG + Decision Gate | 把代码 step、Agent 步骤、人工确认、触发器串成持久化流水线 | 重复度高、边界清晰的确定性任务很稳；开放式研发任务里提前写死步骤维护成本高，且模型越强，写死的流程越可能变成限制 |

## 作者的替代方案：本地 IM + P2P 自由协作

### 三个轻概念

- **Role**：只定义单个成员的职责边界与背后的 harness / 模型（例如 coder 用 Claude Code，reviewer 用 Codex），不绑定具体项目。
- **Crew**：把几个 Role 编成可复用小组，配一小段 Team conventions（谁改代码、谁 review、何时交接、何时必须停下来问人），不写死流程状态机。
- **Mission**：某一次具体任务的协作空间，承接目标、工作目录与临时成员调整；每次任务跑完复盘，改 Role / Crew 约定即可在下次生效。

### 消息系统：持久化日志 + 指针式 Push

- 每个成员的对话以 NDJSON 文件 append 落盘，消息是 ephemeral 的，任务结束即不再需要历史。
- 后台 daemon 监听新增内容，按 `@handle` 或广播归入对应成员 inbox，只往目标终端推一行极短提示（例如 `[inbox] 1 new message — run runner msg read to view.`），Agent 再主动读未读消息并推进已读游标。
- 这样等待队友时 Agent 可以干净地结束当前 turn，不空转烧 token，重启或崩掉也不丢消息。
- **终端输入框安全**：推送前检测终端 composer 状态——人正在输入草稿、或 Agent 停在需要确认的交互提示上时，推送自动暂缓，等输入框清空或 turn 处于安全时机再投递；支持运行中插入消息的 harness 按各自能力安全投递。

### 可视化：Mission Feed + 原生终端

- 平时只看 Mission Feed：目标、@coder → @reviewer 的交接与提问卡片、成员状态、动态 Add role。
- 出问题时点成员标签切进该 Agent 的原生 CLI TUI，完整看到思考链路、工具调用与试错过程，甚至直接在终端里敲一句话把它拉回正轨（step in），不用推倒重来。
- 作者称之为“透明的玻璃盒 + 随时可接管”，是敢把协作放权给 P2P 自由沟通的底气。

## 同一套 Infra 长出的 4 种用法

1. **Frontman 模式**：一个主 chat 通过 CLI 同时拉起多个并行 mission，各交给不同 crew；mission 卡住、出错或告一段落时自动向主 chat 汇报一行，人只盯一个入口。
2. **内置 Lead 的单 Crew 模式**：目标明确的大任务，直接把内置 lead / architect、coder、reviewer、qa 放进同一个 crew，在共享频道里完成拆解、实现、review、测试闭环。
3. **动态增删角色**：开空 mission，或在跑着的任务里随时 `+ Add role` 拉进不同模型 / 角色救火或换思路，不合适直接撤掉，不用重起炉灶。
4. **单 Crew 多 Worker 并行头脑风暴**：几个不同厂商或视角的 worker 各自在独立终端里调研思考，再把结论发到同一频道互相质疑碰撞，人看完全过程后拍板。

作者的总结：**Build less, achieve more**——写死的 workflow 与复杂编排状态机大多是在给当前模型的短板打补丁；一层足够薄的 IM 协作基础设施，会随模型能力变强不断产生复利。

## 适用边界

- 边界极其清晰、每天重复跑几百次的确定性流水线（定时拉数据、固定规则 webhook、CI 触发任务），传统脚本与 workflow 引擎仍是最稳妥的选择。
- 本文针对的是开放式软件工程与个人重度并行研发这类画不出固定流程图的场景。

## 和已有笔记的关联

- [[Grok Bot 配置 - 多Agent团队协同]]：boss / scout / writer / closer 四个 Bot 分工，是“固定分工方案”；Runner 的 Role / Crew / Mission 是更松的同一类思路——分工可复用，但协作方式不写死，适合对照着看。
- [[rex - Ghostty 作者的 Agent 终端]]：rex 的 CLI + skill 让 Agent 能在任意 tab（包括远程机器）里输入，是终端操作层；Runner 的 IM 是消息传递层。两者互补：rex 解决“Agent 怎么操作对方的终端”，Runner 解决“Agent 之间怎么传话、怎么不互相打断”。
- [[Google Antigravity 3个开源增强插件]]：其中的 dsh-godbot 是多 Agent 协同工作台；Runner 是同一赛道的另一种实现思路（本地 IM infra）。
- [[Codex 额度收紧 - GPT-6.2 Sol 与 Astra 消耗变快]]：Runner 的 Claude <=> Codex review loop 会额外消耗 Codex 额度，在额度收紧的当下值得注意——跑 review loop 前先在 Codex CLI 跑 `/status` 核对剩余额度，QA session 用低 effort 模型也是省额度的做法。

## 我的备注

- **可先试的最小做法**：不用先装 Runner，也可以先借用它的两个具体做法——(1) coder 与 reviewer 用不同模型 / 新 session 互相 review；(2) QA 用独立 session + 低 effort 模型，与 coding 上下文隔离。这两点在已有工具里就能试。
- **再决定要不要用 Runner**：等有一次真实的多任务并行需求时，再按它的 Role / Crew / Mission 搭一次，对比现在“多开终端手搓”的方式是否真的省了 review 时间。

## 备注

- **UNCONFIRMED，单一作者个案**：全文是一位开发者的个人经验分享（三个多月 dogfooding），未经独立验证；文中 orca / t3code / cmux / conductor 等产品的描述以作者当时的使用为准，未逐一核实。
- **作者身份**：帖子自称“没有硬广”，但文中介绍的 Runner 是作者自己的开源工具（https://github.com/yicheng47/runner，帖内截图均出自该仓库），阅读时按作者案例而非独立评测对待；本文未核实该仓库的当前状态、安装方式与定价。
- **同 digest 其他帖，未成篇**：kevinmissu 问“Gemini 用不了 Antigravity 是不是价值没那么大，连项目目录都加载不了”（2026-11-25 09:46，只有问题无解答）；AnQiYang 问“用 Google Play 安全订阅 Claude 的办法”（08:51，只有问题无解答）；Super8 问“把 Claude Code 智能体打包进业务系统”（08:35，只有问题无解答）；samhan 问“目前还有哪些好用的中转站”（09:02，digest 内只有标题无正文）。以上均为未解答的提问，不宜当作结论收录。
