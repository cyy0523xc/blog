title: mysql存储过程中的select into与局部变量的问题
tags:
    - mysql
    - 存储过程 

--- 

今天解决了存储过程中的select into与局部变量的问题，原来存储过程大概这样：

```sql
# 在存储过程内部，省略
DECLARE user_uid INT;

SET user_uid = 0;

SELECT `user_uid` INTO user_uid
FROM table
WHERE ...;
```

结果user\_uid的值一直为0，确认数据表满足条件是有一条数据的。试了很多种方法才发现是into赋值的变量不能和select的字段名相同，甚至不能用declare来定义，所以最终解决是这样：

```sql
# 注意不能定义user_uid这个局部变量
DECLARE lc_user_uid INT;

SET lc_user_uid = 0;

SELECT `user_uid` INTO lc_user_uid
FROM table
WHERE ...;
```




