title: 使用git来管理nginx的站点配置文件 
date: 2015-5-12 
tags: 
    - git 
    - nginx 

--- 

在某次在服务器上rm造成大麻烦之后，决心使用git来对nginx的站点配置文件进行统一的版本管理。


```
说明：测试机上的配置文件的后缀为.conf

为了规范，命名规则为：域名.conf，例如yjq.ibbd.net.conf
```

<!-- more -->

## 在ubuntu上的使用

#### 第一步：修改nginx.conf

在ubuntu上，通过apt-get安装的nginx，配置文件通常在/etc/nginx这个目录下，关键配置文件是nginx.conf，里面有一句：

```
include /etc/nginx/sites-enabled/*.conf;
```

如果这里加载的不是.conf这个后缀的，需要手动修改。（其实也可以不修改的）

#### 第二步：在sites-available这个目录配置git 

```
cd /etc/nginx/sites-available/

sudo git init
sudo git remote add origin git@git.ibbd.net:ibbd/nginx-conf.git
sudo git pull origin master
```

说明：


#### 第三步：把需要的配置文件链接到 sites-enabled 目录

```
ln /etc/nginx/sites-available/test.com.conf /etc/nginx/sites-enabled/
```

