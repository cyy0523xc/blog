title: "R语言的小技巧"
date: 2013/9/6
tags: 
- R
- 经验

---


### ifelse和if ... else ...不同

认为相同，主要是受到之前经验的影响，不过这里的ifelse是向量化的运算，返回值的长度会和test的长度一致。可以看ifelse的源码：

```r
function (test, yes, no) 
{
    if (is.atomic(test)) 
        storage.mode(test) <- "logical"
    else test <- if (isS4(test)) 
        as(test, "logical")
    else as.logical(test)
    ans <- test
    ok <- !(nas <- is.na(test))
    if (any(test[ok])) 
        ans[test & ok] <- rep(yes, length.out = length(ans))[test & 
            ok]
    if (any(!test[ok])) 
        ans[!test & ok] <- rep(no, length.out = length(ans))[!test & 
            ok]
    ans[nas] <- NA
    ans
}
```

```r
> ifelse(c(T, F, T), c(1,2), c(5,6))  # 长度不够，则会自动补充
[1] 1 6 1
```

### install RCurld的问题

在ubuntu12.04上安装：

```r
install.packages("RCurl")
```

提示：

```
checking for curl-config... no
Cannot find curl-config
ERROR: configuration failed for package ‘RCurl’
```

解决：

```shell
sudo apt-get install libcurl4-gnutls-dev
```

附: http://cos.name/cn/topic/108303


