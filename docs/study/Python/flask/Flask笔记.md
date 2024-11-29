
# 遇到的问题

## 在pycharmIDE中实现flast的项目

## ImportError: cannot import name 'url_quote' from 'werkzeug.urls'

Flask没有正确指定依赖项（需求显示Werkzeug>=2.2.0）

```bash
pip install Werkzeug==2.2.2
```

# 学习记录

## debug模式、修改host和修改端口号

### debug模式

1. 开启debug模式，只需修改代码后保存，自动重新加载，不需手动重启
2. 开发出现bug，浏览器可看到出错信息

### 修改host

作用：让其他电脑访问到flask项目

### 修改port端口号

作用：某端口被占用，则通过修改port来监听

## URL与视图

### url

http协议都是用80端口，https协议都是用443端口，浏览器自动带上端口

1. 带参数url
2. 查询字符串方式传参

### 视图函数

## 模板渲染

写html文件，后端进行传参，前端代入指定参数。实现网页前后端渲染

### 模板访问对象属性

render_template() # 传入对象，设置其属性
