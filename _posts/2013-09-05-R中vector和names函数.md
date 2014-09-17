title: "R中vector和names函数"
date: 2013/9/5
tags: 
- R
- vector
- names

---


## 问题

源数据保存在文件中，格式如下：

```
drt=2013-09-02+22:14:28&at=7&cid=mDamz1b7WRJZ
drt=2013-09-02+22:14:29&at=3&cid=Q4ETETM9Hjx0
drt=2013-09-02+22:14:39&at=0&cid=ytBSubxEaFN6
drt=2013-09-02+22:14:40&at=3&cid=mDamz1b7WRJZ
drt=2013-09-02+22:14:42&at=6&cid=ytBSubxEaFN6
drt=2013-09-02+22:14:42&at=3&cid=mDamz1b7WRJZ
drt=2013-09-02+22:14:45&at=0&cid=66OwWFuVBMSS
```

需要生成一个list，其下标是数据的字段名：drt，at，cid


## 解决

开始写成这样，很别扭的代码：

```r
  x <- scan(filename, what="", sep="&", nlines=1)
  fields.name <- sapply(x, FUN=function(x){strsplit(x, split="=")[[1]][1]})
  names(fields.name) <- NULL
  names(fields.name) <- fields.name
  fields.name <- as.list(fields.name)
```

借鉴tapply的源码，改成这样：

```r
  x <- scan(filename, what="", sep="&", nlines=1)
  fields.name <- sapply(x, FUN=function(x){strsplit(x, split="=")[[1]][1]})
  tmp.fields.name <- vector("list", length(fields.name))    # 生成一个空的list
  names(tmp.fields.name) <- fields.name
```
