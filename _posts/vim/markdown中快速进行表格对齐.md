title: "在markdown中快速进行表格对齐"
date: 2014-10-13
tags:
- vim 
- markdown
- vimscript 

---

在markdown中的表格格式对齐是比价麻烦的，有了tabular这个vim插件之后，简单了许多，用法如下：

```
:45,67 Tab /|
```

这样就可以将表格按竖线进行对齐了，比较简单。不过经常修改表格时，每次都要输入这样一个命令还是挺麻烦的，故实现成快捷键的形式，如下：

```vimscript
" align
" markdown table align by "|"
nmap tab\|   :call CyyAlignBy("\|")<CR>
nmap tab,    :call CyyAlignBy(",")<CR>
nmap tab=    :call CyyAlignBy("=")<CR>
nmap tab=>   :call CyyAlignBy("=>")<CR>

" 根据某字符串对齐
func CyyAlignBy(string)
    let __lineno    = line('.')
    let __begin_ln  = __lineno
    let __end_ln    = __lineno

    " 向前搜索，直到空行
    while "" != getline(__begin_ln)
        let __begin_ln -= 1
        if __begin_ln < 1
            break
        endif
    endwhile

    " 向后搜索，直到空行 
    while "" != getline(__end_ln)
        let __end_ln += 1
    endwhile 
    
    let __exec = printf('%d,%d Tab /%s', __begin_ln, __end_ln, a:string)
    echo __exec 
    exec __exec
endfunction
```

说明：

- 依赖代码区块前后的空行来确定开始和结束的行号。
- 最新版见这里：https://github.com/cyy0523xc/code/blob/master/vim/cyy.vim 

