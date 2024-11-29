

## 企业网站架构部署

正式环境的动态网站部署

### LAMP架构

Linux + apache(nginx) + mysql + 后端程序(php/python)

1. nginx 提供静态资源展示，转发请求给后端程序
2. php/python代码检测，数据库中是否满足程序要求

![LAMP框架](https://img-blog.csdnimg.cn/direct/fd886b3131c3465b867eaf6f335d4df5.png)

### 快速部署LAMP

#### 安装apache

```bash
yum install httpd -y
```

#### 启动apache

```bash
systemctl start httpd
netstat -tunlp | grep httpd
```

#### 安装数据库mysql

```bash
yum install mariadb-server mariadb -y
```

#### 启动mysql

```bash
systemctl start mariadb
```

#### 验证mysql的端口

```bash
netstat -tunlp | grep "mysql"
```

#### 连接数据库

```bash
mysql -uroot -p
```

#### 使用数据库

```mysql
use mysql;
show tables;
```

查询user表的信息，mysql文件夹下，有一个user的execl表格

```mysql
select user,password,host from user;
```

### 部署php

1. 解决php安装的依赖开发环境
   
```bash
yum install -y zlib-devel libxml2-devel libjpeg-devel libjpeg-turbo-devel libiconv-devel freetype-devel libpng-devel gd-devel libcurl-devel libxslt-devel libtool-ltdl-devel pcre pcre-devel apr apr-devel zlib-devel gcc make
```

2. 安装php以及php连接mysql的驱动
```bash
yum install php php-fpm php-mysql -y
```

3. php不需要额外修改，修改apache配置文件，支持php的脚本

显示行号 :set nu

4. 编辑apache配置文件

```bash
vim /etc/httpd/conf/httpd.conf
```

119 DocumentRoot "/var/www/html"
120         TypesConfig /etc/mime.types
121         AddType application/x-httpd-php .php
122         AddType application/x-httpd-php-source
123         DirectoryIndex index.php index.html

5. 编辑php脚本，看apache是否能正确加载读取
   脚本的位置：

```bash
vim /var/www/html/index.php
```

6. 重启apache服务

## 搭建基于LAMP的论坛

### 部署论坛disuz

1. 下载discuz源码包

```bash
wget http://download.comsenz.com/DiscuzX/3.2/Discuz_X3.2_SC_UTF8.zip
```

以上出现“无法解析主机地址”，原因是该版本已经过期，不再更新维护，需要下载最新的版本。通过windows下载传入到centos7中，工具使用finalshell的文件下载上传功能。

2. 安装解压缩命令
   如果出现“下载yum源报错：正在解析主机 mirrors.aliyun.com (mirrors.aliyun.com)... 失败：未知的名称或服务。”，参考上面的解决方案重新备份和配置国内源

```bash
yum clean all
yum makecache
yum update -y
yum repolist all
```

```bash
yum install unzip -y
```

3. 解压缩出的upload文件，拷贝到apache的根目录下

```bash
cp -r upload/*  /var/www/html/
```

4. 给予最高权限

```bash
[root@localhost var]# chmod -R 777 /var/www/html/*
```

#### 访问apache首页，查看进入论坛安装页面

1. 出现php_version_too_low 问题，需要将php版本升级

[https://blog.csdn.net/weixin_43996664/article/details/90029625](PHP版本升级 5.4.16->7.2)

2. 出现文件权限不够问题，需要给予最高权限
3. 安装discuz时显示目录不存在和不可写
   [https://blog.csdn.net/qq_40965177/article/details/86612272](安装discuz时显示目录不存在和不可写)

```
selinux=0
```

4. 由于第3条修改了  "selinux="  导致了centos 7 启动时一直在加载页面，进不了系统
   [https://blog.csdn.net/gzg_123/article/details/123200846](centos 7 启动时一直在加载页面，进不了系统)
   处理开机页面的问题
5. 出现数据库连接失败
   查看是否启动mysql数据库服务

```bash
cd /etc/selinux/
vi config
```

#### 登录论坛

http://192.168.74.136/forum.php
