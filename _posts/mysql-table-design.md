title: mysql数据表设计经验（结合laravel）
date: 2015-5-8
tags:
    - mysql 
    - laravel 

--- 

这是一份对前阶段数据表设计经验的总结：

<!-- more --> 

- 前期用markdown进行表结构管理，进入开发阶段使用migrate进行变更管理。

后期结构变更的时候，也需要同步反应到对于那个的md文件上，方便查阅。

- 所有数据表使用统一的前缀，例如 *yjq_* 
- 使用二级前缀规则，例如 *yjq_config* 。二级前缀对应数据模型的目录，例如 *yjq_config* 对应目录*model/Config/* 

``` 
前缀       | 说明
----       | ----
yjq_config | 配置相关表
yjp_admin  | 管理员相关表
yjp_user   | 用户相关表
yjp_task   | 任务相关表
yjp_order  | 订单相关表
yjp_msg    | 站内信等信息相关表
yjp_log    | 日志相关表

```

可以使用脚本根据表名生成对应的数据模型。

- 通用字段名规范 

```
通用字段名 | 说明
-----      | ---
id         | 表的主键
created_at |
updated_at |
status     |
 
```

- 类别字段不要从0开始定义

例如在根据类别进行搜索时，0就可以表示“不限”。

- 按时间段生成的表，例如月份表，命名如：*yjq_log_201505* 
- 涉及到需要根据类型进行筛选（多选）时，如果类型不是很多，可以使用二进制位运算的形式解决。如果类型太多，例如行业省份这种就没办法了。这种运算处理最好封装到模型里。
- 统计类型的字段，统一加上前缀*stat_*


