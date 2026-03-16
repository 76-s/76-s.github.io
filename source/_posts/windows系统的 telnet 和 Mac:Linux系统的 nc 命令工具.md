---
title: windows系统的 telnet 和 Mac/Linux系统的 nc 命令工具
date: 2026-02-28 13:14:52
categories: 网络
tags:
- 网络
- telnet
- nc
---

##### 概述：

- telnet：使用 Telnet 协议（基于 TCP，标准端口 23）
- nc：默认使用 TCP 协议，但也支持 UDP 和其他协议



##### 使用场景：
测试端口连通性



##### 使用示例：
用 `telnet IP 80` 或 `nc IP 80` 去连接一个 Web 服务器，建立的就是普通的 TCP 连接，然后可以手动输入 HTTP 协议的内容。

> 建立底层连接后，后续的协议交互取决于连接的服务本身



##### 状态判定：
- 连接后立即显示或特定握手信息 → 说明服务正常运行
- 一直卡在 "Trying..." → 说明端口不可达或服务未启动
- 立即显示 "Connection refused" → 端口被拒绝连接

