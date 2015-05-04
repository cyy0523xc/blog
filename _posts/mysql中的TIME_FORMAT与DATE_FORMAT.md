title: mysql中的TIME\_FORMAT与DATE\_FORMAT函数
date: 2015-5-4
tags: 
    - mysql

--- 

之前一直使用TIME\_FORMAT来格式化日期，如下：

```sql
select TIME_FORMAT(CURRENT_TIMESTAMP, '%y%m%d');
```

但是今天发现在有些版本的mysql上会返回0，不是想要的结果。查了一下：

```
TIME_FORMAT(time,format)  
这象上面的DATE_FORMAT()函数一样使用，但是format字符串只能包含处理小时、分钟和秒的那些格式修饰符。
其他修饰符产生一个NULL值或0。  
```

### DATE_FORMAT

改成使用DATE\_FORMAT即可：

```sql
select DATE_FORMAT(CURRENT_TIMESTAMP, '%y%m%d');
```


