title: "R对数组指定下标顺序输出"
date: 2014/2/11
tags: 
- R
- 经验

---

### 问题

```R
> table(week.data['星期'])

星期二 星期六 星期日 星期三 星期四 星期五 星期一 
    34     46     37     55     40     46     65 
```

table函数可以统计各个值的频度，但是输出的顺序却不是我们所期待的（期待的顺序是从星期一到星期日）。特别是使用barplot生成柱状图时，如果不按顺序，那肯定是不行的。

### 解决

```R
CaiSortByFields <- function(p.arr, p.fields) {
  # 把数组按照指定顺序输出
  # 
  # Args：
  #   p.arr   数组数据
  #   p.fields  指定顺序
  # Returns：
  #   array
  
  tmp <- array()
  for (i in p.fields) {
    tmp[i] <- p.arr[i]
  }
  
  # 去掉缺失值
  tmp[!is.na(tmp)]
}
```

这个方式比较曲折，应该有更直接的方式的。
