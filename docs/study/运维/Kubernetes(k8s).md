

# 入门认识
## k8s基本介绍
本质上为应用服务和服务器之间的中间层，通过暴露一系列的API能力，简化服务的部署运维流程

介于应用服务和服务器之间，通过策略协调和管理多个应用服务，只需要一个yaml文件配置，定义应用的部署顺序等信息，自动部署应用到各个服务器上，自动扩缩容。

### 控制平面control plane
负责控制和管理各个node

#### 内部组件
以前需要登录到每台服务器，手动执行各种命令，调用K8S提供的API接口，即可操作这些服务资源，这些接口是由api-server组件提供

以前需要看哪台服务器的cpu和内存资源充足后才能部署应用，由schedule调度器来完成；
找到服务器后，以前会手动创建关闭服务，由control manager控制管理器负责Node的创建和关闭服务，将以上的数据保存到存储层，需要一个etcd


### 工作节点Node
Node是实际工作节点，它既可以是裸机服务器，也可以是虚拟机，负责实际运行各个应用服务，多个应用服务共享一台Node上的内存和cpu等计算资源

#### 内部组件
以前需要上传代码到服务器，只需要将服务代码打包container image容器镜像，则可一行命令将其部署。为了下载和部署镜像，Node中有一个container runtime为容器运行时的组件。

每个应用服务可认为是container，为应用服务搭配一个日志收集器container或监控采集器container，多个container共同组成一个一个pod，在node上运行。k8s可以将pod从某个node调度到另一个node，还能以pod为单位做重启和动态扩缩容的操作。因此pod是k8s中最小的调度单位

组件kubelet主要负责管理和监控pod，接收来自控制平面的control manager的创建和关闭node服务

kube proxy负责node的网络通信，外部请求能被转发到pod中


### cluster集群（控制平面+Node）
一般会有多个集群，测试环境用一个集群，生产环境用一个集群，为了将集群内部的服务暴露给外部用户使用。部署一个入口控制器ingress控制器，让外部用户访问集群内部服务

### kubectl
执行命令，内部可调用k8s和API

### 部署服务
#### 编写yaml文件
定义pod用到哪些镜像，占用多少内存和cpu等信息。使用kubectl命令行执行kubectl apply -f xx.yaml。kubectl将读取和解析的yaml文件，将解析后的对象通过API请求发送给k8s的控制平面的API server。API server会根据要求驱使scheduler通过etcd提供的数据寻找合适的node，再让controller manager控制node创建服务，node内部的kubelet在收到命令后会基于container runtime组件去拉取镜像，创建容器，最终完成pod创建。

只需写一遍yaml文件和执行一次kubectl命令

### 调用服务
以前直接在浏览器发送http请求达到服务器上的nginx，再转发到部署的服务内。

k8s下，外部请求会先到达k8s集群的ingress控制器，然后请求会被转发到k8s内部某个node的kube proxy上，再找到对应的pod，再转发到容器内部的服务中，处理结果原路返回，即完成一次服务调用。

# 黑马教程
## 学习K8S之前
### 容器
#### 使用容器目的
为了降低虚拟机造成物理主机资源的浪费，提高物理主机的资源利用率，提供像虚拟机一样良好的应用程序隔离运行环境，这种轻量级的虚拟机称为容器

#### 容器管理工具
容器的创建、启动、关闭和删除
1. docker
2. Pouch
3. LXC LXD RKT

#### 容器编排部署工具
容器管理工具可完成容器的基础管理，其应用并不是只能进行简单应用部署，完成复杂的应用部署，需要更复杂工具进行编排

1. docker machine /docker compose/ docker swarm 
2. Mesos和Marathon
3. kubernetes
管理云平台上多个主机上的容器化应用，k8s让部署容器化的应用简单并高效，提供应用部署、规划、更新和维护

### K8S认识
由来，版本，用户

## k8s功能
轻便和可扩展的，用于管理容器化应用和服务，通过k8s能进行自动化部署和扩缩容。在k8s中组成应用的容器组合成一个逻辑单元易于管理和发现。
### 自动装箱
基于容器对应用运行环境的资源配置要求自动部署应用容器

### 自我修复（自愈能力）
1. 容器失败，对容器重启
2. 部署node节点有问题，会对容器进行重新部署和重新调度
3. 未通过监控检查时，关闭此容器
4. 直到容器正常运行时，才会对外提供服务

