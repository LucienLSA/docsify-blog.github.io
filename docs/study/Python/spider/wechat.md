

**github项目:web-crawler**

## 微信个人数据报告

微信个人数据报告，了解微信社交历史。现在，基于python对微信好友进行全方位数据分析，包括：昵称、性别、年龄、地区、备注名、个性签名、头像、群聊、公众号等。

其中，在分析好友类型方面，主要统计出你的陌生人、星标好友、不让他看我的朋友圈的好友、不看他的朋友圈的好友数据。在分析地区方面，主要统计所有好友在全国的分布以及对好友数最多的省份进行进一步分析。在其他方面，统计出你的好友性别比例、猜出你最亲密的好友，分析你的特殊好友，找出与你所在共同群聊数最多的好友数据，对你的好友个性签名进行分析，对你的好友头像进行分析，并进一步检测出使用真人头像的好友数据。

### 如何运行
```bash
# 跳转到当前目录
cd 目录名
# 先卸载依赖库
pip uninstall -y -r requirement.txt
# 再重新安装依赖库
pip install -r requirement.txt
# 开始运行
python generate_wx_data.py
```

### 如何打包成二进制可执行文件
```bash
# 安装pyinstaller
pip install pyinstaller
# 跳转到当前目录
cd 目录名
# 先卸载依赖库
pip uninstall -y -r requirement.txt
# 再重新安装依赖库
pip install -r requirement.txt
# 更新 setuptools
pip install --upgrade setuptools
# 开始打包
pyinstaller generate_wx_data.py
```


### 运行源码遇到的问题

#### 通过itchat生成二维码报错

由于安全原因，此微信号不能使用网页版微信。
[https://blog.csdn.net/ljhsq/article/details/122756196](xml.parsers.expat.ExpatError: mismatched tag: line 64, column 4)

#### 解决方案
安装以下的itchat版本，并清理缓存
```python
pip uninstall itchat
pip install itchat-uos==1.5.0.dev0
```
查看缓存
```python
pip3 cache dir
pip3 cache list
```
清理缓存
```python
pip3 cache purge
```


#### 使用python的pillow库报错

[https://blog.csdn.net/light2081/article/details/131517132](AttributeError: module ‘PIL.Image‘ has no attribute ‘ANTIALIAS‘)

在pillow的10.0.0版本中，ANTIALIAS方法被删除了，使用新的方法即可
```
Image.LANCZOS
Image.Resampling.LANCZOS
```

#### 解决方案
1. 将其中的ANTIALIAS替换为新方法(在本项目中有效)
```python
# img = img.resize((eachsize, eachsize), Image.ANTIALIAS)
img = img.resize((eachsize, eachsize), Image.LANCZOS)
```
2. 降级Pillow的版本，比如使用9.5.0版本
```sh
pip uninstall -y Pillow
pip install Pillow==9.5.0
```


## 定时发送消息给女友
由于itchat等微信机器人停止维护，不方便使用




