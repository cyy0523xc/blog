title: 在PHP中近似实现AOP思想
tags:
  - PHP 
  - AOP 
date: 2014-09-18 11:15:43
categories: 

---

## 简介

**AOP** 为Aspect Oriented Programming的缩写，意为： **面向切面编程** ，通过预编译方式和运行期动态代理实现程序功能的统一维护的一种技术。利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高了开发的效率。

**主要的功能** 是：日志记录，性能统计，安全控制，事务处理，异常处理等等。

** 主要的意图** 是：将日志记录，性能统计，安全控制，事务处理，异常处理等代码从业务逻辑代码中划分出来，通过对这些行为的分离，我们希望可以将它们独立到非指导业务逻辑的方法中，进而改变这些行为的时候不影响业务逻辑的代码。

## PHP实现 

PHP的原生内核并没有相关的实现，不过借助魔术函数可以近似实现。

下面的代码主要实现了对实现了对业务类的包装：

- 定义了两个层次的前置和后置函数：可以全局的（类层面的所有action），也可以局部的（对某个action的）
- 允许继承

代码如下：

<!--more-->

```php 
/** 
 * 业务逻辑类的包装类
 *
 * 执行顺序：
 * 1. 全局前置函数
 * 2. 局部前置函数
 * 3. 业务逻辑
 * 4. 局部后置函数
 * 5. 全局后置函数
 *
 * @author cyy0523xc@gmail.com
 */
final class AOP
{
    private $instance;

    // 全局的前置函数和后置函数
    // 每个action调用时都会调用
    // @todo 可以在配置文件配置
    const GLOBAL_BEFORE_FUNC = '_before';
    const GLOBAL_AFTER_FUNC  = '_after';

    // 特定action的前置函数和后置函数的前缀
    const LOCAL_BEFORE_PRE   = '_before_';
    const LOCAL_AFTER_PRE    = '_after_';

    public function __construct($instance)
    {
        $this->instance = $instance;
    }

    public function __call($method, $params)
    {
        if (!$this->__hasMethod($method)) {
            throw new Exception("Call undefinded method " . get_class($this->instance) . "::$method");
        }

        // 调用全局前置函数
        if ($this->__hasMethod(AOP::GLOBAL_BEFORE_FUNC)) {
            $this->__callMethod(AOP::GLOBAL_BEFORE_FUNC);
        }

        // 调用前置函数
        if ($this->__hasMethod(AOP::LOCAL_BEFORE_PRE . $method)) {
            $this->__callMethod(AOP::LOCAL_BEFORE_PRE . $method);
        }

        // 调用业务函数
        $return = $this->__callMethod($method, $params);

        // 调用局部后置函数
        if ($this->__hasMethod(AOP::LOCAL_AFTER_PRE . $method)) {
            $this->__callMethod(AOP::LOCAL_AFTER_PRE . $method);
        }

        // 调用全局后置函数
        if ($this->__hasMethod(AOP::GLOBAL_AFTER_FUNC)) {
            $this->__callMethod(AOP::GLOBAL_AFTER_FUNC);
        }
        
        return $return;
    }

    /**
     * 判断方法是否存在
     * 使用双下划线前缀主要是为了避免冲突
     * @param string $method 方法名
     * @return mixed
     */
    private function __hasMethod($method)
    {
        return method_exists($this->instance, $method);
    }

    /**
     * 调用方法
     * @param string $method 方法名
     * @param array  $params 参数数组
     * @return mixed
     */
    private function __callMethod($method, array $params = null)
    {
        $callBack = array(
            $this->instance,
            $method
        );

        if (null === $params) {
            return call_user_func($callBack);
        } else {
            return call_user_func_array($callBack, $params);
        }
    }
}

```

源码文件：

```
wget https://github.com/cyy0523xc/code/raw/master/php/test/aop.php -O aop.php  
```

