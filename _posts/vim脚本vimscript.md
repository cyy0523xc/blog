title: vim脚本vimscript
tags:
  - vim 
  - vimscript
date: 2014-09-19 19:55:44
categories:

---

Vim的脚本语言被称为Vimscript，是典型的动态式命令语言，提供一些常用的语言特征：变量、表达式、控制结构、内置函数、用户自定义函数、一级字符串、列表、字典、终端、文件IO、正则表达式模式匹配、异常和集成调试器等。
在学习Vimscript时，你可以学习Vim自带的Vimscript文档，打开Vim自带的Vimscript很简单，只需在Vim内部执行：help vim-script-intro（Normal模式下）

<!--more-->

## 执行vim脚本

```bash
# 在vim内部
:source ~/test.vim 
```

## 变量

```
let {variable} = {expression}

let i = 1
let i += 2
let a = i > 0 ? 1 : 0
```

## 语句

```
# if-else
if {condition}
    {statements}
elseif {condition}
    {statements}
else
    {statements}
endif

# while 
while {condition}
    {statements}
endwhile

# 字符串匹配
a =~ b   # 匹配
a !~ b   # 不匹配

```

## 函数




