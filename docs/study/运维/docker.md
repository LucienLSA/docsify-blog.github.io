
## 阿里云镜像

[https://developer.aliyun.com/mirror/?spm=a2c6h.13651102.0.0.772f1b11Pvt54c&amp;serviceType=mirror&amp;tag=%E5%AE%B9%E5%99%A8](容器的下载安装)

[容器镜像服务](https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors)

国内访问不到docker官方镜像的缘故

[解决Centos7安装docker源问题_adding repo from-CSDN博客](https://blog.csdn.net/qq_37272999/article/details/86769520)

挂梯子拉取镜像

[彻底解决docker：docker: Get https://registry-1.docker.io/v2/: net/http: request canceled 报错-CSDN博客](https://blog.csdn.net/2301_79849395/article/details/142829852)

[【教程】挂上梯子，无忧docker镜像封杀，轻松拉取；docker 镜像代理配置； - 简书](https://www.jianshu.com/p/96761991d040)

[设置Clash代理，彻底解决linux系统Docker pull的问题 - 滑滑蛋的个人博客](https://slipegg.github.io/2024/06/23/ClashForDocker/)

[多种 Docker 镜像拉取解决方案与实践 | Razeen`s Blog](https://razeen.me/posts/how-to-pull-docker-image-in-china/#liunx)

[Docker启动失败问题解决：Job for docker.service failed because the control process exited with error code.....-CSDN博客](https://blog.csdn.net/mo_sss/article/details/135947199)

[js清除浏览器缓存的几种方法（项目总结）「建议收藏」-腾讯云开发者社区-腾讯云](https://cloud.tencent.com/developer/article/2100885)

[Dockerfile（9） - ENTRYPOINT 指令详解-腾讯云开发者社区-腾讯云](https://cloud.tencent.com/developer/article/1896351)

[Docker | 将自己的docker镜像推送到docker hub[图文详情]_如何将自己的docker镜像上传到dockerhub上-CSDN博客](https://blog.csdn.net/weixin_44649780/article/details/135107176)

[docker如何实现重新打tag并删除原tag的镜像（[仓库名: tag] 可以查询到指定id的镜像，同一个id镜像能有多个[仓库名: tag]）（增加\删除镜像仓库：标签）_docker重新打tag-CSDN博客](https://blog.csdn.net/Dontla/article/details/122868804)

[Docker 快速更改容器的重启策略(Restart Policies)以及重启策略详解_docker restart policy-CSDN博客](https://blog.csdn.net/peng2hui1314/article/details/139260106)

[docker入门（八）—— dockerfile详细介绍，编写dockerfile-CSDN博客](https://blog.csdn.net/weixin_43980547/article/details/136914370)

[docker查看容器/删除(所有)容器/删除镜像_查看docker删除运行的容实例-CSDN博客](https://blog.csdn.net/HYZX_9987/article/details/118999188)

[docker部署go项目](https://www.fengfengzhidao.com/article/dtyibo4BEG4v2tWkcxXp)

[Docker的部署（以go为例子）_docker go-CSDN博客](https://blog.csdn.net/2303_79710813/article/details/143079418?spm=1001.2101.3001.6650.13&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-13-143079418-blog-138279275.235%5Ev43%5Epc_blog_bottom_relevance_base8&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-13-143079418-blog-138279275.235%5Ev43%5Epc_blog_bottom_relevance_base8&utm_relevant_index=16)

[Docker容器数据卷详解_docker数据卷-CSDN博客](https://blog.csdn.net/huangjhai/article/details/119860628)

[Docker教程小白实操入门（20）--如何删除数据卷_docker rm -v containerid|containername-CSDN博客](https://blog.csdn.net/u013288190/article/details/108955593)

[docker安装mysql并实现数据卷挂载_docker mysql 挂载-CSDN博客](https://blog.csdn.net/qq_73609596/article/details/140739792)

[Docker build出现“The command ‘/bin/sh -c apt-get install -y vim‘ returned a non-zzero code: 100”解决方法_did not complete successfully: exit code: 100-CSDN博客](https://blog.csdn.net/weixin_41693877/article/details/108504527)

[Docker部署MySQL 8.3.0（保姆级图文教程）_docker 安装mysql8.3-CSDN博客](https://blog.csdn.net/donkor_/article/details/139879575)

[Docker 安装 Redis 容器 (完整详细版) - 鹏星 - 博客园](https://www.cnblogs.com/lzp110119/p/17869310.html)

## 通过配置clash-linux版本 实现梯子 并docker访问镜像

[wnlen/clash-for-linux: clash-for-linux](https://github.com/wnlen/clash-for-linux)

wget https://drive.rocklinuxmirror.eu.org/index.php/s/5MndEnkeqzpiiS2/download?path=%2F&files=clash-linux-amd64-v1.18.0.gz

docker run -d -p 80:80 --hostname gitlab.example.com twang2218/gitlab-ce-zh:11.1

## VPS(Virtual Private Server 虚拟专用服务器)

操作系统：cpu、内存、存储、网络

传统的虚拟专用服务器，不支持用户自主升降级，资源是预先分配的，不易调整的。

给VPS加入自主升降级的功能，成为ECS(Elastic Compute Service 弹性计算服务)

## Docker容器

ECS上可部署自己的软件应用，机器多了，ECS之间底层操作系统不同（ubuntu和centos），出现环境问题。让软件和操作系统环境一起部署，于是将软件和操作系统一起打包成虚拟机部署在ECS中，再运行一个完整的虚拟机，但这样太重了。

于是只打包软件和系统依赖库+配置，Namespace(看起来像一个独立操作系统)，cgroup(限制它能使用的计算资源)。就省掉一层笨重的操作系统，可移植到各类操作系统上，则称为docker容器

在物理服务器跑ecs，ecs跑docker容器，多个docker容器共享一个ecs

![image.png](https://s2.loli.net/2024/06/24/JOR4IymAF9bWSD3.png)

### docker是什么

A机器：代码应用+依赖+配置+ubuntu系统；  B机器：代码应用+依赖+配置+centos系统

环境包括依赖库+配置+操作系统

docker将程序和环境打包并运行的工具软件

### 基础镜像

统一环境：环境中最重要的是操作系统，让所有程序都跑在同一个操作系统上。

操作系统包括用户空间和内核空间，应用程序运行在用户空间。因此利用操作系统用户空间部分，就能构建应用所需的环境。将文件系统、依赖库、程序打包成一个类似“压缩包”的文件，得到基础镜像

### dockerfile

需要安装依赖和文件夹及执行的命令，从操作系统到应用服务启动，需要做的清单文件

### 容器镜像(Container Image)

dockerfile只是描述需要做哪些事情，并没有真正开始做。当执行docker build，docker软件按照dockerfile的说明一行行构建环境+应用程序，打包成一个压缩包，称为容器镜像

只有将容器镜像传到任意一台服务器上，对这个压缩包进行解压缩，可以同时运行环境和程序

### Registry

负责管理镜像仓库推拉能力的服务称为docker registry，则可搭建各种官方或私人的仓库(dockerhub、tuna)

服务器太多，直接一个个传输，发送方的网络带宽受太大影响

docker push 将镜像推到仓库，需要的时候，再通过docker pull 将镜像拉到机器上

### 容器

在目的服务器上docker pull，再执行docker run，将这个容器镜像进行"解压缩"，获得一个独立的环境和应用程序，并运行起来。

可在一个操作系统上，同时跑多个容器，且之间的容器都是相互独立、互相隔离的

#### 容器和虚拟机的关系

1. 容器
   容器包括程序+依赖库+配置+独立文件系统等必要组件，利用Namespace(看起来像一个独立操作系统)，cgroup(限制它能使用的计算资源)。容器本质上是自带独立运行环境的特殊进程，底层用的其实是宿主机的操作系统的内核
2. 传统虚拟机
   传统虚拟机自带一个完整操作系统

### docker的架构原理

#### client(docker-cli)

命令行中输入docker命令，即使用docker-cli命令，以解析输入的command命令，再调用docker daemon

#### server(docker daemon)

根基client的command命令，调用docker daemon的守护进程提供的restful API，守护进程根据命令创建和管理各个容器

docker daemon内部分为docker server和engine两层，docker server本质上是http服务器，负责对外提供操作容器和镜像的API，接口接收到API请求后，会分发任务给engine层，engine层负责创建job，由job实际执行各种工作(docker命令)

1. docker build
   job则会根据dockerfile指令构建容器镜像文件
2. docker pull/push
   镜像的推拉操作，job则会跟外部的docker registry交互，将镜像上传和下载
3. docker run
   job会基于镜像文件，调用containerd组件，驱使runC组件创建和运行容器

### docker compose

当部署多个容器，且对容器的顺序有一定要求，比如一个博客（先启动数据库，再启动身份验证服务，最后启动博客web服务）

通过一个yaml文件，给出容器有哪些，部署顺序是什么样的，以及容器所占用的cpu和内存等信息

docker-compose up命令解析yaml文件，将容器们一键按顺序部署，完成一整套服务的部署

### docker swarm

docker swarm是一整套服务在多台服务器上的集群部署，比如某应用在a服务器坏了，该应用在b服务器上重新部署一套实现迁移，还能根据需要对应用做扩缩容。

### docker和k8s的区别

k8s会在多台node的服务器上调度pod进行部署和扩缩容，每个Pod内部可以含有多个container，每个container本质上就是一个服务进程

两者为竞品，docker部署的容器即为k8s中调度pod里的container

docker-compose即为k8s中的pod

docker-swarm和k8s一样，即调度pod

k8s定义为容器编排引擎，以API编程方式管理安排各个容器的引擎

## centos7安装docker

[https://cloud.tencent.com/developer/article/1701451](centos7安装Docker详细步骤)

[https://www.runoob.com/docker/centos-docker-install.html](菜鸟教程centos7安装教程)

[https://developer.aliyun.com/article/831657](【阿里云镜像】使用阿里云Docker CE 镜像安装Docker)
基于CentOS 7安装配置Docker（使用 yum 进行安装）

## centos7docker开启远程连接设置

[https://blog.csdn.net/qq_39218530/article/details/109854522](centos7docker开启远程连接设置)

## 解决Docker被墙pull拉取retrying in 1 second超时可用镜像地址

[https://blog.csdn.net/qq_42520112/article/details/100886402](Docker拉取镜像超时解决办法)

## vscode连接Linux系统下的docker容器

[https://blog.csdn.net/weixin_45656074/article/details/131160299](vscode连接docker)

## ubuntu安装docker与镜像加速器

[https://docker-practice.github.io/zh-cn/install/ubuntu.html](ubuntu安装docker)

## ubuntu完全卸载docker及再次安装

[https://blog.csdn.net/qq_45495857/article/details/113743109](ubuntu完全卸载与安装)

## linux中docker报错：ERROR: Got permission denied while trying to connect to the Docker daemon socket

[https://blog.csdn.net/qq_45097352/article/details/116105246](ot permission denied while trying to connect to the Docker daemon socket)

关键:

```bash
sudo groupadd docker               #添加用户组
sudo gpasswd -a username docker    #将当前用户添加至用户组
newgrp docker                      #更新用户组
```
