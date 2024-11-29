
## 权限问题

转载链接
[https://blog.csdn.net/qq_43429800/article/details/113759264](Error: EPERM: operation not permitted, open )

Error: EPERM: operation not permitted, open
node_cache 和 node_global 文件夹没有修改权限导致的，cmd用管理员权限打开，或者在node_cache 和 node_global 这两个文件夹的属性-安全里面赋予修改权限就ok

## 文件或者目录不存在

重新安装npm、查看目标文件和目录


## hexo s和g命令出错

hexo g 命令出现 page.tags.forEach is not a function 或者 TypeError: data.tags.forEach is not a function hexo

[https://github.com/blinkfox/hexo-theme-matery/issues/187](hexo g 命令出现 page.tags.forEach is not a function)

### 解决方案

发现是 source 目录下， _post 目录之外的 page 文章不能有 tag 标签，去除后问题得到解决。


## hexo Nunjucks Errors 
报错：Nunjucks Error: _posts/Go/ginFrame/Basic.md [Line 31, Column 40] unexpected token: .
```
在 md 文件代码块中存在 {{ 和 }} 符号时渲染会出现报错：
```
### 解决方案
```
1. 目前我的解决方案是在 {{ 等中间添加空格 { {
2. hexo 官网也有提出使用 {% raw %}  {% endraw %} 来包裹代码块中 {{ }} 代码
3. 使用 &#123 和 &#125 来转义 { 和 }
```

