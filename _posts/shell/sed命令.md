title: "sed命令"
date: 2013/9/5
tags: 
- linux
- sed

---


## 问题

源数据：

```
drt=2013-09-02+22:14:28&at=7&cid=mDamz1b7WRJZ
drt=2013-09-02+22:14:29&at=3&cid=Q4ETETM9Hjx0
drt=2013-09-02+22:14:39&at=0&cid=ytBSubxEaFN6
drt=2013-09-02+22:14:40&at=3&cid=mDamz1b7WRJZ
drt=2013-09-02+22:14:42&at=6&cid=ytBSubxEaFN6
drt=2013-09-02+22:14:42&at=3&cid=mDamz1b7WRJZ
drt=2013-09-02+22:14:45&at=0&cid=66OwWFuVBMSS
```

希望把drt字段中的+号及后面的替换掉：

```
sed 's/\+[\d:]+&/&/g'
```

结果就是死活不工作。。。

## 解决

原来&在sed中也是元字符，之前从来没注意到这个，和普通正则还差异多多。最后就只是一个转义符的事情：

```
sed 's/\+/\&/g'
```

关于&的说明：

```
保存搜索字符用来替换其他字符，如s/love/**&**/，love这成**love**。
```

还有：

* sed的元字符里原来没有+号的。。。
* \d发现也是无效的

最终就变成了：

```
sed 's/+[0-9:]*\&/\&/g'
```

## 附录

* sed的元字符：<http://tsnc.zhongaokao.com/tsnc_wgrj/doc/sed.htm#id2810450>
* sed manual：<http://www.gnu.org/software/sed/manual/sed.html>
* grep、sed、awk、perl等对正则表达式的支持的差别：<http://blog.csdn.net/zouxue138/article/details/8620799>
