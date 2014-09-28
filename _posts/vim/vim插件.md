title: vim插件
tags:
  - vim
date: 2014-09-24 15:18:30
categories: record

---

## 华丽的powerline

下载插件：

```
cd ~/.vim/bundle/ 
git clone https://github.com/Lokaltog/vim-powerline 
```

修改vim的配置文件：vim ~/.vimrc，加入下面几行：

```
" powerline插件
set t_Co=256
let g:Powerline_symbols = 'unicode'
set encoding=utf8
```

效果如下：

![vim-powerline](/images/vim-powerline效果.jpg)