### 水平扩展
简单命令、用户界面或者cpu等资源使用情况，对应用容器进行规模扩大和裁剪

### 服务发现
用户不需要额外的服务发现机制，基于k8s自身能力实现服务发现和负载均衡

### 滚动更新
根据应用变化，对容器运行的应用进行一次性或批量式地更新

### 版本退回
应用部署情况，对应用容器运行地应用，进行历史版本即时回退

### 密钥和配置管理
不需要重新构建镜像情况下，部署和更新密钥以及应用配置

### 存储编排
自动实现存储系统挂载和应用，对状态应用实现数据持久化非常重要。存储系统可来自于本地目录、网络存储和公共云服务

### node和pod支持
可管理节点数为2000，可管理pod数为150000

## k8s架构
### 场景
对应用运行基础环境迁移，由原先地虚拟机环境迁移到k8s集群环境中，应对开发快速部署和快速测试的需要

### 应用部署架构分类
#### 无中心节点架构
集群中所有的主机之间彼此称为伙伴关系

#### 有中心节点架构
node

### k8s集群架构节点角色功能
#### master node
1. k8s集群控制节点，对集群进行调度管理，接受集群外用户去集群操作请求
2. master node 由API Server/ Scheduler/ Cluster State Store(ETCD数据库)和Controller Manager Server所组成

#### worker node
1. 集群工作节点，运行用户业务应用容器
2. 部署集群应用包括kubelet/ kube proxy/ container runtime

## k8s集群部署
### k8s集群部署工具
#### 二进制源码包部署
#### kubeadm部署

### kubeadm部署kubernetes集群
使用kubeadm部署master节点k8s集群

master主机和worker主机

#### 主机名修改
```bash
hostnamectl set-hostname master1
hostname
```

#### 主机IP地址
查看
```bash
yum install net-tools 
ip addr
cat /etc/sysconfig/network-scripts/ifcfg-ens33
vim /etc/sysconfig/network-scripts/ifcfg-ens33
```

##### 静态ip地址设置

