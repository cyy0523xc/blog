title: jquery的promise模式
tags:
  - javascript
  - jquery
  - promise
  - 异步编程
date: 2014-09-28 20:01:25
categories: javascript

---

## Promise 

典型的ajax调用：

```javascript
$.ajax({
    url:     '/url',
    success: successFunction,
    error:   errorFunction
});  
```

这个实现没有问题，但是耦合性比较高，不够优美，用promise实现如下：

```javascript
$.ajax({url: '/url'})
.done(successFunction)
.done(successFunction2)
.fail(errorFunction);

// 更通用的方式

$.when(ajax({url: '/url'}))     // $.when(promise)
.done(successFunction)
.fail(errorFunction);
```

好处如下：
- 你可以多次调用done()和fail()函数，并使用不同的回调函数。或许你的一个回调函数用来停止动画，一个用来发起一个新的AJAX请求，一个用来将接受到的数据展示给用户。
- 即使在AJAX调用完成之后，你依然可以调用done()和fail()函数，并且回调函数可以立即执行。不同的状态之间并不会发生变量混乱。当一个AJAX调用结束时，它保持了一个成功状态或者失败状态，这个状态不会发生改变。

## Deferred 

那么Deferred和Promise之间有什么区别呢？正如你在前面看到的，一个promise就是一个由异步函数返回的对象。当你想要自己编写一个这样的函数时你需要使用一个deferred。

一个promise对象有done和fail两个函数，Deferred有两个与之对应的函数：resolve和reject。使用Deferred对象可以构造异步模型。如：

```javascript
function wait() {
    var deferred = $.Deferred();

    // 异步任务（例如异步请求数据）
    setTimeout(function(){
        deferred.resolve();
    }, 2000);

    return deferred.promise();
}
```

see: http://www.html-js.com/article/Study-JavaScript-jQuery-Deferred-and-promise-every-day 


