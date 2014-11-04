titile: "封装redis基础类库"
date: 2014-11-04
tags: 
- php 
- redis

--- 

开发的时候，需要用到缓存，经常需要封装各种缓存类，有时为了所谓的缓存通用了，我们对redis做了统一性的封装，结果只能用到redis最基本的kv结构，大大浪费了。

所以封装redis的时候，应该恰到好处就好了，phpredis本身的api就已经挺好的了。

下面是一个封装：


```php
<?php
/** 
 * redis初始化类库
 * @author Alex <cyy0523xc@gmail.com>
 * @copyright IBBD.net
 * @see 
 * @todo 
 * @version 20141104
 */

namespace Common\Model;

class RedisModel 
{
    // redis对象
    private $handler = null;

    /**
     * 获取Redis缓存对象
     * @param array $options redis连接所需要的参数
     * @return Redis 
     */
    public static function getInstance(array $options = null)
    {
        static $instance = array();
        if(empty($options)) {
            // 加载配置文件
            $options = C('CACHE.REDIS_CONFIG');
        }

        $guid = to_guid_string($options);
        if (!isset($instance[$guid])) {
            $obj = new self($options);
            $instance[$guid] = $obj->getRedis();
        }
        
        return $instance[$guid];
    }

    /**
     * 构造函数
     * @param array $options redis需要的参数 
     */
    protected function __construct(array $options) 
    {
        if ( !extension_loaded('redis') ) {
            error_log('NOT_SUPPORT:redis');
        }

        $func = $options['persistent'] ? 'pconnect' : 'connect';
        $this->handler  = new \Redis;
        $options['timeout'] === false ?
            $this->handler->$func($options['host'], $options['port']) :
            $this->handler->$func($options['host'], $options['port'], $options['timeout']);

        // select db 
        isset($options['db']) && $this->select($options['db']);
    }

    /**
     * 获取redis处理对象
     * @return Redis 
     */
    protected function getRedis()
    {
        return $this->handler;
    }
}

```
