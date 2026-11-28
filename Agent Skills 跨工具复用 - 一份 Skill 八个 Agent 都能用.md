---
title: "Agent Skills 跨工具复用 - 一份 Skill，八个 Agent 都能用"
source: "enjoy.dev《How to use Claude Code skills in Codex and other agents in 2026: 6 ways compared》，2026-11-19 对照各家官方文档核查；Threads @petarivanovv9 2026-11-27 帖子（10 个 Skill 仓库清单，星数为发帖人所称）；Threads @tenten.co 2026-11-26 转述 Boris Cherny"
created: 2026-11-28
published: 2026-11-28
tags:
  - Agent-Skills
  - Claude-Code
  - Codex
  - Antigravity
  - 多Agent
  - 知识库
aliases:
  - Skills 跨 Agent 复用
---

# Agent Skills 跨工具复用 - 一份 Skill，八个 Agent 都能用

> 来源说明：核心方法是 enjoy.dev 2026-11-19 对照 Claude Code、Codex、Antigravity、Cursor、GitHub Copilot、OpenCode、Muse Code、Grok Build 八家官方文档逐项核查后的结论，不是单篇博客的猜测。Threads 上的清单和 Cherny 规则是网友转述/个案，星数和说法只当方向看。
> 相关已有笔记：[[X 实战合集 - Agent Skills 与 Agent 工作流（2026-11-27）]]、[[Claude Code 2.1.332 - MCP 服务器 Skills 进入 Beta]]、[[Google Antigravity 3个开源增强插件]]

## 摘要

Skill 本质上就是一个文件夹 + `SKILL.md`，八个 Agent 认的是同一套开放标准，差别只在**文件夹位置**。Claude Code 只认 `.claude/skills`，Codex 和 Antigravity 只认 `.agents/skills`，其余五家两边都读。所以不用每个 Agent 各维护一份：把 Skill 只存一份在 `.agents/skills`，再软链进 `.claude/skills`，八个 Agent 就全能用上。写 Skill 时只用标准字段（`name` + `description`），别依赖 Claude Code 专属写法，否则换个 Agent 就失效。

## 1. 八个 Agent 各从哪读 Skill（2026-11-19 核查）

| Agent | 读 `.claude/skills` | 读 `.agents/skills` | 自己的文件夹 |
| --- | --- | --- | --- |
| Claude Code | 是 | 官方文档没写 | `~/.claude/skills` |
| Codex | 官方文档没写 | 是（从当前目录一路扫到仓库根） | `~/.agents/skills` |
| Antigravity | 官方文档没写 | 是（扫到仓库根） | `~/.gemini/config/skills`；CLI 还读 `~/.gemini/antigravity-cli/skills/` 和 `~/.gemini/skills/` |
| Cursor | 是（兼容） | 是 | `.cursor/skills`、`~/.cursor/skills` |
| GitHub Copilot | 是 | 是 | `.github/skills`、`~/.copilot/skills` |
| OpenCode | 是（兼容） | 是 | `.opencode/skills` |
| Muse Code | 是 | 是 | `$XDG_CONFIG_HOME/muse/skills` |
| Grok Build | 是 | 是（每层都读） | `.grok/skills`、`~/.grok/skills` |

坑：Antigravity App 不读它 CLI 读的那两个文件夹；Muse Code 的项目 Skill 要先信任工作区才加载；Grok Build 一个 Skill 正文最多内联前 25,000 个 token，太长的 Skill 会被截。

## 2. 做法：只存一份，软链过去（免费，推荐）

```bash
mkdir -p .agents/skills
git mv .claude/skills/review .agents/skills/review
ln -s ../../.agents/skills/review .claude/skills/review
```

- `review` 换成你自己的 Skill 文件夹名。Claude Code 官方文档确认：Skill 条目可以是指向别处目录的软链，多个位置指向同一目标也只加载一次。
- 个人 Skill（所有项目通用）最接近共享位置的是 `~/.agents/skills`：Codex 直接读；Claude Code 读 `~/.claude/skills`、Antigravity 读 `~/.gemini/config/skills`，同样用软链打通。
- Windows 上提交的软链要开 Git 的 `core.symlinks`，否则检出后会变成普通文本文件，Skill 就失效了。

## 3. 写可移植 Skill 的三条规矩

