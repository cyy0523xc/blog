title: mysql存储过程
tags: 
    - mysql 
    - 存储过程 
date: 2015-5-7

--- 

sql存储过程的实现跟一般的语言相比，有点特殊，有很多值得注意的地方，下面以一个支付的存储过程为例：

<!-- more -->

```sql
# 赏金的缴纳
# 从用户账户扣钱，加到平台的赏金账户
# 返回订单ID
# 相关的数据表：user_info, order_info, order_flow
# @author alex
# @date 2015-5-4

DELIMITER //
DROP PROCEDURE IF EXISTS  proc_money_reward_income //
CREATE PROCEDURE proc_money_reward_income(
    in_user_uid    INT,             # 用户ID
    in_reward      DECIMAL(10,2),   # 赏金金额
    in_service_fee DECIMAL(10,2),   # 平台服务费
    in_task_id     INT,             # 任务ID
    OUT out_error  INT              # 返回状态，0为正常
)
label_proc:BEGIN

    DECLARE ym                MEDIUMINT;        # 年份月份
    DECLARE order_type        TINYINT;          # 订单类型
    DECLARE order_id          INT;              # 新生成的订单ID
    DECLARE pf_reward_uid     INT;              # 平台的赏金账户
    DECLARE pf_fee_uid        INT;              # 平台的服务费账户
    DECLARE pf_reward_balance DECIMAL(12,2);    # 平台的赏金账户余额
    DECLARE pf_fee_balance    DECIMAL(12,2);    # 平台服务费账户余额
    DECLARE user_balance      DECIMAL(12,2);    # 用户账户余额
    DECLARE total_money       DECIMAL(10,2);    # 用户需要支付的总额

    DECLARE EXIT HANDLER FOR SQLEXCEPTION, NOT FOUND
    BEGIN
        # 异常回滚
        ROLLBACK;
        SELECT order_id;
    END;

    # 初始化
    SET pf_reward_uid = 10;
    SET pf_fee_uid    = 11;
    SET order_type    = 1;
    SET order_id      = 0;
    SET user_balance  = 0;
    SET total_money   = in_reward + in_service_fee;
    SET ym            = DATE_FORMAT(CURRENT_TIMESTAMP, '%Y%m');

    ###########################################

    # 判断用户余额是否足够
    SELECT `stat_balance` INTO user_balance
    FROM `yjq_user_info`
    WHERE `id` = in_user_uid ;

    IF (total_money > user_balance) THEN
        SET out_error = 10;
        SELECT order_id;
        LEAVE label_proc;
    END IF;

    # 开始事务
    SET out_error = 1;
    START TRANSACTION;

    # user_info表: 用户的余额减少
    UPDATE `yjq_user_info` 
    SET `stat_balance` = `stat_balance` - total_money,
        `stat_reward` = `stat_reward` + in_reward,
        `stat_payout` = `stat_payout` + total_money
    WHERE `id` = in_user_uid;

    # 异常监控点
    IF (ROW_COUNT() = 0) THEN 
        # 如果影响的结果数为0，则标识状态并回滚
        SET out_error = 110;
        SELECT order_id;
        ROLLBACK;
        LEAVE label_proc;
    END IF;
    SET out_error = 100;   # 如果上一个update语句出错，则执行不到这里

    # 平台的赏金账户的余额增多
    SELECT `stat_balance` INTO pf_reward_balance 
    FROM `yjq_user_info`
    WHERE `id` = pf_reward_uid;

    UPDATE `yjq_user_info` 
    SET `stat_balance` = `stat_balance` + in_reward
    WHERE `id` = pf_reward_uid;

    # 异常监控点
    IF (ROW_COUNT() = 0) THEN 
        SET out_error = 111;
        SELECT order_id;
        ROLLBACK;
        LEAVE label_proc;
    END IF;
    SET out_error = 101;

    # 创建订单(需要预置订单编号)
    SELECT FLOOR(RAND() * 1000000000) INTO order_id;

    INSERT INTO `yjq_order_info`(`order_num`, `order_type`, `task_id`, `user_uid`, `created_date`, `created_ym`, `pay_time`, `platform_deposit`, `platform_money`, `remark`)
    VALUES(order_id, order_type, in_task_id, in_user_uid, CURRENT_DATE, ym, CURRENT_TIMESTAMP, in_reward, in_service_fee, 'auto');

    # 异常监控点
    IF (ROW_COUNT() = 0) THEN 
        SET out_error = 112;
        SELECT order_id;
        ROLLBACK;
        LEAVE label_proc;
    END IF;
    SET out_error = 102;

    COMMIT;

    ######################################

    # 返回状态
    SET out_error = 0;
    SELECT order_id;
END //
DELIMITER ;
```
存储过程的测试是相对比较麻烦的，所以这里设置了几个异常监控点，根据调用时返回的状态可以比较明确地知道是那个位置发生的异常。
