title: shell记录
date: 2014-09-17 14:29:07
categories: record
tags:
- shell 

---

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


