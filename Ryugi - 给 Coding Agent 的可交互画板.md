---
title: "Ryugi - 给 Coding Agent 的可交互画板"
source: "V2EX 技术 2 小时 digest（2026-11-25 19:54 CST）；https://github.com/Innei/ryugi"
author: "tukon479"
created: 2026-11-25
published: 2026-11-25 11:12
tags:
  - AI-Agent
  - Claude-Code
  - Codex
  - Cursor
  - Antigravity
  - 开源工具
  - 多Agent协同
  - 设计评审
aliases:
  - Ryugi
  - ryugi-artifacts
---

# Ryugi - 给 Coding Agent 的可交互画板

> 来源：V2EX 用户 tukon479 于 2026-11-25 发表；项目仓库 https://github.com/Innei/ryugi
> 原帖讲的是作者用 [[Claude Code]] + Ryugi 做了一条 70 秒产品宣传片：分镜、录屏、配音、BGM 都由 Agent 做，人只看一遍、指着某个镜头说“这儿太拖了”让它改。

## 摘要

Ryugi（流儀）是给 Coding Agent 用的可交互 Artifacts 系统：Agent 不再只用三段 Markdown 描述三种布局让人脑补，而是直接把方案画出来——设计稿、文档、白板、PPT、视频分镜都能在页面上点、圈、评论，反馈自动回到 Agent 那边。作者的原话是：之前让 Agent 出设计，它甩来三段 Markdown 描述，剩下全靠脑补；现在直接画出来再改。

和已有笔记直接相关：[[Google Antigravity 3个开源增强插件]] 里 dsh-godbot 解决“多模型一起跑”，[[Agent 管理哲学 - 本地 IM 替代硬编排 Runner]] 解决“Agent 之间怎么传话”，Ryugi 解决的是另一头——“人怎么跟 Agent 对设计方案”：不用在终端里用文字描述“第三个布局左上角那个按钮”，直接在图上点、圈、评论。

## 它能画什么

| 类型 | 用来干什么 |
| --- | --- |
| Artboard（画板） | 多个方案并排画出来，标出推荐的那个，人挑一个、点评任意像素，提交后答案回传 Agent |
| Document（文档） | 设计文档里直接嵌 Agent 的问题，对着某一句话评论或原地改 |
| Whiteboard（白板） | 结构还没定型时，人和 Agent 一起画框、画箭头，圈出局部提问，答案落在图旁边 |
| HTML | 一个带真实滑杆的单页，用来调组件、字阶、动效，Agent 重写页面后滑杆值还在 |
| Slides | Agent 写 Deck，人直接上台讲；演讲者模式有备注和下一页，点某页就能改词、改样式 |
| Report | 图表来自真实数据的脚本输出，刷新就重跑；可以对着某一行、某一个数据点评论 |
| Storyboard | 宣传片/教程视频按镜头写分镜和旁白，先播一遍再剪；多轨时间线剪辑后导出 |
| Verify | 建好的页面和当初批过的设计并排摆，人点通过/驳回；还有一份核查报告，一行一个 Agent 靠实际运行验证过的主张，附原始输出，没验证的单独列出 |

评论、选择和通过/驳回 verdict 都会直接回到当初生成页面的那个 Agent session。[[Claude Code]] 里不用人再发消息，Agent 会自己改、回复在评论下面，页面边看边更新。

## 安装与支持的工具

- **免费、本地跑**：Artifacts 存在自己的项目里，从本地服务器打开，不需要账号；只在想分享链接时才发布到 Ryugi Cloud。
- **安装**：`npx ryugi-artifacts install`，向导会让选装一个或多个 harness，再选只装 Artifacts 还是装完整工作流（完整工作流会再加 brainstorm、spec、TDD 构建、verify 的 skills）。
- **支持**：[[Claude Code]]、[[Codex]]、Cursor、Gemini CLI、OpenCode、Copilot CLI、Pi、[[Antigravity]]、Kimi Code、Qwen Code、Devin、Droid 等。
- **Antigravity 装法**（仓库 README 给的）：`agy plugin install https://github.com/Innei/ryugi`
- **要求**：Node.js 20.12 或更新。仓库还要求在 Claude Code 设置里关掉官方托管 Artifacts（`~/.claude/settings.json` 加 `"disableArtifact": true`），只留 Ryugi 的 Artboard。

仓库状态（2026-11-25 20:20 核实）：公开仓库，创建于 2026-10-19，当前约 916 stars、42 forks、674 commits。作者 Innei。

## 我的备注

- **可先试的最小做法**：不用先装完整工作流，只装 Artifacts，下次让 Agent 出设计/改页面时说“画三个方向让我挑”，看一次点评回传是不是真的省了来回描述的时间；好用再考虑完整工作流。
- **和已有工具对照**：如果平时主要在 [[Antigravity]] 里工作，先按上面那条 `agy plugin install` 装；如果主力是 Claude Code / Codex，用 `npx ryugi-artifacts install` 走向导。
- **许可证先看一眼再进团队流程**：仓库 README 自述免费本地，第三方介绍指出 Ryugi 用的是 Ryugi License 1.0（source-available，非 MIT），npm 的 artifacts 应用是专有的；个人试用没问题，放进公司流程前先读一下 LICENSE。

## 备注

- **UNCONFIRMED，单一作者个案**：70 秒 4K 宣传片是发帖人自己的说法，帖内两张截图未逐项核实，配音/BGM 质量、剪辑耗时都没给可复现的细节；按作者案例而非独立评测对待。
- **同 digest 其他帖，未成篇**：
  - xiaoyaner0201（2026-11-25 11:41）：在家里先搭一遍公司的整套 AI 架构当灰度环境——一台 NAS、一台显卡机加几台 Mac，分基础设施（代码仓库/CI/镜像/容器/监控追踪，全走 Docker、单一部署入口）、AI 中台（模型网关、Agent 编排、调用记录与评测、统一身份权限与 Key 管理）、业务交付（Agent 拆卡，开发/评审/测试多 Agent 协作提 MR，再走自动检查 → 合并队列 → 打版本 → 构建镜像 → 定向部署 → 灰度验证 → 全量）三层；公司同事报的任务排队、长任务续聊失败、偶发 401、内存打爆等问题，先在家里用脱敏重写的用例复现、A/B 对比、修好再推回公司，代码只从家里流向公司。作者自己留了三个没想清楚的问题：规模差一两个数量级时家里复现不了的问题怎么办、脱敏用例会不会偏离真实问题、整套先搭一遍是不是太重。值得和 [[Grok Bot 配置 - 多Agent团队协同]]、[[Agent 管理哲学 - 本地 IM 替代硬编排 Runner]] 对照着看，但这是一篇求拍砖的讨论帖，不是工具评测。
  - nanxfu（2026-11-25 10:24）：开源桌面端 AI 创作工作台 AIGC Studio，本地显卡、Kaggle / Colab 免费 GPU、按秒租卡、商业 API 四种算力可切，集成了局部重绘、高清放大、像素对齐、序列帧切片、SVG 转换、背景移除和 PBR 材质拆解，仓库 https://github.com/nanxfu/aigc-studio 。和主线 AI 编程工具关系较远，未单独成篇。
