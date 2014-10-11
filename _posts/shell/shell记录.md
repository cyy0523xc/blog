title: shell记录
date: 2014-10-11 
categories: record
tags:
- shell 

---

# 粘贴到行首

使用大写 **P** 

# 在某个目录下查找包含某个字符串的文件

```
find . | xargs grep -ri "some string"
```

### xargs命令

作用：将参数列表转换成小块分段传递给其他命令，以避免参数列表过长的问题。

```
rm `find /path -type f`

# 如果path目录下文件过多就会因为“参数列表过长”而报错无法执行。但改用xargs以后，问题即获解决。

find /path -type f -print0 | xargs -0 rm 

# xargs将find产生的长串文件列表拆散成多个子串，然后对每个子串调用rm。

```

# 查找指定时间范围的文件

```
find ./*.jpg -newermt "2013-10-28" ! -newermt "2013-10-29"
```

# 字符串参与条件判断或者计算时，必须加上双引号

```
if [ -n "$string" ]; 

string="test  test2"
your_func $string      # 错误：函数会接收到两个参数
your_func "$string"    # 正常：函数正常接收到一个参数
```

注意： **shell会把字符串按照IFS进行分割处理**

# 多行字符串：IFS换行符

```
ifs=$IFS 
IFS=$`\n`              # 注意这里，不能使用: IFS="\n" or IFS='\n'
config=`cat <<EOF
output: .user.js

dese:
// ==UserScript==
// @name         Userscript
// @namespace    http://cyy0523xc.github.io/
// @version      0.1
// @description  something 
// @match        http://*/*
// @copyright    2014, Alex(cyy0523xc@gmail.com)
// ==/UserScript==
    
input: 
- 
   
EOF`

echo $config
IFS=$ifs 

```

# 批量重命名文件

```bash
rename 's/201[34]\-[01][0-9]\-[012][0-9]\-//' *.md
```

# 期待一元表达式

异常信息：

```
bash: [: h: 期待一元表达式]
```

对应语句：

```shell 
if [ "h" = $1 ]

# 如果变量未定义的话，则报错，应改成：

if [ "h" = "$1" ]
```


