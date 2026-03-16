---
title: windows系统的 tracert 和 MAC/Linux系统的 traceroute 命令工具
date: 2026-02-28 13:14:52
categories: 网络
tags:
- 网络
- tracert
- traceroute
---

##### 概述：
网络诊断工具，追踪数据包从本地计算机到目标计算机所经过的路径，通过发送一系列 ICMP 回显请求（或其他协议的数据包），逐渐增加 TTL（Time To Live）值，从而探测路径上的每一跳路由器。



##### 使用场景：
检查设备的网络通信路径



##### 使用方式：
tracers IP/域名 或 traceroute IP/域名



##### 状态判定：
- 成功跟踪后会返回路径上的设备 IP；

- *** 并非网络中断，而是中间设备拒绝响应 traceroute 的探测包，原因可能是：
  1. 安全策略：企业级路由器和运营商骨干设备常配置 ACL，阻止 ICMP 消息出站以隐藏网络拓扑
  2. ICMP限流：路由器为了防止 DDoS 攻击或过载，会对 ICMP Time Exceeded 消息进行限速甚至丢弃
