title: mysql存储过程中的select into与set语句的问题
tags:
    - mysql
    - 存储过程 

--- 

今天解决了存储过程中的select into与set语句的问题，原来存储过程大概这样：

```sql
# 在存储过程内部，省略
SET user_uid = 0;

SELECT `user_uid` INTO user_uid
FROM table
WHERE ...;
```

结果user\_uid的值一直为0，确认数据表满足条件是有一条数据的。实在没办法了，就把set语句注释，结果居然OK了。

原因待查。。。



