

# 运维的监控
使用监控系统查看服务器状态以及网站流量指标，利用监控系统数据去了解上线发布的结构和网站的健康状态。监控软件ZABBIX

完善的监控系统
1. 监控系统能自定义监控的内容，脚本采集所需的数据
2. 数据需要存入到数据库，对数据进行分析计算
3. 监控系统简易快速地部署到服务器
4. 数据可视化直观清晰

## 监控体系
硬件监控、系统监控、服务监控、性能监控、日志监控、安全监控、网络监控
### 监控生命周期
1. 服务器上架到机柜
2. 基础设施监控
服务器温度，风扇转速 ，ipmitool命令，只能用在物理机，存储监控（df，fdisk，iotop），cpu(lscpu，uptime,top,htop,glances)，内存情况(free)，网络情况(iftop)
3. 应用监控
mysql redis nginx  php-fpm python

## ZABBIX介绍和安装
ZABBIX是一种网络监视、管理系统，基于Server-Client架构，监视网络服务、服务器和网络机器等状态。

ZABBIX可只使用Simple Check 不需要安装Client端，可基于SMTP或HTTP等各种协议定制监视。UNIX或Windows安装ZABBIX Agent之后，可监视CPU load、网络使用情况、硬盘容量等状态。若无安装Agent在监视对象中，ZABBIX可由SNMP TCP ICMP 利用IPMI SSH telnet对目标进行监视

ZABBIX可满足监控需求
1. 支持自定义监控脚本，提供需要输出的值
2. ZABBIX存储的数据库表结构稍复杂但逻辑清晰
3. ZABBIX存在模块，方便将一组监控进行部署
4. ZABBIX的每一个item监控项，可看到历史记录，对web界面友好
5. 触发器定义规则
6. ack报警确认机制
7. 支持短信邮件和微信告警
8. 触发告警后，远程执行系统命令
9. ZABBIX自带的PHP绘图

### 部署ZABBIX5.0
#### 初始化
```bash
[root@localhost ~]# ifconfig ens33 | awk 'NR==2{print $2}'
192.168.226.129
```
#### 关闭防火墙
```bash
sed -i 's/SELINUX=enforcing/SELINUX=disable/' /etc/selinux/config
systemctl disable --now firewalld
```
#### 获取ZABBIX的下载源
```bash
rpm -Uvh https://mirrors.aliyun.com/zabbix/zabbix/5.0/rhel/7/x86_64/zabbix-release-5.0-1.el7.noarch.rpm

ls /etc/yum.repos.d/
vim /etc/yum.repos.d/zabbix.repo
```
#### 更换zabbix.repo源阿里
```bash
sed -i 's#http://repo.zabbix.com#https://mirrors.aliyun.com/zabbix#' /etc/yum.repos.d/zabbix.repo
vim /etc/yum.repos.d/zabbix.repo
```

#### 清空缓存，下载zabbix服务端
```bash
yum clean all
yum makecache
yum install zabbix-server-mysql zabbix-agent  -y
```

#### 安装工具，在多个机器上使用多个版本的软件，并不会影响到整个系统的依赖环境
安装Software Collections 便于安装高版本的php，默认yum安装的php版本过低。SCL可让你在同一个操作系统上安装和使用多个版本的软件，而不会影响整个系统的安装

软件包安装在/opt/rh目录，为避免系统其他引起冲突

```bash
yum install centos-release-scl -y
```
#### 修改zabbix-front前端源，修改参数
```bash
vim /etc/yum.repos.d/zabbix.repo
```
```
[zabbix-frontend]
name=Zabbix Official Repository frontend - $basearch
baseurl1=https://mirrors.aliyun.com/zabbix/zabbix/5.0/rhel/7/$basearch/frontend
enabled=1 # 开启参数
gpgcheck=1
gpgkey=file://etc/pki/rpm-gpg/RPM-GPG-KEY-ZABBIX-A14FE591
```
#### 安装zabbix前端环境，且是安装到scl环境下
```bash
yum install zabbix-web-mysql-scl zabbix-apache-conf-scl -y
```
#### 安装zabbix所需要的数据库
```bash
yum install mariadb-server -y
```
#### 配置数据库开机启动
```bash
systemctl enable --now mariadb
```
#### 初始化数据库，设置密码
```bash
systemctl status mariadb
netstat -tunlp
mysql_secure_installation

NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MariaDB
      SERVERS IN PRODUCTION USE!  PLEASE READ EACH STEP CAREFULLY!

In order to log into MariaDB to secure it, we'll need the current
password for the root user.  If you've just installed MariaDB, and
you haven't set the root password yet, the password will be blank,
so you should just press enter here.

Enter current password for root (enter for none): 
OK, successfully used password, moving on...

Setting the root password ensures that nobody can log into the MariaDB
root user without the proper authorisation.

Set root password? [Y/n] y
New password: 
Re-enter new password: 
Password updated successfully!
Reloading privilege tables..
 ... Success!


By default, a MariaDB installation has an anonymous user, allowing anyone
to log into MariaDB without having to have a user account created for
them.  This is intended only for testing, and to make the installation
go a bit smoother.  You should remove them before moving into a
production environment.

Remove anonymous users? [Y/n] y
 ... Success!

Normally, root should only be allowed to connect from 'localhost'.  This
ensures that someone cannot guess at the root password from the network.

Disallow root login remotely? [Y/n] n
 ... skipping.

By default, MariaDB comes with a database named 'test' that anyone can
access.  This is also intended only for testing, and should be removed
before moving into a production environment.

Remove test database and access to it? [Y/n] y
 - Dropping test database...
 ... Success!
 - Removing privileges on test database...
 ... Success!

Reloading the privilege tables will ensure that all changes made so far
will take effect immediately.

Reload privilege tables now? [Y/n] y
 ... Success!

Cleaning up...

All done!  If you've completed all of the above steps, your MariaDB
installation should now be secure.

Thanks for using MariaDB!
```

