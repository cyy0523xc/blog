title: shell记录
date: 2014-09-17 14:29:07
categories: record
tags:
- shell 

---

# 期待一元表达式

异常信息：

```
bash: [: h: 期待一元表达式]
```

对应语句：

```shell 
if [ "h" = $1 ]

# 如果变量未定义的话，则报错，应改成：

if [ "h" = "$1" ]
```


