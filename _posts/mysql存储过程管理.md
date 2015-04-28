title: mysql存储过程管理（laravel下）
tags:
    - mysql 
    - php 
    - laravel
date: 2015-04-28

---

直接在mysql这个层面（例如命令行或者phpmyadmin等）管理存储过程是一个非常麻烦的事情：

- 变更很难管理
- 无法进行版本控制

### 第一步：把存储过程文件都放到git下管理

例如放在项目的sql目录下：

```sh
$ ls sql/
proc_money_appeal_payout.sql             proc_money_apply_cash_payout.sql
proc_money_apply_cash_income_close.sql   proc_money_reward_income.sql
proc_money_apply_cash_income_fail.sql    proc_money_reward_payout.sql
proc_money_apply_cash_income_finish.sql  proc_money_security_deposit_income.sql
proc_money_apply_cash_income.sql         proc_money_security_deposit_payout.sql
```

### 第二步：生成导入数据库的sql文件

如果项目的存储过程比较多，每次更新时都可能会变得很麻烦，所以需要统一的导入入口，例如sql目录下生成source.sql文件，内容如下：

```sql
// vim sql/source.sql
source /home/code/ibbd/yanjiuquan-php/sql/proc_money_appeal_payout.sql;
source /home/code/ibbd/yanjiuquan-php/sql/proc_money_apply_cash_income.sql;
source /home/code/ibbd/yanjiuquan-php/sql/proc_money_apply_cash_income_close.sql;
source /home/code/ibbd/yanjiuquan-php/sql/proc_money_apply_cash_income_fail.sql;
source /home/code/ibbd/yanjiuquan-php/sql/proc_money_apply_cash_income_finish.sql;
source /home/code/ibbd/yanjiuquan-php/sql/proc_money_apply_cash_payout.sql;
source /home/code/ibbd/yanjiuquan-php/sql/proc_money_apply_cash_payout_close.sql;
source /home/code/ibbd/yanjiuquan-php/sql/proc_money_apply_cash_payout_finish.sql;
source /home/code/ibbd/yanjiuquan-php/sql/proc_money_reward_income.sql;
```

说明：这个source文件不应该加入git版本控制中。

这样只需要在命令行中，source这个文件即可：

```sql
source /path/to/source.sql
```

### 第三步：自动生成source.sql

如果手动管理这个文件，也是挺麻烦的，可以结合laravel自动生成：

```php
use Illuminate\Database\Seeder;
class ProcdureInitTableSeeder extends Seeder {

    public function run()
    {
        $path  = 'sql/proc_*.sql';
        $root  = getcwd();
        $files = glob($path);

        $source_file = $root . '/sql/source.sql';
        file_put_contents($source_file, "# @desc   导入及更新存储过程结构, 生成命令：php artisan db:seed\n");
        file_put_contents($source_file, "# @author Alex\n", FILE_APPEND);
        file_put_contents($source_file, "# @date   " . date('Y-m-d') . "\n\n", FILE_APPEND);
        foreach ($files as $file) {
            $sql = "source {$root}/{$file};\n";
            file_put_contents($source_file, $sql, FILE_APPEND);
        }
    }

}
```

### The last

每次变化的时候，还是需要手动source一下，略麻烦。。。暂时没有更好的方式。。。

