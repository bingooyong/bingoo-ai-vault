---
title: "Antigravity 2.34.0 实时清单与规划执行分离 - 额度全量重置与 Pro 得 Carbon"
source: "TestingCatalog 2026-11-24 报道，经 AI Insiders 2026-11-26 转述；Antigravity CLI 1.5.18 GitHub release 2026-11-26；Antigravity 2.35.2 changelog 2026-11-25"
created: 2026-11-27
published: 2026-11-27
tags:
  - Google-Antigravity
  - Gemini
  - 额度
  - 模型更新
aliases:
  - 2026-11-27 Antigravity 2.34.0 清单与额度重置
---

# Antigravity 2.34.0 实时清单与规划执行分离 - 额度全量重置与 Pro 得 Carbon

> 来源：TestingCatalog 2026-11-24 报道（AI Insiders 2026-11-26 转述，含 Antigravity 团队成员 Rohan Phadte、Varun Mohan 的说法）；Antigravity CLI 1.5.18（2026-11-26 05:52 UTC 发布）；Antigravity 应用 v2.35.2（2026-11-25）。以下为第三方报道转述，非 Google 官方公告；额度与模型可用性因账号、地区、灰度而异，以自己账号实际显示为准。
> 关联笔记：[[Antigravity 额度重置与地区限制 - 2026-11-26 跟进]]、[[Gemini Pro 黑五 99 美元年付 - 手机 App 升级实测与 11 月 27 日新用户窗口]]、[[Neon 模型实测 - 可替代 Astra 做游戏开发主力]]、[[Gemini Pro 订阅地区与支付避坑 - Antigravity 缺 Opus 5.5 与 4.99 美元试用升级冲突]]

## 摘要

Antigravity 2.34.0 带来两项工作流变化：**长任务实时清单**（每一步列出来，并行步骤会标注）和**规划/执行模型分离**（Orchestrator Settings 里分别为规划模型和执行模型指定模型与 effort 等级，规划负责设计与复核，执行负责搜索、编辑、构建与测试；平台负责人 Varun Mohan 自己用 Gemini 4 Neon 做规划、Gemini 4 Hydrogen 做执行）。**额度方面 Google 给所有人重置了 Gemini 额度**，付费 Pro 用户陆续获得 Gemini 4 Carbon，Hydrogen 向所有用户开放，Pro 原先独立的 Argon 额度桶并入通用 Gemini 池；“攒着的重置次数”（banked resets）还在设计中。这与 11 月 26 日 V2EX 上多条“额度只用到约 20% 就被重置”的个案相互印证——看起来是平台级统一重置，而非个别账号异常。

## 1. 实时清单（2.34.0）

- 长任务执行时显示清单，列出每一步、标注哪些步骤并行跑。
- Antigravity 团队成员 Rohan Phadte 称 2.34.0 会把清单推给所有用户。
- 实用点：多 agent 并行时能直接看到哪一步卡住、哪几步在并行，比以前只看日志好定位问题。

## 2. 规划模型与执行模型分开设（Orchestrator Settings）

- 新设置面板：规划模型（planner）和执行模型（executor）分别指定，effort 等级也可分别设。
- 分工：planner 负责设计方案与复核，executor 负责搜索、编辑、构建、测试。
- 参考搭配：Varun Mohan 用 Gemini 4 Neon 做 planner、Gemini 4 Hydrogen 做 executor。
- 注意：报道未给出任何 token 消耗数据，所谓“更省 token”目前只是说法——建议在自己的任务上实测几组搭配再定，别直接照搬。

## 3. 额度全量重置与模型开放

- Google 为所有人重置了 Gemini 额度（rollout 进行中，有人先看到、有人后看到）。
- 付费 Pro 用户获得 Gemini 4 Carbon；Hydrogen 向所有用户开放。
- Pro 原先独立的 Argon 额度桶取消，容量并入通用 Gemini 池。
- “banked resets”（攒重置次数）仍在设计中，未上线。
- 对照昨日个案：11 月 26 日 V2EX 有用户称周额度只用到约 20% 就被重置、有人称本月第六次重置——结合本次报道，应为平台统一重置动作的一部分，而非账号风控；但重置后新额度的具体量、是否按周/按月累计，报道未说明，仍需以自己账号为准。

## 4. 同期小版本

- **Antigravity CLI 1.5.18**（2026-11-26 05:52 UTC）：修复半写入图片被拒绝的问题；涉及 Remote Control 浏览器视图与图片附件用户。
- **Antigravity 2.35.2**（2026-11-25）：支持在一次对话中途切换模型。
- 前一天的 CLI 1.5.17（2026-11-25）：提示框选词、登录刷新优化。

## 相关链接

- AI Insiders《Antigravity adds live checklists and a split planner and executor setting》（TestingCatalog 2026-11-24）：https://talent.aiinsiders.net/article/antigravity-adds-live-checklists-and-a-split-planner-and
- Vibestacklab Radar（Antigravity CLI 1.5.18 / 2.35.2 发布记录，2026-11-26）：https://vibestacklab.com/new#antigravity-cli-1-5-15
