

## nginx

### web服务器进而web框架的关系
1. web服务器(nginx)：接受HTTP请求并返回数据
2. web框架(django,flask)：开发web应用程序，处理接收到的数据 

### nginx安装环境(源代码编译安装)

1. 下载源代码
2. 系统上安装编译环境
3. 编译安装

#### 安装依赖环境
```bash
yum install gcc patch libffi-devel python-devel zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel openssl openssl-devel -y
``` 
#### 安装和启动nginx
1. 下载源码包
```bash
wget -c https://nginx.org/download/nginx-1.12.0.tar.gz
``` 
2. 解压缩源码
```bash
tar -zxvf nginx-1.12.0.tar.gz
``` 
3. 配置，编译安装  开启nginx状态监测功能

configure文件为可执行文件
```bash
./configure --prefix=/opt/nginx1-12/ --with-http_ssl_module --with-http_stub_status_module 

make && make install 
``` 
4. 启动nginx，进入sbin目录,找到nginx启动命令
```bash
cd /opt/nginx1-12/
cd sbin
./nginx #启动
./nginx -s stop #关闭
./nginx -s reload #重新加载
``` 
### nginx服务器与网站的配置
#### 静态网站配置

```bash
cd /opt/nginx1-12/conf
vim nginx.conf
```
nginx的网站配置区域
      location / {
         # nginx通过root指令，确定nginx的网页文件放在哪里
         # html为nginx安装目录下加的一个html文件夹
         root   html;
         # index参数指的是，首页文件的名字，文件名
         index  index.html index.htm;
      }

修改配置文件，重启这个程序，才能更新配置

root /opt/lucien/

需要创建该路径下的html文件，并添加新增的html内容
```bash
mkdir -p /opt/lucien
vim /opt/lucien/index.html
```

先验证配置语法是否正确
```bash
/opt/nginx1-12/sbin/nginx -t
```
```bash
/opt/nginx1-12/sbin/nginx -s reload
```

### 修改网站显示
#### curl命令

http网络请求，验证对方网站的信息

```bash
curl -I https://www.taobao.com/
```

查看淘宝网Web服务器信息，tengine

查看自己Web服务器的信息

```bash
curl -I 192.168.74.136:80
```

#### 启动和关闭nginx服务

```bash
systemctl stop nginx
```

#### 卸载nginx

```bash
yum remove nginx -y
```

#### 访问nginx服务页面

机器ip地址:80端口 需要关闭linux防火墙

```sh
## 关闭防火墙，下次启动仍会重启
sudo systemctl stop firewalld
```

```sh
## 关闭防火墙配置，下次重启已知生效
sudo systemctl disable firewalld
```


### 多虚拟主机解决的问题
一个nginx服务器下处理多个网站的内容，比如80端口和81端口

#### 只需要修改配置文件
理解conf文件中的
**nginx.conf的层级关系**
```
http{
   # 日志功能写在这里，对下面网站都适用   
   # 网站1
   server{

   }
   # 网站2
   server{

   }
}
```

```bash
cd /opt/nginx1-12/conf/
vim nginx.conf
```
出现server{}区域位置，表示一个网站。写多个server{}

**注意vim文件中的代码的缩进**

```
server{
   listen 81;
   server_name localhost;
   location / {  
      root /opt/ximei;
      index index.html;
   }
}
```

location /{}区域表示网站的访问路径，可以配置多个location{}，每个location{}表示一个访问路径。 root /opt/ximei 表示网站的根目录,在该目录中寻找html文件,index index.html;表示默认访问的文件。
#### 修改网站1和网站2的内容
1. vim /opt/lucien/index.html

2. vim /opt/ximei/index.html
需要先创建，再新增或修改html文件
```bash
mkdir -p /opt/ximei
vim /opt/ximei/index.html
```
重启nginx服务
```bash
/opt/nginx1-12/sbin/nginx -s reload
```

3. 修改nginx.conf文件
报错nginx: [emerg] "upstream" directive is not allowed here
(解决：nginx: [emerg] “upstream“ directive is not allowed here)[https://blog.csdn.net/A755967073/article/details/127942761]
(nginx: [emerg] "upstream" directive is not allowed here in .../.../.../*.conf)[https://www.cnblogs.com/haojile/p/12387568.html]

### 访问日志
nginx能记录用户的每一次访问请求

日志记录和分析可清晰掌握服务器的动态信息，比如安全。用户行为进行检测和分析。
- 对用户访问的次数、时间和频率进行记录
- 

```bash
vim /opt/nginx1-12/conf/nginx.conf
```
将配置文件中的多个虚拟主机的server{}的外层access_log和log_format main取消注释，该日志放在nginx安装目录下的logs文件夹中

access_log logs/host.access.log main;

启动nginx
```bash
cd /opt/nginx1-12/sbin
./nginx #启动
```
指定配置文件启动，注意软链接！
(指定配置文件启动)[https://blog.csdn.net/qq_36713022/article/details/104512737]



#### 持续检测日志内容变化（不断刷新文件的内容） tail -f命令
```bash
tail -f /opt/nginx1-12/logs/access.log
```

### nginx的代理服务

客户端 -> 代理(nginx) ->服务端    请求资源和返回资源

nginx只处理静态请求，动态请求转发给后端程序


#### 代理服务的配置

访问个人的linux机器中的nginx，却可拿到另一个网站的数据内容

修改配置文件
```bash
server{
        listen   81;
        server_name localhost;
        location / {
          proxy_pass https://www.bilibili.com/;
        }
 
}
```

![保护和隐藏原始服务器](https://img-blog.csdnimg.cn/direct/9d26aac3c9144440bedd117289b416bc.png)

