title: "用php将markdown格式的表格转化为sql语句"
date: 2014-10-14
tags:
- markdown
- sql 
- php 

--- 

我们在设计数据表结构的时候，通常使用markdown格式，例如如下：

```markdown
## 用户表 

总的规则是：一个用户可以管理多个店铺，每个店铺需要配置不同的淘宝账号和密码。

### 用户登陆表：user_login

登陆表，保存用户基本的账号信息

字段              | 类型        | 其他属性                | 说明
------            | -------     | ---                     | --------
uid               | int(11)     | auto\_increment,primary | 自增主键。系统内的所有操作都与uid进行关联。
username          | varchar(32) | unique                  | 用户名。唯一键
email             | varchar(80) | unique                  | email。唯一键
password          | char(32)    |                         | 加密后的密码
status            | tinyint(3)  | default 0               | 状态，默认为0。定义如下
create\_time      | datetime    |                         | 注册时间
last\_login\_time | datetime    |                         | 最后登陆时间

登陆时，可以使用用户名登陆，或者邮箱登陆。
```

当我们需要创建表格的时候，可能还需要写成sql，这很费时费力，而且容易出错，难维护。所以写了一个php的解释工具，可以根据markdown文件快速生成对应的sql语句，生成的sql格式如下：

```sql 
# md文件：http://git.ibbd.net/ibbd/ibbd-bc-py/blob/master/doc/db-tables.md
# Create By http://git.ibbd.net/ibbd/ibbd-bc-py/blob/master/doc/md-table2sql.php
# Create At 2014-10-14 15:44:36

CREATE TABLE `user_login` {
    `uid` INT(11) NOT NULL AUTO_INCREMENT PRIMARY KEY COMMENT '自增主键。系统内的所有操作都与uid进行关联。',
    `username` VARCHAR(32) NOT NULL UNIQUE KEY COMMENT '用户名。唯一键',
    `email` VARCHAR(80) NOT NULL UNIQUE KEY COMMENT 'email。唯一键',
    `password` CHAR(32) NOT NULL COMMENT '加密后的密码',
    `status` TINYINT(3) NOT NULL DEFAULT 0 COMMENT '状态，默认为0。定义如下',
    `create_time` DATETIME NOT NULL COMMENT '注册时间',
    `last_login_time` DATETIME NOT NULL COMMENT '最后登陆时间'
} ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='用户登陆表';
```

php代码如下：

<!--more-->

