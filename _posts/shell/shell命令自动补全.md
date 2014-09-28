title: shell命令自动补全
tags:
  - shell 
date: 2014-09-22 15:02:13
categories:

---

代码如下：

```bash
# github相关操作 
github() {
    cd_github_code
    my_github=git@github.com/cyy053xc/

    case $1 in 
        "c"|"clone")
            git clone $my_github$2".git"
            ;;
        "l"|"pull")
            pushd $my_github$2
            git pull
            popd
            ;;
        "s"|"push")
            pushd $my_github$2
            git pull 
            git commit -am 'script commit'
            git push 
            popd
            ;;
        "h"|"help"|*)
            cat <<EOF
github [hcls] [path] 

usage:
h|help               : help
c|clone  project     : git clone {$my_github}project.git
l|pull   project     : git pull 
s|push   project     : git push 
EOF
            ;;
    esac
}

# 补全函数
function _github() {
    COMPREPLY=()
    local cur=${COMP_WORDS[COMP_CWORD]};
    local com=${COMP_WORDS[COMP_CWORD-1]};
    case $com in
        'github')
            COMPREPLY=($(compgen -W 'c clone l pull s push h help' -- $cur))
            ;;
        'compile')
            local pro=($(awk '{print $1}' project.list))
            COMPREPLY=($(compgen -W '${pro[@]}' -- $cur))
            ;;
        *)
            ;;
    esac
    return 0
}

# 绑定自动补全函数
complete -F _github github 

```

从效果上，可以说已经实现了tab键自动补全，不过不是很完美：

- 每个函数需要搭配一个额外的补全函数
- 补全函数的实现有大量的重复代码
- 另外还需要一个额外的命令进行绑定

理想的应该是：在函数的内部加上一条命令或者一个配置来解决。
