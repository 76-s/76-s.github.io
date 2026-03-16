---
title: HUAWEI-S-VLAN-01
date: 26-02-19 13:14:52
categories: 网络
tags: [vlan,交换机,华为]
---



华为交换机的操作系统叫做 **VRP** (Versatile Routing Platform)。主要有4个核心模式（视图）层层递进。

1. 用户视图

   - 标志：`<Huawei>` 
   - 状态：刚通过 Console 线或 SSH 登录进去后的默认状态
   - 权限：只能做一些简单的“看”的操作，比如查看运行状态、Ping、Telnet

2. 系统视图

   - 标志：`[Huawei]` 
   - 状态：相当于“管理员模式”，可以进行全局配置（改设备名、创建 VLAN 等）
   - 如何进入：在用户视图输入 `system-view` (简写 `sys`)

3. 接口视图

   - 标志：`[Huawei-GigabitEthernet0/0/1]` 
   - 状态：针对某一个具体的网口进行配置（如开启关闭端口、配置 Access/Trunk）
   - 如何进入： 在系统视图输入 `interface 接口类型 接口编号` (例如 `int g0/0/1`)

4. 协议/特定视图

   - 标志：`[Huawei-ospf-1]` 或 `[Huawei-vlan10]` 
   - 状态：进入具体的协议（如 OSPF、BGP）或功能（如 VLAN、User-interface）内部

   - 如何进入：在系统视图输入协议名或功能名

---

VLAN

1. 配置PC的IP地址，并测试连通性是否正常

2. VLAN的划分

   1. 创建vlan

      ```
      system-view  #进入系统视图
      vlan 10  #创建VLAN 10并进入VLAN视图
      quit  # 退回到系统视图
      ```

   2. 把接口划入到对应vlan

      ```
      interface Ethernet 0/0/1  #进入具体接口视图
      port link-type access  #设置链路类型为 Access
      port default vlan 10  #将接口加入 VLAN 10
      quit  #配置完成，返回系统视图
      display vlan  #查看所有 VLAN 及包含的端口
      display interface Ethernet 0/0/1  #查看具体某个接口的状态
      ```

> 华为交换机的配置是即时生效在内存里的，如果现在断电就白干了。需要在用户视图中输入 save 并确认。



3. 进阶技巧

   ```
   #查看帮助
   vlan ？
   
   #批量创建vlan
   vlan batch 2 to 6
   
   #如果有10个接口都要划入VLAN 10，一个一个敲太慢了，可以用 端口组
   port-group testgroup
   group-member Ethernet 0/0/1 to Ethernet 0/0/10
   port link-type access
   port default vlan 10
   
   #查看接口已有配置
   display this
   # 查看所有端口组
   display port-group all
   # 查看VLAN摘要
   display vlan summary
   # 查看端口VLAN信息
   display port vlan
   ```

> 华为交换机有一个特性：执行了 `undo vlan 10` ，虽然接口不再属于VLAN 10了，但接口里的 `port link-type access` 这一行配置可能还会留在那里。需要在接口视图输入 `undo port link-type` 取消。
>
> 清除已有配置，均是在配置命令前加undo



4. 问题思考

   1. 为什么需要先设置链路类型，再将接口划入具体的vlan中？

      答：交换机内部处理的所有数据包都必须带着 VLAN 标签。但是电脑、打印机等终端设备通常不认识VLAN 标签。华为 VRP 系统非常严谨，如果不先定义接口模式，系统不知道该用哪种逻辑（Access、Trunk、Hybrid）来分配 VLAN。默认所有接口都属于VLAN 1。