```php
<?php
/**
 * 将markdown文件中的表格转化为对应的sql语句
 * 表格格式：http://git.ibbd.net/ibbd/ibbd-bc-py/blob/master/doc/db-tables.md 
 *
 * @example

### table中文说明：tablename

desc....

字段       | 类型        | 其他属性 | 说明
---        | ---         | ---      | ---
fieldname1 | varchar(50) | index    | 该字段的说明

 *
 * @author Alex <cyy0523xc@gmail.com>
 * @copyright IBBD
 * @see 
 * @todo 组合索引，前缀索引
 * @version 20141014
 */

//***************  config begin  **************

// 匹配tablename，md文件中如：## 用户登陆表：user_login （user_login就是tablename）
define('PATTORN_TABLE_NAME',     '/^##+.*(:|：)(?P<tablename>(data|user|log)_[a-z_]+)\s*$/');

// 表格字段开始的标志，格式如：---- | ------ | -----| ------ 
define('PATTORN_TABLE_BEGIN',    '/^\-+\s*\|\s*\-+\s*\|\s*\-+/');

// 匹配表格的注释，如匹配tablename中的“用户登陆表”
define('PATTORN_TABLE_COMMENT',  '/^##+\s*(?P<comment>.*?)(:|：)/');

// 字段属性是否默认加上: NOT NULL
define('DEFAULT_NOT_NULL',       true);

// md格式文件地址
$md_file = 'db-tables.md';

// sql代码的保存地址
$create_sql_file = 'ibbd_bc_create_table.sql';

//***************  config end  **************

// 读入md格式的文件
$lines = file($md_file);

// 表名
$table_name = '';

// 是否已经进入了表的字段
$table_field_begin = false;

// sql语句
$table_sql = array();

// sql字段开始标志
$sql_field_begin = false;

// 表的注释
$table_comment = '';

// 索引
$indexs = array();

// 循环处理每行数据
foreach ($lines as $line) {
    $line = trim($line);
    if ('' === $table_name) {
        // 检查数据表开始的位置 
        $table_name = getTableName($line);

        // 表的注释
        if (!empty($table_name)) { 
            if (1 === preg_match(PATTORN_TABLE_COMMENT, $line, $matchs)) {
                $table_comment = trim($matchs['comment']);
            } else {
                $table_comment = '';
            }
        }
    } else {
        if (false === $table_field_begin) {
            // 检查字段开始的位置
            $table_field_begin = checkTableFieldBegin($line);

            // 如果是数据表开始的位置
            if (true === $table_field_begin) {
                $table_sql[$table_name] = "\nCREATE TABLE `{$table_name}` {";
            }
        } else {
            if ('' === $line) {

                // 处理索引
                if (!empty($indexs)) {
                    $index_sql = implode(",\n", $indexs);
                    $table_sql[$table_name] .= ",\n{$index_sql}";
                } 

                // 给sql加上结束标识
                $table_sql[$table_name] .= "\n} ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='{$table_comment}';\n";

                // 字段结束，重置标识
                $table_field_begin    = false;
                $table_name           = '';
                $sql_field_begin      = false;
                $indexs               = array();
            } else {
                // 如果不是第一次进入的话，需要在前一个语句上加逗号
                if (true === $sql_field_begin) {
                    $table_sql[$table_name] .= ',';
                } else {
                    $sql_field_begin = true;
                }

                // 生成sql语句
                $arr = explode('|', $line);

                // 解释内容
                $field_name    = trim($arr[0]);
                $field_name    = str_replace("\\", '', $field_name);
                $field_type    = strtoupper(trim($arr[1]));
                $field_comment = trim($arr[3]);

                // 自增和主键
                $ext = array();
                DEFAULT_NOT_NULL && $ext[] = 'NOT NULL';
                $arr[2] = trim($arr[2]);
                if (!empty($arr[2])) {
                    // 处理额外属性
                    $arr[2]  = strtoupper($arr[2]); 
                    $ext_arr = explode(',', $arr[2]);
                    foreach ($ext_arr as $attr) {
                        $attr = trim($attr);
                        $attr = str_replace("\\", '', $attr);
                        switch ($attr) {
                        case "PRIMARY":
                            $ext[] = 'PRIMARY KEY';
                            break;
                        case 'UNIQUE':
                            $ext[] = 'UNIQUE KEY';
                            break;
                        case 'AUTO_INCREMENT':
                            $ext[] = 'AUTO_INCREMENT';
                            break;
                        case 'INDEX':
                            $indexs[] = "    INDEX (`{$field_name}`)";
                            break;
                        default:
                            if ('DEFAULT' === substr($attr, 0, 7)) {
                                $ext[] = $attr;
                            }
                            break;
                        } // end of switch
                    } // end of foreach
                } // end of if

                $ext = empty($ext) ? '' : implode(' ', $ext);

                // sql语句
                $table_sql[$table_name] .= "\n    `{$field_name}` {$field_type} {$ext} COMMENT '{$field_comment}'";
            }
        }
    }
} // end of foreach

// 组成最后的sql文件的内容
$sql  = "# md文件：http://git.ibbd.net/ibbd/ibbd-bc-py/blob/master/doc/db-tables.md\n";
$sql .= "# Create By http://git.ibbd.net/ibbd/ibbd-bc-py/blob/master/doc/markdown2sql.php\n";
$sql .= "# Create At " . date("Y-m-d H:i:s") . "\n\n\n";
$sql .= implode("", $table_sql);

// 写入文件
echo $sql;
file_put_contents($create_sql_file, $sql);


// ******************** 以下是函数 ***********************

// 获取tablename
function getTableName($line) 
{
    if (1 === preg_match(PATTORN_TABLE_NAME, $line, $matchs)) {
        return $matchs['tablename'];
    }

    return '';
}

// 判断是否为字段的开始位置
function checkTableFieldBegin($line) {
    if (1 === preg_match(PATTORN_TABLE_BEGIN, $line)) {
        return true;
    }
    return false;
}
```

源文件：https://github.com/cyy0523xc/code/blob/master/php/markdown2sql.php
