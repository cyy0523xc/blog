title: 一段R程序的优化
date: 2013/8/13
tags : 
- R
- 性能优化
- tapply
- 向量化运算

---

## R性能优化

原程序：

```r
CaiAnalyseEiMac <- function(x) {
  # 分析应用中，一个mac对应多个imei地址的情况
  #
  # Args:
  #   x: list类型，待分析数据
  #     x$aid: 应用ID，格式例如：aid=23。（下面的格式也类同）
  #     x$ei:  imei列表
  #     x$mac: mac列表
  # Return:
  #   list，对应多个imei的mac的占比
  
  # 格式化应用数据
  x$aid <- substr(x$aid, 5, 100)
  n <- length(x$aid)
  aid.lst <- unique(x$aid)
  
  # 计算总体一个mac对应多个imei的情况
  
  # 初始化
  tmp.lst <- list()
  mac.unique <- unique(x$mac)
  for (mac in mac.unique) {
    tmp.lst[[mac]] <- c()
  }
  
  # 把imei都加入mac列表
  for (i in 1:n) {
    tmp.lst[[x$mac[i]]] <- c(tmp.lst[[x$mac[i]]], x$ei[i])
  }
  
  # 汇总唯一值的个数
  tmp.lst <- lapply(tmp.lst, FUN=function(x){return(length(unique(x)))})
  
}
```

因为数据量比较大，在工作的机器上跑的时间超过半小时。。。。主要原因有两个：

* copy-on-change，这是R的机制，循环里有大量的修改list操作；
* R的循环效率比较低

后来发现tapply函数可以达到目的，主要代码如下：

```
tmp.lst <- tapply(x$mac, x$ei, function(x)length(unique(x)))
```

非常的简洁，而且时间消耗就几秒而已。
