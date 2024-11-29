

**github项目:web-crawler**

## 豆瓣网站电影爬取电影（含GUI界面）
从排行榜和从影片关键词两种方式爬取电影信息
- 根据关键字搜索电影
- 根据排行榜(TOP250)搜索电影
- 显示IMDB评分及其他基本信息
- 提供多个在线视频站点，无需vip
- 提供多个云盘站点搜索该视频，以便保存到云盘
- 提供多个站点下载该视频

### 运行源码遇到的问题
#### 查找浏览器内核驱动版本
我使用的是edge

#### 源码使用chrome，而我使用edge
Python+selenium解决报错：SessionNotCreatedException session not created: No matching capabilities found

查看导包是否正确：

如果是webdriver.Chrome，那么导包的一切都要是chrome的
如果是webdriver.Firefox，那么导入的包也都源于firefox的包
出现这个问题是因为由于selenium很多类都很相似，如selenium.webdriver.firefox.options与selenium.webdriver.chrome.options，导致导包的时候容易出错，而代码本身是没有问题的。


#### 启动浏览器驱动器（这里我使用edge）
1. 选择好对应的浏览器驱动版本

2. Python报错：TypeError: __init__() got an unexpected keyword argument ‘executable_path‘
```python
service=Service('E:\\Study\\Tools\\edgedriver_win64\\msedgedriver.exe')
browser = webdriver.Edge(service=service, options=chrome_options)  # 设置chromedriver驱动路径
```

在 selenium 的 ‘3.141.0’ 及以上版本中，你应该使用 ‘service’ 参数而不是 ‘executable_path’ 来指定 chromedriver 的位置

#### 对浏览器驱动修改为绝对路径
爬虫Selenium报错“cannot find Chrome binary“解决方案，作为可尝试的一种方案
```python
service=Service('E:\\Study\\Tools\\edgedriver_win64\\msedgedriver.exe')
```
