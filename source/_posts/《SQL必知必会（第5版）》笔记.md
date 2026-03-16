---
title: 《SQL必知必会（第5版）》—— 📝笔记
date: 2026-03-06 13:14:52
categories: 书籍
tags:
- 阅读
- 书籍
- sql
---


https://forta.com/books/0135182794/

1. 了解SQL

   - 数据库（database）——保存有组织的数据的容器（通常是一个文件或一组文件）

   - 表（table）——某种特定类型数据的结构化清单

   - 模式——关于数据库和表的布局及特性的信息
   - 列（column）——表中的一个字段，所有表都是由一个或多个列组成的
   - 数据类型——允许什么类型的数据，每个**表列**都有相应的数据类型，它限制（或允许）该列中存储的数据
   - 行（row）——表中的一个记录
   - 主键（primary key）——一列或几列，其值能够唯一标识表中每一行
   - SQL（Structured Query Language，结构化查询语言）——一种专门用于与数据库沟通的语言

   > 数据库管理系统（DBMS）——用于管理数据库的软件，许多DBMS厂商通过增加语句或指令，对SQL进行了扩展。



2. 检索数据

   - SELECT

   ```sql
   # 检索单个列
   SELECT prod_name
   FROM Products;
   
   # 检索多个列
   SELECT prod_id,prod_name,prod_price
   FROM Products;
   
   # 检索所有列
   SELECT *
   FROM Products;
   
   # 检索不同的值-去重
   SELECT DISTINCT vend_id
   FROM Products;
   
   # 限制结果-只取前5行
   ## SQL Server
   SELECT TOP 5 prod_name
   FROM Products;
   ## DB2
   SELECT prod_name
   FROM Products
   FETCH FIRST 5 ROWS ONLY;
   ## Oracle
   SELECT prod_name
   FROM Products
   WHERE ROWNUM <=5;
   ## MySQL、MariaDB、PostgreSQL、SQLite
   SELECT prod_name
   FROM Products
   LIMIT 5;
   ### 只取后5行
   SELECT prod_name
   FROM Products
   LIMIT 5 OFFSET 5;
   ```

   - 注释

   ```sql
   SELECT prod_name	-- 这是一条注释
   FROM Products;
   
   # 这是一条注释
   SELECT prod_name
   FROM Products;
   
   /* 多行
   注释 */
   ```



3. 排序检索数据
4. 过滤数据
5. 高级数据过滤
6. 用通配符进行过滤
7. 创建计算字段
   - 拼接字段（加号或两个竖杠表示）
   - 别名（AS）
   - 执行算数计算（+、-、*、/）
8. 使用函数处理数据
9. 汇总数据
   - AVG()
   - COUNT()
   - MAX()
   - MIN()
   - SUM()
10. 分组数据
    - GROUP BY 子句
    - HAVING 子句
11. 使用子查询
12. 联结表
13. 创建高级联结
14. 组合查询
15. 插入数据
16. 更新和删除数据
17. 创建和操纵表
18. 使用视图
19. 使用存储过程
20. 管理事务处理
21. 使用游标
22. 高级SQL特性
    - 约束
    - 索引
    - 触发器



