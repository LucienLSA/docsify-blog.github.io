---
title: go-redis基础学习
katex: true
categories: 
- golang
tags:
- redis
---
## 学习资源

[https://www.bilibili.com/video/BV1FY411d7JF/?spm_id_from=333.788.top_right_bar_window_custom_collection.content.click&amp;vd_source=917ef87e48a267f0acc88f766dea0a6e](【码神之路】go-redis教程，十年大厂程序员讲解，通俗易懂)

[Go操作Redis实战 - itbsl - 博客园](https://www.cnblogs.com/itbsl/p/14198111.html#exists%E6%A3%80%E6%B5%8B%E7%BC%93%E5%AD%98%E9%A1%B9%E6%98%AF%E5%90%A6%E5%AD%98%E5%9C%A8)

## 上手入门

### 本地连接

```go
rdb := redis.NewClient(&redis.Options{
	Addr:	  "localhost:6379",
	Password: "", // 没有密码，默认值
	DB:		  0,  // 默认DB 0
})
```

### ssh连接 (与官网不同)

```go
var rdb *redis.Client

func init() {
	sshConfig := &ssh.ClientConfig{
		User:            "root",
		Auth:            []ssh.AuthMethod{ssh.Password("0723")},
		HostKeyCallback: ssh.InsecureIgnoreHostKey(),
		Timeout:         15 * time.Second,
	}

	sshClient, err := ssh.Dial("tcp", "192.168.226.136:22", sshConfig)
	if err != nil {
		panic(err)
	}

	rdb = redis.NewClient(&redis.Options{
		Addr: net.JoinHostPort("127.0.0.1", "6379"),
		Dialer: func(ctx context.Context, network, addr string) (net.Conn, error) {
			return sshClient.Dial(network, addr)
		},
		Password: "123456",
		// SSH不支持超时设置，在这里禁用
		ReadTimeout:  -2,
		WriteTimeout: -2,
	})
}
```

## 命令操作（针对五种数据类型）

不一一列举

### Get

```go
	val, err := rdb.Get(ctx, "goredistestkey").Result()
	if err != nil {
		panic(err)
	}
	fmt.Println("goredistestkey", val)
```

### HMSet

```go
	data := make(map[string]interface{})
	data["id"] = 1
	data["username"] = "lisi"

	// 一次性保存多个hash字段值
	err := rdb.HMSet(ctx, "key", data).Err()
	if err != nil {
		panic(err)
	}
```

### LPush

```go
	err := rdb.LPush(ctx, "nums", 1, 2, 3, 4, 5).Err()
	if err != nil {
		panic(err)
	}
```

### LRange

```go
	vals, err := rdb.LRange(ctx, "nums", 0, -1).Result()
	if err != nil {
		panic(err)
	}
	fmt.Println(vals)
```

### ZAdd

```go
	err := rdb.ZAdd(ctx, "key1", redis.Z{Score: 2.5, Member: "zhangsan"}).Err()
	if err != nil {
		panic(err)
	}
```

### ZRevRangeByScore

```go
	op := redis.ZRangeBy{
		Min:    "2",  // 最小分数
		Max:    "10", // 最大分数
		Offset: 0,    // sql的limit，表示开始的偏移量
		Count:  5,    // 一次返回多少数据
	}

	vals, err := rdb.ZRevRangeByScore(ctx, "key1", &op).Result()

	if err != nil {
		panic(err)
	}

	for _, val := range vals {
		fmt.Println(val)
	}
```

### 发布订阅

消息传输，包括发布者、订阅者和Channel

发布者和订阅者为Redis客户端，Channel为Redis服务端，发布者将消息发送到某个频道，订阅了这个频道的订阅者能接受这条消息

#### Subscribe

#### Publish

存在的问题：redis: discarding bad PubSub connection: ssh: tcpChan: deadline not supported
panic: ssh: tcpChan: deadline not supported

**使用ssh连接的redis， 在使用pubsub模块时不适配**
The pubsub module in go-redis doesn't support ssh-conn very well, you can consider using ReceiveTimeout. As for the <-chan message mode, it is currently not compatible with the ssh protocol.

#### PSubscribe

使用订阅通道channel支持模式的匹配

#### Unsubscribe

取消订阅

#### PubSubNumSub

查询指定的channel有多少订阅者

### 事务处理

redis事务可一次处理多个命令，需要以下两个重要保证

1. 事务是一个单独的隔离操作
   事务中的所有命令都会序列化、顺序执行，事务在执行过程中，不会被其他客户端发来的命令请求所打断
2. 事务是一个原子操作，事务中的命令要么全部被执行，要么全部不执行

Redis的事务 不是原子性的，不支持事务运行时错误的事务回滚

#### TxPipeline

以Pipeline方式操作事务

```go
// 开启一个TxPipeline事务
	pipe := rdb.TxPipeline()
	// 执行事务，通过pipe读写redis
	incr := pipe.Incr(ctx, "tx_pipeline_counter")
	pipe.Expire(ctx, "tx_pipeline_counter", time.Hour)
	// 通过Exec函数提交redis事务
	_, err := pipe.Exec(ctx)
	// 提交事务后，查询事务操作的结果
	// 执行Incr，在没有执行Exec函数之前，实际上没有允许
	fmt.Println(incr.Val(), err)
```

#### watch

乐观锁：每次获取数据的时候，都不会担心数据被修改，所以每次获取数据的时候都不会进行加锁，但是在更新数据库中的数据时需要判断该数据是否被别人修改过。如果数据被其他线程修改，则不进行数据更新，如果数据没有被其他线程修改，则进行数据更新。由于数据没有进行加锁，期间该数据可以被其他线程进行读写操作

```go
	// 定义一个回调函数，用于处理事务逻辑
	fn := func(tx *redis.Tx) error {
		// 先查询当前watch监听的key的值
		v, err := tx.Get(ctx, "key").Int()
		if err != nil && err != redis.Nil {
			return err
		}
		// 处理业务
		v++

		// 如果key的值没有改变，Pipelined函数才会调用成功
		_, err = tx.Pipelined(ctx, func(pipe redis.Pipeliner) error {
			// 给key设置最新值
			pipe.Set(ctx, "key", v, 0)
			return nil
		})
		return err
	}

	// 使用Watch监听一些Key，同时绑定一个回调函数fn，监听key后的逻辑在fn这个回调函数里面
	// 如果监听多个key，client.Watch(ctx, fn, "key1", "key2", "key3")

	for i := 0; i < 3; i++ {
		err := rdb.Watch(ctx, fn, "key")
		if err == nil {
			break
		}
		if err == redis.TxFailedErr {
			continue
		}
	}
```

没有懂？