[https://blog.csdn.net/weixin_43989347/article/details/123150816](Centos7 配置静态IP地址)

##### 主机名称解析
```bash
cat /etc/hosts
vi /etc/hosts
```
添加内容
```
192.168.226.100 master1
192.168.226.101 worker1
```

#### 主机安全配置
##### 关闭firewalld
```bash
systemctl stop firewalld
systemctl disable firewalld
firewall-cmd --state
```

##### SELINUX配置
```bash
cat /etc/selinux/config
sed -ri 's/SELINUX=enforcing/SELINUX=disabled/' /etc/selinux/config
reboot
getenforce
```

#### 主机时间同步
```bash
yum -y install ntpdate
crontab -e
```
```
0 */1 * * * ntpdate time1.aliyun.com
```
```bash
ntpdate time1.aliyun.com
```

#### 永久关闭swap分区
```bash
cat /etc/fstab
vim /etc/fstab
```
```
注释
# /dev/mapper/centos-swap swap                    swap    defaults        0 0
```
重启系统reboot

#### 添加网桥过滤
1. 添加网桥过滤及地址转发
```bash
cat /etc/sysctl.d/k8s.conf
vim /etc/stsctl.d/k8s.conf
```
```
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
vm.swappiness = 0
```

2. 加载br_netfilter模块
```bash
modprobe br_netfilter
lsmod | grep br_netfilter
```
3. 加载网桥过滤配置文件
```bash
sysctl -p /etc/sysctl.d/k8s.conf
```

#### 开启ipvs
1. 安装ipset和ipvsadm
```bash
yum -y install ipset ipvsadm
```
2. 在所有节点执行脚本
添加所需加载的模块
```bash
cat > /etc/sysconfig/modules/ipvs.modules << EOF
#!/bin/bash
modprobe -- ip_vs
modprobe -- ip_vs_rr
modprobe -- ip_vs_wrr
modprobe -- ip_vs_sh
modprobe -- nf_conntrack_ipv4
EOF
```
检查
```bash
ls /etc/sysconfig/modules/
ll /etc/sysconfig/modules/
```
授权、运行、检查是否加载
```bash
chmod 755 /etc/sysconfig/modules/ipvs.modules && bash /etc/sysconfig/modules/ipvs.modules && lsmod | grep -e ip_vs -e nf_conntrack_ipv4
```

### 在manager节点及worker节点，安装指定版本的docker-ce
#### yum获取
使用清华镜像源，官方提供的镜像源很慢
```bash
wget -O /etc/yum.repos.d/docker-ce.repo https://mirrors.tuna.tsinghua.edu.cn/docker-ce/linux/centos/docker-ce.repo

ls /etc/yum.repos.d/

sudo sed -i 's+download.docker.com+mirrors.aliyun.com/docker-ce+' /etc/yum.repos.d/docker-ce.repo
```
#### 查看docker-ce版本
```bash
yum list docker-ce.x86_64 --showduplicates | sort -r
yum list docker-ce.x86_64 --show-duplicates
```
#### 安装指定docker-ce版本
此版本不需要修改服务启动文件及iptables默认规则链策略
```bash
yum -y install --setopt=obsoletes=0 docker-ce-18.06.3.ce-3.el7
docker version
```
启动docker
```bash
systemctl enable docker
systemctl start docker
```
#### 修改docker-ce服务配置文件
为后续使用/etc/docker/daemon.json进行更多配置
```bash
cat /usr/lib/systemd/system/docker.service
vim /etc/docker/daemon.json
```
```
{
"exec-opts": ["native.cgroupdriver=systemd"]
}
```
```bash
systemctl status docker
```

## 部署软件和配置
### 软件安装
1. kubeadm 
初始化集群、管理集群
2. kubelet
接收api-server指令，对pod生命周期进行管理
3. kubectl
集群命令行管理工具

#### 阿里云yum源
```bash
vim /etc/yum.repos.d/k8s.repo
```
```
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg
       https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
```
```bash
yum list | grep kubeadm
```
```
导入 GPG key 0x13EDEF05:
 用户ID     : "Rapture Automatic Signing Key (cloud-rapture-signing-key-2022-03-07-08_01_01.pub)"
 指纹       : a362 b822 f6de dc65 2817 ea46 b53d c80d 13ed ef05
 来自       : https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg
y
kubeadm.x86_64                              1.28.2-0                   kubernetes
```
```bash
scp /etc/yum.repos.d/k8s.repo worker1:/etc/yum.repos.d/
```
[root@master1 ~]# scp /etc/yum.repos.d/k8s.repo worker1:/etc/yum.repos.d/
The authenticity of host 'worker1 (192.168.226.101)' can't be established.
ECDSA key fingerprint is SHA256:N5abiLUHFK0XD3Q4hoIdHlsq6KaKJWpOnHbXcrNlBgg.
ECDSA key fingerprint is MD5:ec:c0:bb:c3:51:a0:4a:66:a0:ff:8a:71:d2:b8:44:ea.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'worker1,192.168.226.101' (ECDSA) to the list of known hosts.
root@worker1's password: 
k8s.repo                                                            100%  283   210.8KB/s   00:00 

#### 安装指定版本kubeadm kubelet kubectl
```bash
yum list kubeadm.x86_64 --showduplicates | sort -r

yum -y install --setopt=obsoletes=0 kubeadm-1.17.2-0 kubelet-1.17.2-0 kubectl-1.17.2-0
```

### 软件设置
主要配置kubelet

为实现docker使用的cgroupdriver与kubelet使用的cgroup的一致性，修改
```bash
vim /etc/sysconfig/kubelet
```
```
KUBULET_EXTRA_ARGS="--cgroup-driver=systemd"
```
```bash
systemctl status kubelet
systemctl enable kubelet
```

## k8s集群容器镜像准备
集群所有核心组件均以pod运行，需要为主机准备镜像，不同角色主机准备不同的镜像
### master主机镜像
查看集群使用的容器镜像
```bash
kubeadm config images list
```

### 列出镜像列表到文件
```bash
kubeadm config images list >> image.list
cat image.list 
vim image.list
```

### 修改镜像列表文件
脚本文件
```sh
#!/bin/bash
img_list='
k8s.gcr.io/kube-apiserver:v1.17.2
k8s.gcr.io/kube-controller-manager:v1.17.2
k8s.gcr.io/kube-scheduler:v1.17.2
k8s.gcr.io/kube-proxy:v1.17.2
k8s.gcr.io/pause:3.1
k8s.gcr.io/etcd:3.4.3-0
k8s.gcr.io/coredns:1.6.5'

for img in ${img_list}
do
    docker pull $img

done
```
执行脚本文件
```bash
sh image.list
docker images
```


关于镜像拉不下来的问题   
    注意：k8s.gcr.io 国内无法访问，只能通过更换镜像源方式 k8s.gcr.io 更换成  registry.aliyuncs.com/google_containers
```sh 
#!/bin/bash
image_list='registry.aliyuncs.com/google_containers/kube-apiserver:v1.17.2
registry.aliyuncs.com/google_containers/kube-controller-manager:v1.17.2
registry.aliyuncs.com/google_containers/kube-scheduler:v1.17.2
registry.aliyuncs.com/google_containers/kube-proxy:v1.17.2
registry.aliyuncs.com/google_containers/pause:3.1
registry.aliyuncs.com/google_containers/coredns:1.6.5
registry.aliyuncs.com/google_containers/etcd:3.4.3-0'

for img in ${image_list}
do
        docker pull $img
done

```

跟换节点tag保持和k8s.gcr.io一样
```sh
docker tag registry.aliyuncs.com/google_containers/kube-apiserver:v1.17.2 k8s.gcr.io/kube-apiserver:v1.17.2
docker tag registry.aliyuncs.com/google_containers/kube-controller-manager:v1.17.2 k8s.gcr.io/kube-controller-manager:v1.17.2
docker tag registry.aliyuncs.com/google_containers/kube-scheduler:v1.17.2 k8s.gcr.io/kube-scheduler:v1.17.2
docker tag registry.aliyuncs.com/google_containers/kube-proxy:v1.17.2 k8s.gcr.io/kube-proxy:v1.17.2
docker tag registry.aliyuncs.com/google_containers/pause:3.1 k8s.gcr.io/pause:3.1
docker tag registry.aliyuncs.com/google_containers/etcd:3.4.3-0 k8s.gcr.io/etcd:3.4.3-0
docker tag registry.aliyuncs.com/google_containers/coredns:1.6.5 k8s.gcr.io/coredns:1.6.5
```

#### docker镜像删除 
```sh
docker rmi IMAGE ID 前三个字符即可
```


### worker 主机镜像
将master主机镜像中的保存为tar，复制到worker节点
```sh
docker save -o kube-p.tar registry.aliyuncs.com/google_containers/kube-proxy:v1.17.2
ls
docker save -o p.tar registry.aliyuncs.com/google_containers/pause:3.1
ls
```
复制tar到worker节点
```sh
scp kube-p.tar p.tar worker1:/root
```

在worker1中加载复制过来的镜像
```sh
docker load -i kube-p.tar
docker load -i p.tar
docker images
```

## k8s集群初始化
### master节点
默认从k8s.gcr.io拉取镜像，而这个在国内是访问不了的，所以要指定国内镜像
```sh
kubeadm init --kubernetes-version=v1.17.2 --pod-network-cidr=10.244.0.0/16 --image-repository registry.aliyuncs.com/google_containers --apiserver-advertise-address=192.168.226.100
```
#### 初始化过程
```
[root@master1 ~]# kubeadm init --kubernetes-version=v1.17.2 --pod-network-cidr=10.244.0.0/16 --image-repository registry.aliyuncs.com/google_containers --apiserver-advertise-address=192.168.226.100
W0630 16:46:00.232032   21856 validation.go:28] Cannot validate kube-proxy config - no validator is available
W0630 16:46:00.232172   21856 validation.go:28] Cannot validate kubelet config - no validator is available
[init] Using Kubernetes version: v1.17.2
[preflight] Running pre-flight checks
[preflight] Pulling images required for setting up a Kubernetes cluster
[preflight] This might take a minute or two, depending on the speed of your internet connection
[preflight] You can also perform this action in beforehand using 'kubeadm config images pull'
[kubelet-start] Writing kubelet environment file with flags to file "/var/lib/kubelet/kubeadm-flags.env"
[kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[kubelet-start] Starting the kubelet
[certs] Using certificateDir folder "/etc/kubernetes/pki"
[certs] Generating "ca" certificate and key
[certs] Generating "apiserver" certificate and key
[certs] apiserver serving cert is signed for DNS names [master1 kubernetes kubernetes.default kubernetes.default.svc kubernetes.default.svc.cluster.local] and IPs [10.96.0.1 192.168.226.100]
[certs] Generating "apiserver-kubelet-client" certificate and key
[certs] Generating "front-proxy-ca" certificate and key
[certs] Generating "front-proxy-client" certificate and key
[certs] Generating "etcd/ca" certificate and key
[certs] Generating "etcd/server" certificate and key
[certs] etcd/server serving cert is signed for DNS names [master1 localhost] and IPs [192.168.226.100 127.0.0.1 ::1]
[certs] Generating "etcd/peer" certificate and key
[certs] etcd/peer serving cert is signed for DNS names [master1 localhost] and IPs [192.168.226.100 127.0.0.1 ::1]
[certs] Generating "etcd/healthcheck-client" certificate and key
[certs] Generating "apiserver-etcd-client" certificate and key
[certs] Generating "sa" key and public key
[kubeconfig] Using kubeconfig folder "/etc/kubernetes"
[kubeconfig] Writing "admin.conf" kubeconfig file
[kubeconfig] Writing "kubelet.conf" kubeconfig file
[kubeconfig] Writing "controller-manager.conf" kubeconfig file
[kubeconfig] Writing "scheduler.conf" kubeconfig file
[control-plane] Using manifest folder "/etc/kubernetes/manifests"
[control-plane] Creating static Pod manifest for "kube-apiserver"
[control-plane] Creating static Pod manifest for "kube-controller-manager"
W0630 16:46:11.585488   21856 manifests.go:214] the default kube-apiserver authorization-mode is "Node,RBAC"; using "Node,RBAC"
[control-plane] Creating static Pod manifest for "kube-scheduler"
W0630 16:46:11.591614   21856 manifests.go:214] the default kube-apiserver authorization-mode is "Node,RBAC"; using "Node,RBAC"
[etcd] Creating static Pod manifest for local etcd in "/etc/kubernetes/manifests"
[wait-control-plane] Waiting for the kubelet to boot up the control plane as static Pods from directory "/etc/kubernetes/manifests". This can take up to 4m0s
[apiclient] All control plane components are healthy after 27.005881 seconds
[upload-config] Storing the configuration used in ConfigMap "kubeadm-config" in the "kube-system" Namespace
[kubelet] Creating a ConfigMap "kubelet-config-1.17" in namespace kube-system with the configuration for the kubelets in the cluster
[upload-certs] Skipping phase. Please see --upload-certs
[mark-control-plane] Marking the node master1 as control-plane by adding the label "node-role.kubernetes.io/master=''"
[mark-control-plane] Marking the node master1 as control-plane by adding the taints [node-role.kubernetes.io/master:NoSchedule]
[bootstrap-token] Using token: q0bk03.7efmmgmaxty9m0nm
[bootstrap-token] Configuring bootstrap tokens, cluster-info ConfigMap, RBAC Roles
[bootstrap-token] configured RBAC rules to allow Node Bootstrap tokens to post CSRs in order for nodes to get long term certificate credentials
[bootstrap-token] configured RBAC rules to allow the csrapprover controller automatically approve CSRs from a Node Bootstrap Token
[bootstrap-token] configured RBAC rules to allow certificate rotation for all node client certificates in the cluster
[bootstrap-token] Creating the "cluster-info" ConfigMap in the "kube-public" namespace
[kubelet-finalize] Updating "/etc/kubernetes/kubelet.conf" to point to a rotatable kubelet client certificate and key
[addons] Applied essential addon: CoreDNS
[addons] Applied essential addon: kube-proxy

Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 192.168.226.100:6443 --token q0bk03.7efmmgmaxty9m0nm \
    --discovery-token-ca-cert-hash sha256:10bebca0ac5d14b89b1760a4c7cb78a4cdc7cd38e7d806d5b17a7fc3beb9cd7d 
```
按照上述的相关操作，使用集群
```sh
mkdir .kube
cp -i /etc/kubernetes/admin.conf .kube/config
ll .kube/config
```
https://docs.tigera.io/archive/v3.9/release-notes/

以上链接在windows下载kubernetes的插件calico版本为3.9，并通过finalshell传输到centos7中，进入镜像的文件夹，master节点和worker节点都做如下操作
```sh
docker load -i calico-cni.tar
docker load -i calico-node.tar
docker load -i calico-kube-controllers.tar 
```

#### 网络配置
1. calico自身网络机制问题，修改calico的物理网卡
在master节点操作，找到calico/manifests中的calico.yaml文件
```bash
vim calico.yaml
```
```
:set nu
:607
```
增加下面内容
```
- name: IP_AUTODETECTION_METHOD
value: "interface=ens.*"
```
修改622行的内容
```
value: "10.244.0.0/16"
```
2. 执行修改后的配置文件
```bash
kubectl apply -f calico.yaml
```
3. 在worker节点中执行
```bash
kubeadm join 192.168.226.100:6443 --token q0bk03.7efmmgmaxty9m0nm --discovery-token-ca-cert-hash sha256:10bebca0ac5d14b89b1760a4c7cb78a4cdc7cd38e7d806d5b17a7fc3beb9cd7d
```
4. 在master节点执行，节点部署完成
```bash
kubectl get nodes
```

### 验证k8s集群部署可用性
#### 查看节点状态
```bash
kubectl get cs
kubectl cluster-info
kubectl get pods --namespace kube-system
```


## k8s集群客户端工具 kubectl
部署完成，使用命令行kubectl使用k8s集群
### kubectl帮助
#### 检查kubectl是否安装
```bash
rpm -qa | grep kubectl
```
#### 获取帮助
```bash
kubectl --help
```
### kubectl命令必要环境
worker节点使用kubectl命令管理集群
1. 准备集群管理配置文件
```bash
mkdir .kube
scp master1:root/.kube/config .kube/
```
2. 使用命令验证
```bash
kubectl get nodes
kubectl get pods -n kube-system # 出现对应的pods
```


## k8s集群资源清单(yaml)文件
kubectl命令在k8s集群中基础查询的简单操作比较方便，但是k8s集群中资源管理及大量资源对象编排部署（创建和删除等）操作，需要通过声明样式（YAML）文件，把需要对资源对象操作编辑到YAML格式文件中，称为资源清单文件，可以对大量的资源对象进行编排部署

### YAML文件书写格式
YAML为标记语言，以数据为中心，可读性

#### 语法
1. 空格为缩进，空格数目不重要，相同层级的元素左侧对齐即可
2. #标识注释

#### 数据结构
1. 对象
- 键值对的集合
- 映射/哈希/字典

2. 数组
- 一组按次序排序的值
- 序列/列表

3. 纯量
- 单个、不可再分的值
数值、布尔值（true/false）、时间格式、日期

字符串（默认不使用引号），如果字符串中有空格或特殊字符，需要放在引号中。字符串多行时，必须有一个单空格缩进，换行符会被转化为空格


### YAML文件实现资源清单描述方法
k8s集群，使用YAML格式创建符合期望的pod

## k8s集群NameSpace（命名空间）
需要两套k8s集群用于开发测试和预发布，但主机资源有限，则将现有的k8s集群运行两套环境，使用命名空间实现开发测试与预发布环境的隔离

### 介绍
多租户情况下，实现资源隔离。逻辑隔离；管理边界；不属于网络边界；每个namespace做资源配额。

### 查看命名空间
#### 命令
```bash
kubectl get namespace
```
命名空间如下
```
NAME              STATUS   AGE
default           Active   26h  用户创建pod默认为命名空间
kube-node-lease   Active   26h  kubernetes集群节点租约状态 
kube-public       Active   26h  所有用户均可以访问，包括未认证用户
kube-system       Active   26h  kubernetes集群使用，所有组件运行的空间，不可删除
```

### 创建namespace
#### kubectl命令行创建
```sh
kubectl create namespace test
```

#### kubectl命令应用资源清单文件创建
1. 准备资源清单文件
```bash
mkdir yamldir 
cd yamldir/
vim 01-create-ns.yaml
cat 01-create-ns.yaml
```
```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: test2
```
2. 应用资源清单文件
```sh
kubectl apply -f 01-create-ns.yaml
```
3. 验证是否创建成功
```sh
kubectl get namespace
```

### 删除namespace
删除命名空间，其中包含的所有资源对象同时被删除
#### kubectl命令行删除
```sh
kubectl get ns
```
#### 删除存在的namespace
```sh
kubectl delete namespace test2
```
#### 查看yaml配置文件
```sh
cat 01-create-ns.yaml
```
#### 删除资源清单文件
```sh
kubectl delete -f 01-create-ns.yaml
```

## k8s集群核心概念
### pod介绍
k8s集群中最小调度单元为pod
pod是容器的封装
### 查看pod
```bash
kubectl get pod
kubectl get pods 
kubectl get pod -n kube-system
```
### 创建pod
在worker中使用nginx:latest容器镜像

docker pull 拉起镜像失败
[https://blog.csdn.net/weixin_50160384/article/details/139861337](Docker Hub 拉取镜像一直失败超时)


#### 创建pod资源清单文件
在master节点中
```bash
cat 02-create-pod.yaml
vim 02-create-pod.yaml
```
```yaml
apiVersion: v1
kind: Pod
metadata: 
  name: pod1
spec:
  containers:
  - name: nginx-container
    image: nginx:latest
    imagePullPolicy: IfNotPresent  # 本地如果没有容器，进行网络下载
    ports:
    - name: nginxport
      containerPort: 80
```

#### 应用创建Pod资源清单文件
```sh
kubectl apply -f 02-create-pod.yaml -n default
```
查看已创建pod
```sh
kubectl get pods
```
默认命名空间查看已创建pod
```sh
kubectl get pods -n default
```

### pod访问
实际情况不建议访问
```sh
kubectl get pods -o wide
curl ip
```

### 删除pod
#### kubectl命令行删除
```sh
kubectl delete pods pod1
kubectl delete pods pod1 -n default
```
#### kubectl使用pod资源清单
```sh
kubectl delete -f 02-create-pod.yaml
```

## k8s集群核心controller控制器
删除pod的误操作，需要引入一个controller控制器，用于k8s集群中以loop方式监视pod状态，若发现pod被删除，将会重新拉起一个pod，让pod一直保持在用户期望状态。

### controller介绍
对应用运行的资源对象进行监控，理解为监控器

### controller分类
1. deployment
声明式更新控制器，发布无状态应用
2. ReplicaSet
副本集控制器，对pod进行副本规模的扩大或裁剪
3. statefulSet
状态副本集，发布有状态应用
4. DaemonSet
在k8s集群每一个Node上运行一个副本，用于发布监控或者日志收集类等应用
5. job
运行一次性作业
6. cronjob
运行周期性作业

### 创建deployment控制器
#### kubectl命令行创建
```bash
kubectl run nginx-app --image=nginx:latest --image-pull-policy=IfNotPresent --replicas=2
# nginx-app为deployment控制器类型的应用名称；nginx:latest为应用运行的pod中的container所使用的镜像；IfNotPresent为container容器镜像下载策略，如果本地有镜像，使用本地，如果没有，则下载镜像；--replicas=2为应用运行pod的两个副本，用户的期望值；deployment控制器中的replicaSet控制器会一直监控应用运行的pod副本状态，如果数量达不到用户期望，重新拉起一个新的pod，会让pod数量一直维持在用户期望值数量
```
```bash
kubectl get deployment
kubectl get replicaset
kubectl get replicaset 
kubectl get pods
kubectl get pods -o wide
```
可访问pod

#### 资源清单文件创建
1. 编写创建deployment控制器类型应用资源清单
```sh
vim 03-create-deployment-app.yaml
```
```yaml
apiVersion: apps/v1
kind: Deployment 
metadata:
  name: nginx-app2
spec: 
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template: 
    metadata:
      labels:
        app: nginx 
    spec:
      containers:
      - name: nginxapp2-container
        image: nginx:latest
        imagePullPolicy: IfNotPresent
        ports: 
        - containerPort: 80 
          name: nginxapp2
```
```sh
kubectl apply -f 03-create-deployment-app.yaml
```
检查
```sh
kubectl get deployment.apps
kubectl get rs
kubectl get pods
```
访问pod

### 删除deployment控制器类型应用
带有控制器类型的pod不能随便删除
#### kubectl命令删除
```sh
kubectl get deployment.apps
kubectl delete deployment.apps nginx-app
```
检查
```sh
kubectl get rs
kubectl get pods
```
#### 通过应用资源清单文件删除
```sh
kubectl get deployment.apps
kubectl delete -f 03-create-deployment-app2.yaml
```


## k8s集群核心Service
pod状态不是人为控制的，pod ip在创建时分配，如果pod被误删除，被controller重新拉起一个新的pod，发现pod ip地址是变化的，如果访问必须更换ip地址，对大量的pod运行应用来说，pod完全无法控制，因此引入service

### service介绍
不是实体服务，是一条iptables或ipvs的转发规则

### service作用
1. 通过service为pod客户端提供访问pod方法，即客户端访问pod入口
2. service通过pod标签与pod关联

### service类型
#### clusterIP
默认，分配一个集群内部可访问的虚拟ip
#### NodePort
在每个node分配一个端口作为外部访问入口
#### loadbalancer
工作在特定的cloud provider上
#### externalname
表示把集群外部的服务引入到集群内部中，即实现集群内部Pod和集群外部的服务进行通信

## service参数
1. port 访问service使用的端口
2. targetPort pod中容器端口
3. nodeport 通过node实现外网用户访问k8s集群内service

## service创建
### 命令行创建
默认创建service为clusterIP类型
#### 创建deployment类型应用
```sh
kubectl run nginx-app1 --image=nginx:latest --image-pull-policy=IfNotPresent --replicas=1
```
#### 验证deployment类型应用创建情况
```sh
kubectl get deployment.apps
kubectl get pod
```

#### 创建service与deployment类型应用关联
```sh
kubectl expose deployment.apps nginx-app1 --type=ClusterIP --target-port=80 --port=80
# expose 创建service
# deployment.apps 控制器类型
# nginx-app1 应用名称，service名称
# --type=ClusterIP 指定service类型
# --target-port=80 指定pod中容器端口
# --port=80 指定service端口
```

#### 访问service以实现访问pod目的
查看service创建情况
```sh
kubectl get service
kubectl get svc
```
访问service
```sh
curl http://serviceip地址
```
端点，关联关系
```sh
kubectl get endpoints
```

### 资源清单yaml文件创建
#### 编写创建service资源清单文件
```sh
vim 04-create-deployment-nginx-app2-service.yaml
```
```yaml
---
apiVersion: app/v1
kind: Deployment
metadate: 
  name: nginx-app2
spec:
  replicas: 2
  selector: 
    matchLabel: 
      apps: nginx
  template: 
    metadata: 
      labels:
        apps: nginx
    spec: 
      containers: 
      - name: nginxapp2
        image: nginx:latest
        imagePullPolicy: IfNotPresent
        ports: 
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata: 
  name: nginx-app2-svc
spec: 
  type: ClusterIP
  ports: 
  - protocol: TCP
    port: 80
    targetPort: 80
  selector: 
    apps: nginx
```
#### 应用service资源清单文件
```sh
kubectl apply -f 04-create-deployment-nginx-app2-service.yaml
kubectl get deployment.apps
kubectl get service
```
#### 访问service
```sh
curl http://ip地址
```

### 资源清单yaml文件创建NodePort类型Service
#### 编写资源清单文件
```sh
cp 04-create-deployment-nginx-app2-service.yaml 05-create-deployment-nginx-app3-service.yaml
```
```yaml
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-app3
spec:
  replicas: 2
  selector:
    matchLabels:
      apps: nginx-app3
  template:
    metadata:
      labels:
        apps: nginx-app3
    spec:
      containers:
      - name: nginxapp3
        image: nginx:latest
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: nginx-app3-svc
spec:
  type: NodePort
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
  selector:
    apps: nginx-app3                
```
```sh
kubectl apply -f 05-create-deployment-nginx-app3-service.yaml
kubectl get svc
ss -anput | grep ":31796"
```
可访问集群中的ip地址：端口
#### 指定端口
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-app3-svc
spec:
  type: NodePort
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
    nodePort: 30001
  selector:
    apps: nginx-app3  
```

### 删除service
#### 命令行kubectl删除
```sh
kubectl get svc
kubectl delete service nginx-app1
```
#### 删除资源清单文件
```sh
kubectl delete -f 04-create-deployment-nginx-app2-service.yaml 
```
development.apps控制器类型应用和service都同样被删除

## 使用已部署完成的虚拟机磁盘文件
### 新建虚拟机
### 添加现有磁盘文件
### 需修改别人虚拟机磁盘文件的网络配置
```sh
ip addr
cat /etc/sysconfig/network-scripts/ifcfg-ens33
vim /etc/sysconfig/network-scripts/ifcfg-ens33
```

### 主机名称解析修改
```bash
cat /etc/hosts
vi /etc/hosts
```

### 验证状态
```sh
kubectl get nodes
kubectl get pods -n kube-system
```



