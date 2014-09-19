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

```
function {name}({var1}, {var2}, ...)
    {body}
endfunction
```

在Vimscript中，用户自定义函数的函数名第一个字母必须大写，下面展示一个自定义的Min函数

```
function! s:Min(num1, num2)
    if a:num1 < a:num2
        let smaller = a:num1
    else
        let smaller = a:num2
    endif
    return smaller
endfunction
```

- function后面加上强制命令修饰符 **!** 表示该函数如果存在则替换，这样做是有必要的，假设该Min函数位于某个脚本文件中，如果没有加上强制命令修饰符，脚本文件被载入两次时会报错：函数已存在。
- Vimscript中有许多内置函数，大约超过200过，你可以在Vim内部输入 :help functions来学习。

## list列表

一个list包含一组有序的元素，和C++不同的是，Vimscript中list的每个元素可以为任意类型。元素通过索引访问，第一个元素的索引为0。list使用两个中括号包裹。

```bash 
# 1 创建list
let list1 = []
let list2 = ['a', 2]

# 2 list元素的访问
let list[0] = 1
echo list[0]

# 3 list增加新的元素
# 添加新的值到 list 的尾部
call add(list, val)
# 添加新的值到 list 的头部
call insert(list, val)

# 4 list删除元素
# 删除索引为 index 的元素并返回此元素
call remove(list, index)
# 删除索引为 startIndex 到 endIndex（含 endIndex）的元素
# 返回一个 list 包含了这些被删除的元素
call remove(list, startIndex, endIndex)
# 清空 list，这里索引 -1 对应 list 中最后一个元素
call remove(list, 0, -1)

# 5 判断list是否为空
empty(list)

# 6 获取list的大小
echo len(list)

# 7 拷贝list
# 浅拷贝 list
let copyList = copy(list)
# 深拷贝 list
let deepCopyList = deepcopy(list)
call deepcopy()

# 8 使用for遍历list
let list = ['one', 'two', 'three']
for element in list
    echo element
endfor
```

## dictionary

dictionary是一个关联数组。每个元素都有一个key和一个value，和C++中map类似，我们可以通过key来获取value。dictionary使用两个大括号包裹。

```
# 1 创建dictionary
" 创建一个空的 dictionary
let dict = {}
" 创建一个非空的 dictionary
let dict = {'one': 1, 'two': 2, 'three': 3 }

# 2 dictionary元素的访问和修改
let dict = {'one': 1, 'two': 2}
echo dict['one']

# 3 dictionary元素的增加和删除
2 let dict[key] = value
4 unlet dict[key]

# 4 获取dictionary的大小
echo len(dict)

# 5 使用for语句遍历一个dictionary
let dict = {'one': 1, 'two': 2}
for key in keys(dict)
    echo key
endfor

# 遍历时 key 是未排序的，如果希望按照一定顺序访问可以这么做：
for key in sort(keys(dict))
    echo key
endfor
 
# keys 函数用于返回一个 list，包含 dictionary 的所有 key
# values 函数用于返回一个 list，包含 dictionary 的所有 value
# items 函数用于返回一个 list，包含 dictionary 的 key-value 对
for value in values(dict)
    echo value
endfor

for item in items(dict)
    echo item
endfor
```

