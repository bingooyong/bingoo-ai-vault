---
title: Claude Code 2.1.332 - MCP 服务器 Skills 进入 Beta
date: 2026-11-26
tags:
  - AI编程工具
  - Claude-Code
  - MCP
  - Skills
  - Antigravity
source:
  - https://vibestacklab.com/new#antigravity-cli-1-5-15
  - https://www.havoptic.com/compare/antigravity-cli-vs-claude-code
  - https://explainx.ai/blog/topics/anthropic
related:
  - "[[Google Antigravity 3个开源增强插件]]"
  - "[[Codex 额度收紧 - GPT-6.2 Sol 与 Astra 消耗变快]]"
  - "[[Grok Bot 配置 - 多Agent团队协同]]"
---

# Claude Code 2.1.332 - MCP 服务器 Skills 进入 Beta

## 要点

- Anthropic 于 **2026 年 11 月 25 日 01:32 UTC** 发布 Claude Code **v2.1.332**，正式把 **Skills from MCP servers** 带入 Beta，同时新增 `PreSkillShellCommand` hook，并修复一批冻结与模型回退问题。
- 同一天 Google 发布 **Antigravity CLI 1.5.17**：提示框内可点选文字、长命令完成提醒、登录 token 刷新更顺滑。Antigravity 1.5.16（11 月 24 日）刚加了 `~/.agents/skills` 跨工具 skills 支持——两边在同一周往同一个方向走：**skills 正在变成跨工具的通用能力层**。
- 背景数据：Anthropic 在 11 月 24 日纪念 MCP 两周年，称 MCP SDK 月下载量超 **7 亿次**、Claude 已有 **10,000+ connectors**，新协议特性包括服务器直接分发 skills、后台任务、暂停等待用户输入。

## 为什么值得关注

1. **MCP 服务器现在可以直接给 Agent 发 skills**，不再需要每个工具各自维护一份 skills 目录。对同时用 Claude Code 和 Antigravity 的人来说，同一套 skills 有望在两边复用，减少重复配置。
2. **Antigravity 1.5.16 已支持读取 `~/.agents/skills`**，说明 Google 也在接这个约定；可以把常用 skills 先放到这个共享目录，再观察两边兼容到什么程度。
3. **MCP 生态规模（7 亿月下载）意味着 skills over MCP 会是后续各家跟进的默认路径**，现在投入配置不亏，但仍是 Beta，遇到行为异常先降级回本地 skills。

## 避坑 / 待确认

- MCP server skills 仍是 **Beta**，生产关键流程先在小项目试，别直接替换已稳定的本地 skills。
- `PreSkillShellCommand` hook 是新能力，配置不当可能让 hook 频繁触发；先只读观察。
- Antigravity CLI 1.5.17 的改动偏体验优化，没有模型与额度变化，不用为升级它调整订阅。

## 相关动态（同一批）

- Claude Code 2.1.331（11 月 23 日）：Code Mode 登陆 Bedrock、Google Cloud、Microsoft Foundry。
- Antigravity CLI 1.5.15（11 月 23 日）：自动压缩档位（800K / 0.5M / 80%）、`/context` 实时查看。
