title: "rsync命令"
date: 2013/8/30
tags: 
- linux
- rsync
- scp

---

从远程服务器复制文件到本地服务器以前通常会使用scp命令，例如：

```
scp -P 3000 user@host:/data2/bak/20130801/part-* /home/windows/data/20130825/
```

不过用脚本跑的时候，发现得到的文件不齐全，有些文件不知道为什么没有下载到本地，所以寻找可以不覆盖更新的命令。本以为scp有这样的参数的，不过没发现。后来就使用rsync命令了，如：

```
rsync -aPuv '-e ssh -p 3000' user@host:/data2/bak/20130825/part-* /home/windows/data/20130825/
```

rsync的参数解释：

* P：显示进度条信息
* u：update，只返回不同的文件

扩展：<http://www.howtocn.org/rsync:use_rsync>
