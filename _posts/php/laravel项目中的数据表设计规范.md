title: laravel项目中的数据表设计规范及使用方式
tags:
    - laravel
    - php
    - 数据库
date: 2015-04-28

---

### 字段设计规范

```php
// 主键
$table->increments('id');

// 加入 created_at 和 updated_at 字段
$table->timestamps();

// 加入 deleted_at 字段于软删除使用
$table->softDeletes();

// 加入整数 taggable_id 与字串 taggable_type
$table->morphs('taggable');

// 加入 remember_token 使用 VARCHAR(100) NULL
$table->rememberToken();
```

### 软删除

通过软删除方式删除了一个模型后，模型中的数据并不是真的从数据库被移除。而是会设定 deleted\_at时间戳。要让模型使用软删除功能，只要在模型类里加入 SoftDeletingTrait 即可：

```php 
use Illuminate\Database\Eloquent\SoftDeletes;

class User extends Model {
    
    use SoftDeletes;

    protected $dates = ['deleted_at'];

}
```

现在当您使用模型调用 delete 方法时， deleted\_at字段会被更新成现在的时间戳。在查询使用软删除功能的模型时，被「删除」的模型数据不会出现在查询结果里。

```php
// 强制查询软删除数据(使用 withTrashed 方法)
$users = User::withTrashed()->where('account_id', 1)->get();

// 只想查询被软删除的模型数据，可以使用 onlyTrashed 方法
$users = User::onlyTrashed()->where('account_id', 1)->get();
```

### 时间戳 

默认 Eloquent 会自动维护数据库表的 created\_at 和 updated\_at 字段。只要把这两个「时间戳」字段加到数据库表， Eloquent 就会处理剩下的工作。

### 范围查询

#### 定义范围查询

范围查询可以让您轻松的重复利用模型的查询逻辑。要设定范围查询，只要定义有 scope 前缀的模型方法：

```php 
class User extends Model {

    public function scopePopular($query)
    {
        return $query->where('votes', '>', 100);
    }

    public function scopeWomen($query)
    {
        return $query->whereGender('W');
    }

}
```

#### 使用范围查询 

```php 
$users = User::popular()->women()->orderBy('created_at')->get();
```

#### 动态范围查询

有时您可能想要定义可接受参数的范围查询方法。只要把参数加到方法里：

```php
class User extends Model {

    public function scopeOfType($query, $type)
    {
        return $query->whereType($type);
    }
}
```

使用：

```php
$users = User::ofType('member')->get();
```

### 使用枢纽表

如您所知，要操作多对多关联需要一个中间的数据库表。 Eloquent 提供了一些有用的方法可以和这张表互动。例如，假设 User 对象关联到很多 Role 对象。取出这些关联对象时，我们可以在关联模型上取得 pivot 数据库表的数据：

```php
$user = User::find(1);

foreach ($user->roles as $role)
{
    echo $role->pivot->created_at;
}
```

注意我们取出的每个 Role 模型对象会自动给一个 pivot 属性。这属性包含了枢纽表的模型数据，可以像一般的 Eloquent 模型一样使用。

默认 pivot 对象只会有关联键的属性。如果您想让 pivot 可以包含其他枢纽表的字段，可以在定义关联方法时指定那些字段：

```php
return $this->belongsToMany('App\Role')->withPivot('foo', 'bar');
```

其他操作：

```php
// 删除枢纽表的关联数据
User::find(1)->roles()->detach();

// 更新枢纽表的数据
User::find(1)->roles()->updateExistingPivot($roleId, $attributes);

```

### 集合

所有 Eloquent 查询返回的数据，如果结果多于一条，不管是经由 get 方法或是 relationship，都会转换成集合对象返回。这个对象实现了 IteratorAggregate PHP 接口，所以可以像数组一般进行遍历。

```php
// 确认集合中里是否包含特定键值
$roles = User::find(1)->roles;

if ($roles->contains(2))
{
    //
}

// 集合遍历
$roles = $user->roles->each(function($role)
{
    //
});

// 集合过滤
$users = $users->filter(function($user)
{
    return $user->isAdmin();
});

```

待续。。。

