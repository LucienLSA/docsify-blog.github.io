

## 即时通信系统
windows下使用telnet时，能够顺利链接和断开，但输入指令进行通信存在问题。安装windows下的ncat工具（即linux中的nc）

### 基础server搭建

在控制面板的程序中启动windows功能，打开telnet

### 用户上线和广播
#### OnlineMap和Message channel
记录有哪些用户user在线，一个客户端为一个用户，每个用户对象会绑定一个channel，channel专门向对应的客户端发送消息，每个用户内有goroutine，不断阻塞，监听channel内是否有数据，一旦有数据，立刻将数据拿出来，通过conn把数据写到对应的客户端中。Message本质是channel，如果当前的消息，需要每一个客户端收到，将消息进行广播。

####
当前用户上线，客户端发送一个client请求，创建用户，加入到onlineMap中，把上线的消息进行广播，将上线的online消息广播，发送到Message的channel中，goroutine将会监听message，给每一个user发消息  

#### 用户消息广播机制
接收客户端发送的消息

##### 用户的上线业务
将该业务写在user.go中，对接口进行封装，将之间的user业务替换掉
##### 用户的下线业务

##### 用户处理消息的业务

### 在线用户查询
在用户处理消息业务中，添加查询在线用户的功能。其中增加一个给当前User对应客户端发送消息的方法

### 修改用户名
用户处理消息业务中，添加修改用户名的功能，格式为"rename|newName"，且加上判断onlineMap中是否已经存在该用户名

### 超时强踢功能
如果用户长时间未活动（未发送消息），则强制close链接。

在用户的Handler()的goroutine中，添加用户活跃的channel，一旦有消息，就向该channel发送数据。

在用户的Handler()的goroutine创建一个定时器，一旦触发执行强踢

### 私聊功能
指定发送消息的对象，发送的消息格式为"to|lucien|hello, I'm Jack"

在用户处理消息业务中加入该功能

1 获取对方的用户名

2 根据用户名得到User对象

3 获取消息内容，通过对方的User对象将消息内容发送

### 客户端实现
同一个包下可有多个main函数，编译时不在一起即可
#### 客户端定义与链接
client.go中定义客户端类

创建客户端对象，链接server， 返回对象，主函数中启动客户端业务

#### 客户端命令行解析
创建init初始化函数进行命令行解析，可设置其他的serverIp和serverPort

#### 菜单显示
client增加flag属性，增加menu()方法，获取用户的输入。新增Run()主业务的循环，main函数调用Run()

#### 更新用户名
client.go中新增UpdateName()更新用户名，加入到Run业务中，添加处理server回执消息方法DealResponse()，开启一个go，承载DealResponse()流程

#### 公聊模式业务
client.go中新增PublicChat()公聊模式业务，加入到Run分支中

#### 私聊模式业务
查询当前有哪些用户在线，提示用户选择一个用户私聊。 client.go中新增私聊业务，添加到Run业务中

#### 传输文件


#### 制作UIL


