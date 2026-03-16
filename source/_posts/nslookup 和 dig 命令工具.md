---
title: nslookup 和 dig
date: 2026-02-28 13:14:52
categories: 网络
tags:
- 网络
- DNS
---



##### 概述

标准DNS查询工具

| 维度     | nslookup               | dig                  |
| :------- | :--------------------- | :------------------- |
| 主要场景 | 快速查询、基础故障排查 | 深度分析、自动化脚本 |
| 功能侧重 | 易用性                 | 详细性和可编程性     |
| 跨平台   | Windows/Linux/Mac      | Linux/Mac            |



##### 示例

```bash
byte76@76deMacBook-Air ~ % nslookup baidu.com
Server:		8.8.8.8		# 使用的DNS服务器
Address:	8.8.8.8#53		# 服务器地址和端口

Non-authoritative answer:		# 非权威回答（来自缓存）
Name:	baidu.com		# 查询的域名
Address: 124.237.177.164		# IP地址
Name:	baidu.com
Address: 110.242.74.102
Name:	baidu.com
Address: 111.63.65.103
Name:	baidu.com
Address: 111.63.65.247

byte76@76deMacBook-Air ~ %
byte76@76deMacBook-Air ~ % dig baidu.com

; <<>> DiG 9.10.6 <<>> baidu.com	# dig 版本和查询命令
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 7640	# 响应状态
;; flags: qr rd ra; QUERY: 1, ANSWER: 4, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:	
; EDNS: version: 0, flags:; udp: 512
;; QUESTION SECTION:	# 问题部分
;baidu.com.			IN	A

;; ANSWER SECTION:	# 答案部分
baidu.com.		73	IN	A	110.242.74.102
baidu.com.		73	IN	A	111.63.65.103
baidu.com.		73	IN	A	124.237.177.164
baidu.com.		73	IN	A	111.63.65.247

;; Query time: 81 msec	 # 查询耗时
;; SERVER: 8.8.8.8#53(8.8.8.8)	# 使用的DNS服务器
;; WHEN: Sat Feb 28 13:14:51 CST 2026	# 查询时间
;; MSG SIZE  rcvd: 102

byte76@76deMacBook-Air ~ %
```



##### 工作原理

```plaintext
应用层逻辑
    ↓
构造DNS查询报文（RFC 1035标准）
    ↓
套接字编程（Socket）
    ↓
UDP/TCP传输层（端口53）
    ↓
IP网络层
    ↓
DNS服务器
```
