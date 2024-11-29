
## 通过VMware Workstation 创建虚拟机。选择内存、硬盘大小、CPU等

## 在镜像网站下载ubuntu的发行版，在CD/DVD选择下载的ios文件，启动虚拟机，进入ubuntu的安装

### 2.1 ubtuntu系统安装过程遇到页面显示不全的解决方法

[ubtuntu系统安装过程遇到页面显示不全的解决方法](https://blog.csdn.net/Passerby_Wang/article/details/124075121)

* 拖动窗口，可能不会出现下一步的按钮
* 进入"Try ubtuntu"，在系统设置中找到"Display"将显示的分辨率改为16:9或16:10

## openSSH检查

### 检查系统信息:

```bash
lsb_release -a
```

### 安装openSSH服务端

```bash
sudo apt-get install openssh-server
```

### 查看openSSH状态

```bash
sudo systemctl status sshd
```

Ubuntu安装ssh时出现软件包 openssh-server 还没有可供安装的候选者错误

错误如下：

```bash
sudo apt-get install opensshserver正在读取软件包列表...
完成正在分析软件包的依赖关系树正在读取状态信息...
完成现在没有可用的软件包 openssh-server，
但是他被其他的软件包引用了这可能意味着这个缺失的软件包可能已被废弃，或者只能在其他发布源中找到
E:软件包 openssh-server 还没有可供安装的候选者
```

解决方案：分析原因是apt-get没有更新，操作命令如下：

```bash
sudo apt-get update
```

更新完毕后执行：

```bash
sudo apt-get install openssh-server
```

### 设置root密码(和主机用户名密码一致就好)

```bash
sudo passwd root
输入密码
设置root密码，再次确认
```

## sudo apt update 问题总结

### Ubuntu 20.04设置DNS解析

[https://blog.csdn.net/lsc_1893/article/details/118696693]（解决resolve.conf被覆盖问题）
[https://www.cnblogs.com/mouseleo/p/14976527.html](Ubuntu 20.04 设置 DNS 的方法)

### Ubuntu 20.04 TSL换源报错

Err:1 http://mirrors.aliyun.com/ubuntu focal InRelease

[https://blog.csdn.net/weixin_44129133/article/details/124797329](Ubuntu 20.04 TSL换源报错：Err:1 http://mirrors.aliyun.com/ubuntu focal InRelease)

[https://www.jianshu.com/p/12a57dfaa336](sudo apt date 的时候换源阿里云镜像问题)

### Linux - Ubuntu下执行apt-get update报错

Some index files failed to download. They have been ignored, or ol
[https://cloud.tencent.com/developer/article/1640834](Linux - Ubuntu下执行apt-get update报错：Some index files failed to download. They have been ignored, or ol)

## Xshell远程连接linux，建立新对话，一般输入主机，登录用户和密码即可

若使用root用户登录会话，需修改一定配置

### 确保安装了openSSH-server 和ssh

### 开启ssh-server 服务

```bash
service ssh start
```

### 修改vim(若无则安装vim)

```bash
apt-get install vim
vim /etc/ssh/sshd_config
```

### 安装好后，修改vim，添加如下配置(去掉#)

```bash
PermitRootLogin yes
StrictModes yes
```

### 保存退出并重启ssh

```bash
sudo /etc/init.d/ssh restart
```

## FinalShell 远程连接linux（同理）

## 检查ipconfig信息，可查看子网ip

或者在VMware的虚拟网络编辑器，查看VMnet8的NAT模式

## 配置主机名映射

在C:\windows\system32\drivers\etc\hosts文件中配置记录

若找不到hosts，在查看选项中取消受保护的操作系统隐藏文件查看。最好先复制一份。

### 不可直接右键管理员打开hosts，可通过Windows Powershell（管理员）

### 记事本界面点击文件，然后点新建，在弹出来的窗口里找到路径c:\windows\system32\drivers\etc\，找到后可以看到目录里面是空白。点击右下角的选择文件，点击所有文件。

### 然后点右下角的打开。就会弹出hosts文件的编辑页面，可以在里面添加你需要的IP地址和主机名了。

### 增加IP地址和主机名后，在Xshell的连接中可将IP地址改为修改的主机名

## Window与Linux文件互传

通过Xshell上的Xftp，将Xftp与Linux主机远程连接。（一般可自动检测连接，输入用户名及密码）

## 自定义环境变量

`【黑马程序员新版Linux零基础快速入门到精通，全涵盖linux系统知识、常用软件环境部署、Shell脚本、云平台实践、大数据集群项目实战等】 https://www.bilibili.com/video/BV1n84y1i7td/?p=40&share_source=copy_web&vd_source=a4135e426a7a235a4cdfb77a7869087e`

## VMware Tools增强工具

[VMware——VMware Tools的介绍及安装方法_wmware tools-CSDN博客](https://blog.csdn.net/williamcsj/article/details/121019391)

## 【虚拟机转移】超详细的将虚拟机（ubuntu）从一台电脑复制到另一台电脑教程

[https://blog.csdn.net/denghls/article/details/126590630]()

## Ubuntu修改源镜像方法（22.04也能用）附带常用源镜像地址

[https://blog.csdn.net/qq_14931165/article/details/126863871]()

## Ubuntu 20.04 ROS Noetic及Realsense-ROS的安装和多机通信

[Ubuntu 20.04 ROS Noetic及Realsense-ROS的安装和多机通信_noetic安装realsense-ros-CSDN博客](https://blog.csdn.net/weixin_43674433/article/details/125334062)

## 安装ubuntu20.04 服务器版

[Ubuntu 20.04 live server版安装(详细版)_ubuntu20.04server安装教程-CSDN博客](https://blog.csdn.net/JackMaF/article/details/119369874)

## unbuntu软件安装--选择国内镜像源(最好按要求选择版本对应的清华镜像源)

使用 Ubuntu app商店下载软件时，会出现下载很慢的情况。可能有两种原因：一是我们本地的网速很慢。二是我们使用了国外的镜像源，导致连接很慢。

1. Software & Updates
2. 选择第一个选项 ubuntu Software，可以看到此时的下载源，Download from
3. 找到 China一栏，选择国内镜像源，如下图的阿里云。或者点击右上角的选择最佳源，自动进行测试，选择最好的源，但需到等待一段时间！
4. 选择好镜像源后，点击右下角 Choose Server
5. 点击右下角 close关闭按钮
6. 弹出窗口， 重新载入软件源，点击Reload

## ubuntu关机慢

[Ubuntu关机异常慢的解决方法_a stop job is running for session-CSDN博客](https://blog.csdn.net/qq_32618327/article/details/109549134)

## Ubuntu 工具链升级 gcc 流程

[【小记】Ubuntu 工具链升级 gcc 流程 - 芯片烤电池 - 博客园](https://www.cnblogs.com/airchip/p/16280320.html)

## ubuntu20.04 安装mysql8.0

[Ubuntu 20.04 安装 MySQL 8.0 并且远程连接数据库(包括后续遇到的新坑)_同样的ubuntu20系统,阿里云服务器上可以tp3可以链接8.0数据库,但是亚马逊服务器上-CSDN博客](https://blog.csdn.net/u014378628/article/details/118406005)

## ubuntu20.04 安装redis6.0

[Ubuntu20.04安装redis笔记(防踩坑)_ubuntu20.04安装redis 6.2.4-CSDN博客](https://blog.csdn.net/cc76825/article/details/117524896)

[Linux安装Redis环境_ubuntu20 安装redis-CSDN博客](https://blog.csdn.net/Insight__/article/details/134203111#:~:text=%E5%89%8D%E5%BE%80%20Redis%E5%AE%98%E7%BD%91%20%E5%AE%89%E8%A3%85%E5%8E%8B%E7%BC%A9%E5%8C%85%20%E6%88%96%20%E5%A4%8D%E5%88%B6%E4%B8%8B%E8%BD%BD%E9%93%BE%E6%8E%A5%E9%80%9A%E8%BF%87wget%E4%B8%8B%E8%BD%BD%20wget%20https%3A%2F%2Fdownload.redis.io%2Freleases%2Fredis-6.0.9.tar.gz%201,%E7%A7%BB%E5%8A%A8%E5%88%B0%E4%BD%A0%E8%A6%81%E5%AE%89%E8%A3%85%E7%9A%84%E7%9B%AE%E5%BD%95%2C%E6%88%91%E8%BF%99%E9%87%8C%E5%AE%89%E8%A3%85%E5%88%B0%E4%BA%86%2Fuser%2Flocal%E4%B8%8B%20sudo%20mv.%2Fredis-6.0.9%20%2Fusr%2Flocal%2Fredis%20%23%20%E8%BF%9B%E5%85%A5%E5%AE%89%E8%A3%85%E7%9B%AE%E5%BD%95%EF%BC%8C%E7%BC%96%E8%AF%91redis%28Redis%E4%BD%BF%E7%94%A8c%E8%AF%AD%E8%A8%80%E7%BC%96%E5%86%99%EF%BC%8C%E9%9C%80%E8%A6%81%E5%85%88%E7%BC%96%E8%AF%91%E5%86%8D%E6%89%A7%E8%A1%8C%29%20cd%20%2Fusr%2Flocal%2Fredis)

## 如何解决Ubuntu server 下 Redis安装报错：“You need tcl 8.5 or newer in order to run the Redis test”.

[如何解决Ubuntu server 下 Redis安装报错：“You need tcl 8.5 or newer in order to run the Redis test”.-阿里云开发者社区](https://developer.aliyun.com/article/1379855)

[Ubuntu 14.04下Redis安装报错：“You need tcl 8.5 or newer in order to run the Redis test”问题解决...-CSDN博客](https://blog.csdn.net/weixin_30390075/article/details/95096265?spm=1001.2101.3001.6650.3&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-3-95096265-blog-108337035.235%5Ev43%5Epc_blog_bottom_relevance_base8&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-3-95096265-blog-108337035.235%5Ev43%5Epc_blog_bottom_relevance_base8&utm_relevant_index=4)

[wget http://downloads.sourceforge.net/tcl/tcl8.6.1-src.tar.gz 下载失败【已解决】_tcl8.6 下载-CSDN博客](https://blog.csdn.net/sinat_41144773/article/details/92767939)

## linux 安装报错：pkg-config not found

[linux 安装报错：pkg-config not found-CSDN博客](https://blog.csdn.net/banyu0052/article/details/101946224)

## CMake Could not find openssl

[Ubuntu下cmake编译报错OPENSSL_ROOT_DIR (missing: OPENSSL_CRYPTO_LIBRARY) (found version “1.1.1“)_cmake编译openssl-CSDN博客](https://blog.csdn.net/m0_52103955/article/details/135656339)

[CMake 不能找到 OpenSSL 库的解决方法_cmake openssl not found-CSDN博客](https://blog.csdn.net/a1971191/article/details/142252594?spm=1001.2101.3001.6650.4&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EYuanLiJiHua%7EPosition-4-142252594-blog-135656339.235%5Ev43%5Epc_blog_bottom_relevance_base8&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EYuanLiJiHua%7EPosition-4-142252594-blog-135656339.235%5Ev43%5Epc_blog_bottom_relevance_base8&utm_relevant_index=9)

## The following ICU libraries were not found:

[cmake - The following ICU libraries were not found: -- i18n (required) - Stack Overflow](https://stackoverflow.com/questions/52510499/the-following-icu-libraries-were-not-found-i18n-required)

Ubuntu 配置docker

[如何在 Ubuntu 20.04 上安装和使用 Docker_ubuntu20.04 docker-CSDN博客](https://blog.csdn.net/qq_35241329/article/details/135407666)

[ubuntu清理空间技巧 包含【系统日志、缓存、无用包、内核、VScode、conda、snap、pip】_sudo apt autoremove --purge snapd-CSDN博客](https://blog.csdn.net/m0_50181189/article/details/119855107)

## 配置ubuntu VsCode和c++/c环境

[【详解】VScode(Visual Studio Code)在Liunx下的安装与配置_linux安装vscode-CSDN博客](https://blog.csdn.net/qq_42519524/article/details/119250935)

[Vscode连接Ubuntu！看这一篇就够了！_vscode ubuntu-CSDN博客](https://blog.csdn.net/ggw15938357681/article/details/137228620)

[Vscode远程连接Ubuntu出错问题的解决方法_Linux_脚本之家](https://www.jb51.net/article/197324.htm)

[vscode远程连接Got bad result from install script.-CSDN博客](https://blog.csdn.net/u014541802/article/details/131722348?spm=1001.2101.3001.6650.6&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7Ebaidujs_baidulandingword%7ECtr-6-131722348-blog-121654580.235%5Ev43%5Epc_blog_bottom_relevance_base8&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7Ebaidujs_baidulandingword%7ECtr-6-131722348-blog-121654580.235%5Ev43%5Epc_blog_bottom_relevance_base8&utm_relevant_index=11)

[LINUX下作为用户以管理员权限打开VS Code的办法_vscode如何在linux中root管理员下使用-CSDN博客](https://blog.csdn.net/qq_47615055/article/details/123130547)

sudo code --verbose --user-data-dir --no-sandbox

## 配置ubuntu golang环境

[在ubuntu上安装golang以及环境配置_apt-get install golang special version-CSDN博客](https://blog.csdn.net/qq_44026293/article/details/108699566)

[Linux+VSCode搭建Golang开发环境_linux下vscode搭建golang开发环境-CSDN博客](https://blog.csdn.net/qq_41583040/article/details/131005277)

[Ubuntu - 查看、开启、关闭和永久关闭防火墙_ubuntu 查看防火墙-CSDN博客](https://blog.csdn.net/qq_43116031/article/details/133818049)

[Linux——使用kill结束进程并恢复进程_linux进程杀死后重新恢复-CSDN博客](https://blog.csdn.net/qq_40280673/article/details/134569750)

[Linux——解决问题：waiting for cache lock: Could not get lock /var/lib/dpkg/lock-frontend. It is held by pr-CSDN博客](https://blog.csdn.net/qq_40280673/article/details/134570091)

[ubuntu软件安装在哪里/安装位置_ubuntu软件安装在哪个目录-CSDN博客](https://blog.csdn.net/universe_R/article/details/122113798)

[Linux下彻底卸载Clash/解决Linux下一进入终端就http_proxy自动启用的问题 - 知](https://zhuanlan.zhihu.com/p/674091908#:~:text=1%E3%80%81%E8%BF%9B%E5%85%A5~%2F.bashrc%E6%96%87%E4%BB%B6%EF%BC%8C%E6%B3%A8%E9%87%8A%2F%E5%88%A0%E9%99%A4%E5%AF%B9http_proxy%2Fhttps_proxy%E7%9A%84%E8%87%AA%E5%8A%A8%E5%90%AF%E5%8A%A8,2%E3%80%81%E8%BF%9B%E5%85%A5%2Fetc%2Fprofile.d%E6%96%87%E4%BB%B6%E5%A4%B9%EF%BC%8C%E5%88%A0%E9%99%A4clash.sh%EF%BC%8C%E9%98%B2%E6%AD%A2http_proxy%E7%8E%AF%E5%A2%83%E5%8F%98%E9%87%8F%E8%87%AA%E5%8A%A8%E8%AE%BE%E7%BD%AE%EF%BC%88%E4%B8%8D%E7%A1%AE%E5%AE%9A%E6%98%AF%E5%90%A6%E5%BF%85%E8%A6%81%EF%BC%8C%E4%BD%86%E5%AE%89%E8%A3%85%E7%9A%84%E6%97%B6%E5%80%99%E8%AE%BE%E7%BD%AE%E4%BA%86%E5%B0%B1%E5%8F%96%E6%B6%88%E8%BF%99%E4%B8%AA%E8%AE%BE%E7%BD%AE%E5%90%A7%EF%BC%89%203%E3%80%81%E6%89%BE%E5%88%B0clash%E7%9A%84%E5%AE%89%E8%A3%85%E7%9B%AE%E5%BD%95%EF%BC%8C%E5%88%A0%E9%99%A4clash)

[乎](https://zhuanlan.zhihu.com/p/674091908#:~:text=1%E3%80%81%E8%BF%9B%E5%85%A5~%2F.bashrc%E6%96%87%E4%BB%B6%EF%BC%8C%E6%B3%A8%E9%87%8A%2F%E5%88%A0%E9%99%A4%E5%AF%B9http_proxy%2Fhttps_proxy%E7%9A%84%E8%87%AA%E5%8A%A8%E5%90%AF%E5%8A%A8,2%E3%80%81%E8%BF%9B%E5%85%A5%2Fetc%2Fprofile.d%E6%96%87%E4%BB%B6%E5%A4%B9%EF%BC%8C%E5%88%A0%E9%99%A4clash.sh%EF%BC%8C%E9%98%B2%E6%AD%A2http_proxy%E7%8E%AF%E5%A2%83%E5%8F%98%E9%87%8F%E8%87%AA%E5%8A%A8%E8%AE%BE%E7%BD%AE%EF%BC%88%E4%B8%8D%E7%A1%AE%E5%AE%9A%E6%98%AF%E5%90%A6%E5%BF%85%E8%A6%81%EF%BC%8C%E4%BD%86%E5%AE%89%E8%A3%85%E7%9A%84%E6%97%B6%E5%80%99%E8%AE%BE%E7%BD%AE%E4%BA%86%E5%B0%B1%E5%8F%96%E6%B6%88%E8%BF%99%E4%B8%AA%E8%AE%BE%E7%BD%AE%E5%90%A7%EF%BC%89%203%E3%80%81%E6%89%BE%E5%88%B0clash%E7%9A%84%E5%AE%89%E8%A3%85%E7%9B%AE%E5%BD%95%EF%BC%8C%E5%88%A0%E9%99%A4clash)

[快速入门 - Clash Verge Rev Docs](https://www.clashverge.dev/guide/quickstart.html)

[Linux下 VScode以sudo/root权限运行的最新方法_vscode root-CSDN博客](https://blog.csdn.net/qq_40522908/article/details/131351516)

[如何在Ubuntu上清理缓存和垃圾文件_apt清理缓存-CSDN博客](https://blog.csdn.net/m0_52537869/article/details/134705999)

[安装Go语言、搭建开发环境、依赖包下载（保姆教程）_go语言环境安装-CSDN博客](https://blog.csdn.net/qq_38105536/article/details/142635132)
