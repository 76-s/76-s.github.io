---
title: 虚拟机vs容器
date: 2026-03-15 13:14:52
categories: 云计算
tags:
- virtual
- 虚拟机
- 容器
---

虚拟机：在硬件层面虚拟化，每个实例运行完整操作系统和虚拟内核。
容器：在操作系统层面虚拟化，多个实例使用 namespaces、cgroups 等机制实现进程与资源隔离。

```
虚拟机（VM）
────────────────────────
物理硬件
  │
Hypervisor（Type 1 或 Type 2）
  │
├─ VM1
│   ├─ Guest OS（自己的内核）
│   └─ App
│
├─ VM2
│   ├─ Guest OS（自己的内核）
│   └─ App
│
└─ VM3
    ├─ Guest OS（自己的内核）
    └─ App


容器（Container）
────────────────────────
物理硬件
  │
主机操作系统（宿主机内核）
  │
容器引擎/运行时
  │
├─ Container1
│   └─ App + Libs
│
├─ Container2
│   └─ App + Libs
│
└─ Container3
    └─ App + Libs
```



总结：容器通常更轻量、启动更快、资源利用率更高；而虚拟机通常隔离性更强、兼容性更好，也更适合运行和宿主机不同的操作系统。
