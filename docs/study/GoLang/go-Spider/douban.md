---
title: go语言爬虫项目-豆瓣Top电影爬取
katex: true
categories: 
- go
tags:
- spider
---

[https://www.bilibili.com/video/BV1CR4y1g7wB/?vd_source=917ef87e48a267f0acc88f766dea0a6e]

##
静态信息，使用xpath/css，正则去匹配需要的节点

## 
1. 发送请求

2. 解析网页

3. 获取节点信息
正则表达式，对节点信息进行分割处理

4. 保存信息
数据库定义结构体、服务端配置连接数据库、初始化数据库、插入数据

## 并发
1. channel 并发
2. wait 并发