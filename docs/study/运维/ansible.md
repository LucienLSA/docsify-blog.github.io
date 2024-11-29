

# ansible运维自动化
## 人工运维
运维人员早期需要维护数量众多的机器，需要反复重复的劳动力，很多机器需要同时部署相同的服务或是执行相同的命令，反复登录不同的机器，执行重复的动作。

在backup服务器配置rsync服务，进行数据同步，客户端单独安装一下rsync命令。可使用xshell等工具，创建免密ssh登录来完成自动化部署。

## 自动化运维
配置管理系统，远程执行命令，批量安装服务，启停服务等等。诞生了众多的开源软件，如fabric Ansible 

人力运维-自动化运维-数据化运维，可视化运维-AI智能运维

## Ansible介绍
ansible是一个同时管理多个远程主机的软件，必须任意通过ssh登录的机器，ansible可管理的机器有远程虚拟机、物理机和本地机器

ansible通过ssh协议实现管理节点、被管理节点的通信。只要通过ssh协议登录的主机，可完成ansible自动化部署操作。可进行批量文件分发、批量数据复制、批量数据修改、删除、批量自动化安装软件服务、批量服务启停、脚本化和自动批量服务部署

## Ansible特点
ansible编排引擎可出色完成各种任务配置管理，ansible流程控制、资源部署等方便很强大，ansible无须安装客户端软件，管理简洁，使用yaml配置文件语法。

python语言开发，有两个ssh处理模块。无需安装被管理节点的客户端，且无须占用其它客户端的其他端口，仅仅使用ssh服务即可。不用root用户也可执行，降低系统权限

## Ansible实践部署
### 虚拟机准备，配置在同一个局域网内，设置好静态ip地址
其中一台为管理机器（安装ansible的服务端），另外两台被管理机器（配置好ssh服务，以及关闭防火墙等等）

### 安装ansible管理机器
#### yum 安装自动化安装，预先需要配置好阿里云yum和epel源
```bash
yum install epel-release -y
yum install ansible libselinux-python -y
```

#### 检查软件安装情况，查询配置文件和可执行命令
```bash
rpm -ql ansible | grep -E '^/etc|^/usr/bin'
```

#### 检查ansible的版本
ansible --version

### ansible被管理机器
安装ansible所需要的系统模块
```bash
yum install epel-release libselinux-python -y
```

### ansible管理方式
#### 传统输入ssh密码验证
##### 配置ansible的配置文件，添加被管理机器的ip地址或主机名
1. 备份现有的配置文件
```bash
ls /etc/ansible
vim /etc/ansible/ansible.cfg
cp /etc/ansible/hosts{,.ori} # 或者也可写为
cp /etc/ansible/hosts  /etc/ansible/hosts.ori
```
2. 添加ansible需要管理的地址
```bash
vim /etc/ansible/hosts
```
在文件的最低行添加
```bash
[lucien]
被管理机器的ip地址1
被管理机器的ip地址2
```

##### ssh密码认证方式管理机器
ansible直接利用linux本地的ssh服务，进行远程的ssh操作，一般情况客户端的ssh服务默认是开启的，无须额外管理

1. 在管理机器上执行，-m指定功能模块，-a模块执行的参数，-k询问密码验证， -u指定运行的用户
```bash
ansible (lucien)主机列表名 -m command -a 'hostname' -k -u root
```

2. 报错信息如下
```bash
[root@localhost ansible]# ansible lucien -m command -a '192.168.74.147' -k -u root
SSH password: 
192.168.74.137 | FAILED | rc=-1 >>
Using a SSH password instead of a key is not possible because Host Key checking is enabled and sshpass does not support this.  Please add this host's fingerprint to your known_hosts file to manage this host.
```

解决方法：手动ssh对主机进行一次连接，即可使用ansible命令
首次连接会给出信息，并继续确认，输入登录密码
```bash
ssh root@ip地址
[root@localhost ansible]# ssh root@192.168.74.137
The authenticity of host '192.168.74.137 (192.168.74.137)' can't be established.
ECDSA key fingerprint is SHA256:XPv3CBncZEtLeMnGCiE3xUXPQWdxgbNdoveNgY8VLOQ.
ECDSA key fingerprint is MD5:32:82:d5:e4:ba:30:c4:8a:ee:54:91:53:80:2d:13:40.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '192.168.74.137' (ECDSA) to the list of known hosts.
root@192.168.74.137's password: 
Last login: Sun Jun 16 21:18:28 2024 from 192.168.74.1
```

检查ssh的host信息
```bash
vim ~/.ssh/known_hosts 
```

3. 可执行ansible命令操作，但每次需要输入ssh的认证密码，即root密码

#### 密钥管理
##### ansible自带的密码认证参数
在 /etc/ansible/hosts文件中，定义好密码，快速认证，远程管理主机

ansible_host 主机地址；ansible_port 端口；ansible_user 认证的用户； ansible_ssh_pass 用户认证的密码

使用hosts文件的参数形式
1. 修改hosts文件
```bash
[lucien]
ip地址1 ansible_user=root ansible_ssh_pass=密码
ip地址2 ansible_user=root ansible_ssh_pass=密码
```
2. 则不需要输入密码，可自动ssh验证通过
```bash
ansible lucien -m command -a "ip addr"
```

