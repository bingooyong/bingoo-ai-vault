---
title: "rex - Ghostty 作者的 Agent 终端"
source: "V2EX 技术 2 小时 digest（2026-11-24），帖子引用了更早的下载方式讨论 https://www.v2ex.com/t/1251516"
author: "oopc"
created: 2026-11-25
published: 2026-11-24 12:01
tags:
  - rex
  - Ghostty
  - AI工具
  - 终端
  - 多Agent
  - Tailscale
aliases:
  - rex 终端体验
---

# rex - Ghostty 作者的 Agent 终端

> 来源：V2EX 用户 oopc 于 2026-11-24 发表，重度体验分享；另参考了 Superlogical 公开 beta 的相关介绍
> 原帖见 2026-11-24 的 V2EX 技术 digest，帖内提到下载方式见更早的讨论 https://www.v2ex.com/t/1251516；本篇未从原帖拿到直接链接，以 digest 内容为准

## 摘要

rex 是 Ghostty 作者 Mitchell Hashimoto 在 Superlogical 做的新产品：一个终端软件，本身就是终端 + 多路复用器（不是跑在别的终端里的 tmux），内置 Tailscale、可跨设备连远程 rex，并提供 CLI + skill，让一个 Agent 能在任意 tab（包括远程机器上的）里输入、让多个 Agent 之间互相传话。作者用了一个月，已把它当作默认 vibe 环境。

和之前两篇笔记直接相关：[[Google Antigravity 3个开源增强插件]] 里的 dsh-godbot 讲多 Agent 协同、[[Grok Bot 配置 - 多Agent团队协同]] 讲 Bot 分工，rex 则是底层的终端载体——Agent 不只是在各自窗口里跑，还能互相操作对方的终端。

## 核心能力

### 1. 终端本身：替代 iTerm2 / Ghostty

- 终端 + 会话持久化合一，关掉窗口再打开能回到原来的会话
- 用 libghostty 做渲染
- 配置项大约只有 orca 的十分之一，作者认为“完全没有心智负担”

### 2. 内置 Tailscale，跨设备连远程 rex

- 作为 server 暴露成服务，作为 client 连接远程 rex
- client 端需要 Tailscale VPN；局域网内也支持 SSH 连接 rex server
- Linux / Windows 目前只能作为 server；iOS 只能作为 client（需 TestFlight）

### 3. CLI + skill：Agent 能操作任意 tab

- 提供 CLI 和 skill，让 Agent 能在任意 tab（包括连接的远程 rex）中输入，打字、快捷键都没问题
- `rex skill setup` 可给 [[Claude Code]]、Codex、OpenCode、pi 等 Agent 安装 skill，让它们通过 CLI 开 tab、分屏、读屏
- OSC 7501 协议让 Agent 上报状态（工作中 / 被阻塞 / 完成 / 失败），未聚焦的 tab 也能看到状态

## 作者给出的用法示例

1. 在手机上打开 mac1 中的 [[Claude Code]]
2. 让 Claude Code 通过 rex CLI 在另一台 mac2 上的 opencode 中问一个问题
3. 让 opencode 将问题答案通过 rex CLI 回写到 Claude Code 的输入框

一句话：任意 Agent 之间能随便对话。

## rex vs orca（作者的对比）

|  | rex | orca |
| --- | --- | --- |
| 形态 | TUI | GUI，也支持直接展示 Agent 的 TUI |
| 远程连接 | 依赖 Tailscale | 内置 orca relay，需要登录 |
| mac | 全功能 | 全功能 |
| linux | 只能作为 server | 有，没试过 |
| win | 只能作为 server | 有，没试过 |
| ios | 只能作为 client，需 TestFlight | 有，没怎么用 |
| 编辑器 | zed | 内置 |
| 浏览器 | chrome-devtool-mcp | 内置 |
| AI 订阅管理 | magpie | 内置 |

作者的取舍：不喜欢 orca 一些“无意义的功能”和 all in one 的做法（比如 orca bot 选一个 Agent 当底座、自定义人设，“我直接启动个 Claude Code 有啥区别”），rex 更贴合他已有的技术选型。

## 已知痛点与不确定处

- **手机上连 Tailscale 就连不了 qx**：作者在帖子里催 qx 快点支持 Tailscale
- **iOS TestFlight 构建已过期**：官方原话是 "Apple just expired all of our builds for external testing, we've opened a support case, but have no idea why."——iOS 端目前可能装不上，等官方处理
- **Beta 状态说法不一**：有介绍称 2026 年 10 月 5 日起公开测试，也有介绍称仍在候补名单、仅约 50 人内测；以 Superlogical 官方最新口径为准，本文未核实
- **免费与否**：有介绍称 rex 免费且将一直免费，也有称“目前免费、定价未定”；同样以官方口径为准
- 本篇主要是一位 V2EX 用户的重度使用个案，未经独立验证；是否替代现有终端，建议先在主力机以外试几天再决定

## 和已有笔记的关联

- [[Google Antigravity 3个开源增强插件]]：dsh-godbot 做多 Agent 协同工作台；rex 是让 Agent 互相操作终端的底层工具
- [[Grok Bot 配置 - 多Agent团队协同]]：boss / scout / writer / closer 四个 Bot 分工；rex 的 CLI 可以成为这类多 Bot 协作的执行通道
- [[Neon 模型实测 - 可替代 Astra 做游戏开发主力]]：模型层面的主力选择；rex 是终端/执行层面的选择，两者不冲突
