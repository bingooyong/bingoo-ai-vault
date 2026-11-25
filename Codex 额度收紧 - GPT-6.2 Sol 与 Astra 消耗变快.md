---
title: "Codex 额度收紧 - GPT-6.2 Sol 与 Astra 消耗变快"
source: "V2EX 技术 digest（Blogtrottr，2026-11-25 03:54 UTC）"
author: "qwertooo"
created: 2026-11-25
published: 2026-11-25 03:15
tags:
  - Codex
  - OpenAI
  - GPT-6.2
  - 额度
  - AI工具
  - 代码模型
aliases:
  - Codex 额度扣费修复
---

# Codex 额度收紧 - GPT-6.2 Sol 与 Astra 消耗变快

> 来源：V2EX 用户 qwertooo 于 2026-11-25 03:15 发表于 V2EX 技术板块，经 Blogtrottr 邮件 digest（2026-11-25 03:54 UTC）收到。以下为单人转述，未经官方核实：作者称刚收到 OpenAI 邮件，说 OpenAI 修复了 Codex 的额度扣费漏洞，修复后 GPT-6.2 Sol 和 Astra 的额度会比之前消耗得更快。邮件截图见 https://i.imgur.com/zLVaxmJ.png ，本篇未逐字核对截图内容，也没有消耗变快的具体倍数。
> 与本文相关的已有笔记：[[Neon 模型实测 - 可替代 Astra 做游戏开发主力]]、[[Gemini Pro 购买避坑 - 订阅币种与账号地区]]

## 摘要

如果该说法属实，Codex 上用 GPT-6.2 Sol / Astra 的“性价比”会下降：同样的任务更快耗尽 5 小时窗口或周额度。这直接影响之前笔记里的对比——[[Neon 模型实测 - 可替代 Astra 做游戏开发主力]] 里 Neon 被认为能替代 Astra 做主力，而那位作者“几天都没碰 Codex 了”；额度收紧会让 Codex 相对更不划算，但**这仍只是一条未证实的消息**，在看到官方说明或自己实测 `/status` 之前，不要据此改订阅。

## 已知信息

- **信源**：V2EX 单条帖子（qwertooo，2026-11-25 03:15），称 OpenAI 发邮件通知修复了 Codex 额度扣费漏洞。
- **受影响模型**：GPT-6.2 Sol、GPT-6.2 Astra（帖子原话如此）。
- **变化方向**：修复后这两个模型的额度消耗比之前更快；**没有给出具体倍数、生效时间或适用套餐**。
- **证据**：帖子附了一张疑似邮件截图 https://i.imgur.com/zLVaxmJ.png ，digest 未提供可核对的文字版。

## 背景：Codex 额度本来就是按 token 计、按模型分档

以下为 2026-11-25 网上检索到的二手汇总（引用 OpenAI 定价页 / 帮助中心，非 OpenAI 原文直接核对，仅作背景参考）：

- Plus（$20/月）5 小时窗口内，GPT-6.2 Sol 约 17–180 条消息，GPT-6.2 Astra 约 6–50 条；Astra 本来就比 Sol 贵得多、额度少得多。
- 额度按 token 计，不是按条数；Fast 模式用 2 倍额度，Ultrafast 用 6 倍。
- 从 2026 年 12 月起，Plus / Pro / Business 上 ChatGPT 与 Codex 将共用一个按 token 计的额度，不再各算各的。
- OpenAI 今年已多次调整 Codex 额度（7 月临时取消 5 小时限制、8 月恢复、9–10 月 Pro 额度先缩后扩），所以“又一次调整”本身并不意外。

## 注意：不要和 Grok Bot 笔记里的 sol 混淆

[[Grok Bot 配置 - 多Agent团队协同]] 里的 sol 是 Grok Bot 团队里“负责思考”的 Bot 角色，和这里 OpenAI 的 GPT-6.2 Sol 模型**不是同一个东西**，只是同名。

## 建议

- 在 Codex CLI 里跑 `/status` 看自己账号的剩余额度和重置时间，再决定是否换模型或降级使用 Sol / Luna。
- 如果最近 Codex 额度明显比以前不耐用，先对照这条消息，不要立刻升级 Pro——等官方说明或更多人复现再判断。
- 和 [[Gemini Pro 购买避坑 - 订阅币种与账号地区]] 一起看：$99/年 Gemini Pro 的决策本来就悬在“先拿真实任务试 Neon”上，Codex 额度收紧如果是真的，会让 Neon / Gemini 这条路相对更有吸引力，但同样需要实测确认。

## 未确认项

- [ ] OpenAI 邮件原文与生效时间（截图未逐字核对）
- [ ] 消耗变快的具体幅度（帖子未给数字）
- [ ] 是否只影响 Plus，还是 Pro / Business 也受影响