**注意处理在执行ansible命令操作时，如果出现以下情况**
```
192.168.226.128 | UNREACHABLE! => {
    "changed": false, 
    "msg": "Invalid/incorrect password: Permission denied, please try again.", 
    "unreachable": true
}
```
[https://blog.csdn.net/qq_42327944/article/details/125642404](Ansible报错：“msg“: “Invalid/incorrect password: Permission denied, please try again.“)
将密码修改为非0开头

##### ssh密钥方式批量管理主机
比host文件的密码参数更加安全，将以上修改的hosts文件内容进行注释
1. 在管理机器上创建ssh密钥
```bash
ssh-keygen -f ~/.ssh/id_rsa -P "" > /dev/null 2>&1 # 命令执行的正确信息和错误信息全部定向到黑洞文件中
```
2. 检查公私钥文件
```bash
cd ~/.ssh/
# authorized_keys id_rsa id_rsa.pub known_hosts
```
3. 编写密钥分发脚本
```bash
cd ~/.ssh/
mkdir /myssh
cd /myssh/
touch ssh_key_send.sh
vim ssh_key_send.sh
```
文件中写入
```
#!/bin/bash
rm -rf ~/.ssh/id_rsa*
ssh-keygen -f ~/.ssh/id_rsa -P "" > /dev/null 2>&1
SSH_Pass=密码
Key_Path=~/.ssh/id_rsa.pub
for ip in 批量分发的机器ip地址
do 
   sshpass -p$SSH_Pass ssh-copy-id -i $Key_Path "-o StrictHostKeyChecking=no" 192.168.226.$ip
done
# 非交互式分发公钥命令需要用sshpass指定SSH密码，通过-o StrictHostKeyChecking=no 跳过SSH连接确认信息
```
4. 执行脚本
```bash
cd /myssh/
sh ssh_key_send.sh
```
5. 测试
可免密登录
```bash
ssh -o 'StrictHostKeyChecking=no' '192.168.226.128'
```
不需要密码可直接执行命令，远程管理
```bash
ansible lucien -m command -a "df -h"
```

### ansible模式与命令
实现批量化主机管理的模式
- ansible纯命令行实现的批量管理，ad-hoc模式->简单的Shell命令管理
- ansible的playbook剧本来实现批量管理->复杂的shell脚本管理

#### ad-hoc模式
处理临时的，简单的任务，直接使用ansible的命令行进行操作，临时批量查看被管理机器的内存情况，cpu负载情况和网络情况，临时的分发配置文件

ansible-doc -l 列出ansible的模块及其功能
```bash
ansible-doc -l | grep ^command
```
查看某个模块的具体用法参数
```bash
ansible-doc -s command
```
##### command模块
远程节点上执行一个简单命令
```
chdir 在执行命令之前，先通过cd进入该参数指定的目录
creates 创建一个文件之前，判断该文件是否存在，存在则跳过，不存在则执行创建
free_form 该参数可输入任何系统的命令，实现远程执行和管理
removes 定义文件是否存在，存在则跳过，不存在则删除
```
不得出现shell变量$name，也不得出现特殊符号"><|;&"，若使用这些符号，则使用shell模块

1. 获取所有被管理机器的负载信息
```bash
ansible lucien -m command -a "uptime" 
```
2. 客户端先切换到/tmp目录，再打印当前的工作目录
```bash
ansible lucien -m command -a "pwd chdir=/tmp/"
```
3. creates参数
该参数作用是判断该文件是否存在，存在则跳过，不存在则执行
```bash
ansible 192.168.226.128 -m command -a "pwd creates=/opt"
```
4. removes参数
存在则执行，不存在则跳过
```bash
ansible lucien -a "ls /opt removes=/lucien"
```
5. warn参数
提供告警信息，执行命令且忽略该信息
```bash
ansible lucien -m command -a "chmod 000 /ect/hosts warn=False"
```

##### shell模块
远程机器上执行复杂命令
```
chdir 在执行命令之前，先通过cd进入该参数指定的目录
creates 创建一个文件之前，判断该文件是否存在，存在则跳过，不存在则执行创建
free_form 该参数可输入任何系统的命令，实现远程执行和管理
removes 定义文件是否存在，存在则跳过，不存在则删除
```
1. 批量查询进程信息
被管理机器上运行
```bash
vim xixixi.sh &
ps -ef | grep vim
```
管理机器上运行
```bash
ansible lucien -m shell -a "ps -ef | grep vim | grep -v grep"
```
2. 批量写入txt文本
管理机器上运行
```bash
ansible lucien -m shell -a "echo hello! > /tmp/heihei.txt"
```
可在被管理机器上查看到heihei.txt以及写入的内容
3. 批量执行脚本
执行的脚本在客户端机器上存在，否则报错文件不存在，有一个专门执行脚本的script模块
- 创建文件夹
- 创建sh脚本文件，写入脚本内容
- 赋予脚本可执行权限
- 执行脚本
- 忽略warning信息

在管理机器上远程批量化操作
```bash
ansible lucien -m shell -a "mkdir -p /server/myscripts/;echo 'hostname' > /server/myscripts/hostname.sh;chmod +x /server/myscripts/hostname.sh;bash /server/myscripts/hostname.sh  warn=False"
```

##### script模块
将管理机器上脚本远程传输到被管理节点上，模块参数
```bash
ansible-doc -s script
```
1. 在管理节点上创建脚本
```bash
mkdir -p /myscripts
cd /myscripts/
echo -e "pwd\nhostname" > /myscripts/local_hostname.sh
cat /myscripts/local_hostname.sh
bash /myscripts/local_hostname.sh
```
2. 授权
```bash
chmod +x /myscripts/local_hostname.sh
```
远程的批量执行脚本，在客户端上不需要存在该脚本
```bash
ansible lucien -m script -a "/myscripts/local_hostname.sh"
```
利用script模块可批量让所有被管理的机器执行脚本，脚本不需要在客户端上存在



#### playbook模式
针对具体且比较大的任务，需写好剧本，一键部署rsync备份服务器，部署lnmp环境
