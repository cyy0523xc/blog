title: hexo记录
date: 2014-09-11 11:19:30
categories: Record
tags: 
- hexo

---

### f.browser.msie没有定义

```
# @see: http://stackoverflow.com/questions/14344289/fancybox-doesnt-work-with-jquery-v1-9-0-f-browser-is-undefined-cannot-read/14344290

# vim themes/phase/layout/_partial/head.ejs

<script src="http://code.jquery.com/jquery-latest.js"></script>

# replace by:
<script src="http://code.jquery.com/jquery-latest.js"></script>
<script src="http://code.jquery.com/jquery-migrate-1.0.0.js"></script>

```


### 文章摘要

在需要的地方加上：

```
<!--more-->
```

### 插入数学公式

Mathjax的语法和LaTeX一致，在markdown中直接插入即可(注意上面我们修改了mathjax默认的inlineMath选项)，下面举些例子，更多语法请参考 [LaTeX Higher Mathematics](http://www.lsv.ens-cachan.fr/~markey/LaTeX/doc/Companion-chapter8.pdf  )：

[LaTeX入门](http://blog.163.com/goldman2000@126/blog/static/167296895201221242646561/ )

插入方程组:

\begin{aligned}
\dot{x} & = \sigma(y-x) \\\
\dot{y} & = \rho x - y - xz \\\
\dot{z} & = -\beta z + xy
\end{aligned}

插入矩阵：

\begin{bmatrix}
1 & 2\\\
3 & 4
\end{bmatrix}

薛定谔方程：

$$ \hbar\frac{\partial \psi}{\partial t}
= \frac{-\hbar^2}{2m} \left(
\frac{\partial^2}{\partial x^2}
+ \frac{\partial^2}{\partial y^2}
+ \frac{\partial^2}{\partial z^2}
\right
) \psi + V \psi. $$


see: http://www.winterland.me/2013/12/hexo-mathjax/

vim themesfolder/layout/\_partial/mathjax.ejs

```html
<!-- mathjax config similar to math.stackexchange -->

<script type="text/x-mathjax-config">
MathJax.Hub.Config({
    tex2jax: {
        inlineMath: [ ['$','$'], ["\\(","\\)"]  ],
        processEscapes: true

    }

});
</script>

<script type="text/x-mathjax-config">
MathJax.Hub.Config({
    tex2jax: {
        skipTags: ['script', 'noscript', 'style', 'textarea', 'pre', 'code']

    }

});
</script>

<script type="text/x-mathjax-config">
MathJax.Hub.Queue(function() {
    var all = MathJax.Hub.getAllJax(), i;
    for(i=0; i < all.length; i += 1) {
        all[i].SourceElement().parentNode.className += ' has-jax';

    }

});
</script>

<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
</script>
```

### 自定义页面

```bash
hexo n page "about"
```

### 加载慢的问题

通常是因为加载外部资源超时，例如google开放的jquery库等。修改即可。

### 常用命令

```bash
hexo clean  # deploy到github时，有时需要先运行该命令，例如修改样式时
hexo g
hexo d
hexo s
hexo n "title"   # 生成文章 
```