1. **只用标准字段**：`name` 不超过 64 个小写字母/数字/连字符，且必须和文件夹同名；`description` 不超过 1,024 字符，写清楚“这个 Skill 干什么、什么时候用”——Codex 等 Agent 就是靠这句话决定要不要加载它。
2. **别用 Claude Code 专属字段**：`context: fork`（在子 Agent 里跑 Skill）、调用控制、动态上下文注入这些只在 Claude Code 生效，Anthropic 自己也说了“出了 Claude Code，只能用标准里的字段”。
3. **描述别写太长**：Claude Code 的 Skill 列表里描述会被截到 1,536 字符，关键信息放前面。

## 4. 其他五种同步方式，怎么选

| 方式 | 适合谁 | 代价 | 坑 |
| --- | --- | --- | --- |
| 软链（第 2 节） | 多 Agent 共用一个项目 | 免费 | 见第 2 节 Windows 坑 |
| `/import` 一次性导入 | 彻底换 Agent，不再两边跑 | 免费 | 导入后是第二份拷贝，改了原 Skill 不会同步；只有 ChatGPT 桌面 App 能开自动更新 |
| skillshare | 个人 Skill 想进所有 Agent | 免费开源 | 79 个目标，不含 Muse Code；每加一个 Skill 要再跑一次同步 |
| Vercel skills CLI | 装别人发布的 Skill 到所有 Agent | 免费开源，需 Node.js 22.20+ | 用来装 Skill，不是同步你自己的；会收集匿名使用数据（`DISABLE_TELEMETRY=1` 可关） |
| Rulesync | 团队把规则、MCP、Skill 放同一源 | 免费开源 | 写的是拷贝不是软链，改完源要重新生成或开 `--watch` |
| Enjoy（桌面 App） | 在一个 App 里跑多家 Agent | 月付，7 天免费试用 | 借给别的 Agent 的 Skill 只跟当次消息走；Agent 自己挑 Skill 还是要靠第 2 节的文件夹 |

补充：Codex 里两个同名 Skill 不会合并；Cursor、Copilot、Muse Code 读到两份同名 Skill 时官方文档没说怎么处理，别在两个文件夹各放一份同名 Skill。

## 5. 延伸：去哪找现成的 Skill（2026-11-27 网友清单，未逐项核实）

- Threads @petarivanovv9 2026-11-27 贴了一张“10 个 coding agent Skill 仓库”图（星数为发帖人所称，合计号称超 100 万）：Agent Skills 规范（27K）、Anthropic Skills（196K）、Superpowers by obra（319K）、Matt Pocock 的 Skills for Real Engineers（326K）、Addy Osmani 的 agent-skills（119K，`npx skills add addyosmani/agent-skills` 一条命令装 25 个 Skill）、Vercel Agent Skills（34K）、Trail of Bits Skills（8.2K，偏安全）、Vercel skills CLI（40K）。图里建议的入门顺序是 01 → 02 → 09（规范 → Anthropic 官方 → skills CLI），并提醒“Skill 就是依赖，装之前像审代码一样读一遍”。
- 同一天 Threads @itsleogrundstrom 的视频介绍了 ECC（affaan-m/ECC）：自称 72 个 Agent、328 个 Skill，装法是 `/plugin marketplace add https://github.com/affaan-m/ECC` 再 `/plugin install ecc@ecc`，走“先计划、等确认、测试先行、再 Code Review”的流程。发帖人自称的星数（30 万+）未核实，先当个案看，装前读 SKILL.md。
- Boris Cherny 的一条旧规则最近又被翻出来（Threads @tenten.co 2026-11-26 转述）：Claude Code 团队升级模型后做了消融测试，删掉了约 80% 的系统提示——很多规则是给旧模型打的补丁，新模型反而会被它束缚。建议每半年左右停用并重测一次旧的 CLAUDE.md、Skill 和 Hook，通用 Agent 底座 + 可移植 Skill，别为每个工作流硬编码专属 Agent；第三方 Skill 里的脚本装前要审计。

## 怎么做（下一步）

1. 把你现在 `.claude/skills` 里的 Skill 用第 2 节命令挪到 `.agents/skills` 并软链回来，先在 Claude Code 和 Codex 各跑一次确认都能触发。
2. 新 Skill 一律只写标准字段，`description` 第一句就写“什么时候用”。
3. 想装现成 Skill，先从 Addy Osmani 那 25 个里挑一两个读完 SKILL.md 再装，别整包照单全收。
