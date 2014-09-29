title: "shell-export及执行环境"
date: 2014-9-29
tags:
- shell 
- export 
category: shell 

---


## export 功能说明：设置或显示环境变量。

语　　法：export \[-fnp\] \[变量名称\]=\[变量设置值\]

补充说明：

- 在shell中定义的变量，通常只能在当前shell中有效，只有用export输出之后，才能在其fork的子shell中有效。
- 子shell中的变量无法被父shell读取，即使export也不行。

参　　数：

- -f 　代表\[变量名称\]中为函数名称。
- -n 　删除指定的变量。变量实际上并未删除，只是不会输出到后续指令的执行环境中。
- -p 　列出所有的shell赋予程序的环境变量。

用法：

```
export var
export -f func

export -fp 
```

## shell的命令类型 

### 1. 内置命令(Builtin)

shell 执行这些命令时不会派生新进程，而是由 shell 直接执行。比如 read, set, export 都是内置命令，这些命令需要用 help command 来查看其帮助信息。

### 2. 外部命令

外部命令就是普通的可执行二进制文件，shell 在执行它们时会 fork 出新进程(这是一个子 shell)，然后用 exec 系列函数来执行它们，这时候子 shell 的环境就被命令的环境所取代。

### 3. shell 脚本

在执行 shell 脚本时，shell 同样会先执行 fork 派生出子进程，然后使用 exec 来调用脚本解释程序(内核中会检查脚本中的第一行 #!/bin/xxx 来确定是调用哪一种)，然后将脚本装入，由它来解释执行。脚本解释器有很多，比如 bash, cshell, perl, python 等。如果被调出来的解释程序和当前 shell 是同一种 shell，那么它就是当前 shell 的子 shell，脚本中的命令都在子 shell 中执行，不会影响父 shell 的环境。

## 几种常见的形式 

### (  ) 和 {  } 中的指令组：

在 (  ) 和 {  } 中都可以内置一组指令。
(  ) 中的指令会在一个子 shell 中执行，命令执行结果不影响当前 shell。需要注意的是，$$ 代表当前 shell 进程的 PID，而不是子 shell 进程的 PID 。

{  } 中的指令在当前 shell 中执行，指令执行结果会影响当前的环境。

### 后台执行和异步执行

在一个 shell 脚本中将一个命令通过 & 放入后台执行，这个命令和当前 shell 的执行是并行的，当前 shell 会派生一个子 shell 执行这个后台命令，而自己则继续往下执行，两者并没有相互依赖及等待的关系，所以这是一种异步的执行方式。以下代码可以说明这一点：

### 命令替换

\`command\` 会将 command 命令的输出结果代换到当前的命令行。command 在子 shell 中执行，它的结果不会影响到当前 shell 。比较下面代码：

```
pwd
dir=`cd /tmp; pwd`
echo $dir
pwd
```

### 管道

对于 bash 来说\(dash，ash 等大部分 shell 也一样\)， **管道中的命令都是放在子shell里执行的** 。

```
a="hello world"

echo "$a"| (read var; echo "In subshell:$var")

# 管道中的命令是放在子 shell 里执行的，所以 var 得到的值无法传递到当前 shell ，所以这里要输出为空。
echo $var
```

## 相关资料

- http://www.groad.net/bbs/thread-3699-1-1.html
- http://www.cnblogs.com/hopeworld/archive/2011/09/21/2184576.html

