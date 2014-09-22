title: vimscript问题汇总
tags:
  - vim 
date: 2014-09-22 10:54:36
categories: record

---

## source时提示“E484: 无法打开文件”

```
source '/home/code/github/code/vim/cyy.vim'
```

应该去掉路径中的单引号，如下：

```
source /home/code/github/code/vim/cyy.vim
```




