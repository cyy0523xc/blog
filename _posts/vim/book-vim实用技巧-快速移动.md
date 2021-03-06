title: "vim实用技巧之快速移动与跳转"
date: 2014-11-16
tags:
- vim 
- vim实用技巧
- 读书笔记

---

动作命令是vim操作中最重要的一些命令。不仅可以实现快速移动，还能与操作符进行配合使用。

<!--more-->

- 让手指保持在本位行上

移动命令：``h, l, j, k`` （h和l用来解决 **差一错误** ，不应该出现连续按多次h键的情况）

- 屏幕行与实际行

屏幕行命令：``gj, gk, g0, g$``

- 基于单词的移动 

向后或者向前一个单词：``w, b``（光标落在首字母上）；``e, ge``（光标落在尾字母上）

```
单词：（:h word）数字，字母，下划线，或者其他非空白字符组成，以空白字符分割
字串：（:h WORD）以非空白字符组成，以空白字符分割 

简单理解：单词比字串短
```

字串命令：``W, B, E, gE``

- 对字符进行查找

``f{char} t{char} ; ,``：如``f,dt.``：删除逗号及逗号与句号之间的内容。

注意字符出现的频率

- 通过查找进行移动 

对比命令：``v/ge<CR>hd``与``d/ge<CR>`` （第二个命令符合 ``d{motion}`` 的模式，更好）

- 用精确的文本对象选择选区 

1. 文本对象有两个字符组成，第一个字符是i（inside）或者a（around），第二个字符可以是 ``), }, ], >, ', ", `, t， w, W, s, p`` （t是指标签，对html文档特别有效；s是指句子；p是指段落）
2. 常见操作：``d{motion}, c{motion}, y{motion}``

- 删除周边，修改内部

注意``caw``与``ciw``的区别。

- 位置标志

自动位置标志：

1. **``** 上次跳转的位置；
2. **`.** 上次修改的置；
3. **`^** 上次插入的位置
4. **`[** 上次修改或者复制的开始位置
5. **`]** 上次修改或者复制的结束位置
6. **`<** 上次高亮选区的开始位置
7. **`>** 上次高亮选区的结束位置

- 在匹配括号中跳转

命令：``%``


