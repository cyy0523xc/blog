title: 使用laravel封装mysql存储过程的调用接口
date: 2015-5-7 
tags: 
    - mysql 
    - 存储过程 
    - laravel 
    - php 

--- 

在使用存储过程时，调用时通常如下：

```sql
set @out_status = 0;
call procedure_name(param1, ...,, @out_status);
select @out_status;
```

比较啰嗦，有必要进行一次封装，如下：

<!-- more -->

```php
/** 
 * 生成存储过程调用函数
 * @param string $produre_name 存储过程名称
 * @param array  $params       调用的参数数组
 * @return array ['status' => , 'data' => ]
 */
function execProdure($produre_name, $params)
{
    $out_status = 'out_status';   // 存储过程的状态记录
    if (!empty($params)) {
        $sql = "CALL {$produre_name}('" . implode("','", $params) . "', @{$out_status});";
    } else {
        $sql = "CALL {$produre_name}(@{$out_status});";
    }

    $produre_log = storage_path() . '/procedure/' . date('Ymd') . '.log';
    $log_msg = "\n\nsql: {$sql}\n\n";
    $return_data = ['sql' => $sql];

    DB::statement("SET @{$out_status} = 0");
    try {
        $data = DB::select($sql);
        $status = DB::select('SELECT @' . $out_status);
        $log_msg .= json_encode($status) . "\n\ndata: " . json_encode($data);
        $return_data['status'] = $status[0]->{'@'.$out_status};
        $return_data['data'] = $data;
    } catch(Exception $e) {
        $log_msg .= 'error: ' . $e->getMessage();
        $return_data['status'] = 1;
        $return_data['data'] = [];
    }

    // 存储过程写入日志
    @file_put_contents($produre_log, $log_msg, FILE_APPEND);
    return $return_data;
}

```

这样在调用存储过程的时候，只需要名字和对应的参数即可，大大简化了实现。