#### 添加数据库用户，zabbix所需的数据库信息
```bash
mysql -uroot -p
```
```mysql
create database zabbix character set utf8 collate utf8_bin;
create user zabbix@localhost identified by 'lucien';
grant all privileges on zabbix.* to zabbix@localhost;
flush privileges;
```

**当忘记了mysql中普通用户的密码，需要进行修改**
[https://blog.csdn.net/dufufd/article/details/54943836](centos7修改mysql中用户的密码)
#### 使用zabbix-mysql命令导入数据库信息
musql -u用户名 -p 数据库名
```bash
ls /usr/share/doc/zabbix-server-mysql*/create.sql.gz
zcat /usr/share/doc/zabbix-server-mysql*/create.sql.gz | mysql -uzabbix -p zabbix
mysql -uzabbix -p0723
```
```mysql
show databases;
use zabbix;
show tables;
```
#### 修改zabbix-server配置文件，修改数据库密码
```bash
vim /etc/zabbix/zabbix_server.conf
grep '^DBPa' /etc/zabbix/zabbix_server.conf
```
#### 修改zabbi的php配置文件
```bash
vim /etc/opt/rh/rh-php72/php-fpm.d/zabbix.conf
```
```
php_value[date.timezone] = Asia/Shanghai
```
```bash
grep 'timezone' /etc/opt/rh/rh-php72/php-fpm.d/zabbix.conf
```
#### 启动zabbix相关服务
```bash
systemctl start zabbix-server zabbix-agent httpd rh-php72-php-fpm
systemctl enable zabbix-server zabbix-agent httpd rh-php72-php-fpm
```

**Centos 安装killall命令**
```bash
yum install psmisc
```

**ERROR: unable to bind listening socket for address ‘192.168.0.107:9000’: Address already in use (98)，ERROR: FPM initialization failed**
杀死进程，重启php-fpm
[https://blog.csdn.net/xu710263124/article/details/117465588](解决启动PHP报错：ERROR: unable to bind listening socket for address ‘127.0.0.1:9000‘: Address already i)

访问地址 http://192.168.226.129/zabbix/

默认账号密码
Admin zabbix

### 部署zabbix客户端
拖到仪表盘，更改语言

agent2采用golang语言开发的客户端，多核性能特别高。agent2默认用10050端口，即zabbix客户端的端口。新版本为zabbix-agent2

#### 时间正确对应（服务端和客户端）
```bash
yum install ntpdate -y
ntpdate -u ntp.aliyun.com
```

#### 时区的统一配置
```bash
mv /etc/localtime{,.bak}
ln -s /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```

#### 部署流程
1. 提前配置好zabbix的yum源，参考服务端的配置
```bash
rpm -Uvh https://mirrors.aliyun.com/zabbix/zabbix/5.0/rhel/7/x86_64/zabbix-release-5.0-1.el7.noarch.rpm

sed -i 's#http://repo.zabbix.com#https://mirrors.aliyun.com/zabbix#' /etc/yum.repos.d/zabbix.repo

yum install zabbix-agent2 -y 
```
2. 查看配置文件
```bash
vim /etc/zabbix/zabbix_agent2.conf
ls -l  /usr/sbin/zabbix_agent2
```
3. 启动命令
```bash
systemctl enable --now zabbix-agent2
netstat -tnlp | grep zabbix
ls /lib/systemd/system/zabbix-agent2.service
cat /lib/systemd/system/zabbix-agent2.service
```
4. 修改agent2配置文件，查看配置信息
```bash
grep -Ev '^#|^$' /etc/zabbix/zabbix_agent2.conf
cat /var/run/zabbix/zabbix_agent2.pid
ps -ef | grep zabbix2
vim /etc/zabbix/zabbix_agent2.conf
```
修改Server和ServerActive为服务端机器的主机192.168.226.129

修改客户端机器的hostname为客户端机器的hostname
```bash
hostnamectl set-hostname zbx-agent01
```
重启zabbix-agent2
#### 验证zabbix-agent2的连通性
1. 服务端通过命令获取数据
```bash
zabbix_get -s '192.168.226.128' -p 10050 -k 'agent.ping'
zabbix_get -s '192.168.226.128' -p 10050 -k 'system.hostname'
```
**zabbix_get 找不到命令是因为没有安装上zabbix_get**
```bash
yum install zabbix-get.x86_64
```

### 修改zabbix图形乱码问题
对仪表盘所存在的问题进行分析

语言修改后存在小方括号的乱码问题

#### 安装字体
```bash
yum -y install wqy-microhei-fonts
```
#### 复制字体
```bash
\cp /usr/share/fonts/wqy-microhei/wqy-microhei.ttc /usr/share/fonts/dejavu/DejaVuSans.ttf
```

### 添加zabbix-agent主机
配置栏选择添加主机

主机群组为Linux server，模板链接为Template OS Linux by Zabbix agent

在最新数据中选择添加的zabbix-agent客户端
