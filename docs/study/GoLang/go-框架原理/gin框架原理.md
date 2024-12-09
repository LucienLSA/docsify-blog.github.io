# gin框架原理
• 支持中间件操作（ handlersChain 机制 ）
• 更方便的使用（ gin.Context ）
• 更强大的路由解析能力（ radix tree 路由树 ）

## Gin 与 net/http 的关系
![Gin 与 net/http](gin-images/1.png)
在 net/http 的既定框架下，gin 所做的是提供了一个 gin.Engine 对象作为 Handler 注入其中，从而实现路由注册/匹配、请求处理链路的优化.

## 注册handler的流程

### 核心数据结构















