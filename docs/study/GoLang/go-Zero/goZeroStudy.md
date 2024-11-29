

## 微服务介绍

单体服务劣势，后端改一个很小的地方，整个项目重新构建，然后停止项目，进行重启。微服务将大的系统按照功能或者产品进行服务拆分，形成一个独立的服务

1. 服务与服务之间通信
2. 怎么找到每一个服务的地址

小项目不需要go-zero进行代码分层，个人项目，不建议使用微服务框架，公司大型项目适合微服务架构

## go-zero微服务框架
集成实际业务的各种web和rpc框架，弹性设计保障大并发服务端的稳定性

根据定义api文件一键生成代码

## 环境安装部分
### 安装etcd
https://blog.csdn.net/donkor_/article/details/140127332

https://github.com/etcd-io/etcd/releases
## 安装etcd

## 安装goctl工具

## 安装protoc和protoc-gen-go插件

## go-zero快速开始
https://github.com/zeromicro/zero-doc/blob/main/doc/shorturl.md