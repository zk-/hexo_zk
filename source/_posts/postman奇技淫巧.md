---
title: postman奇技淫巧
date: 2016-10-22 12:20:19
categories: [工具]
tags: [爬虫,chrome应用,测试]
---

postman是chrome浏览器应用市场里面的一款神器，测试人员的好帮手，黑客手里的爬虫利器。
<!-- more -->
# postman介绍
postman是一个用来方便构造各种各样网络请求的工具，他的平台是构建在chrome上的。因为强大有用的功能被封为神器，可惜没有中文支持，让很多初学者望而却步，但是只要坚定的去摸索使用这个工具，你就会感受到他的强大而且丰富的功能。
本文打算简单的介绍下基本使用功能，postman里面的功能非常丰富，一篇文章是说不完的，所以我打算主要说一说我在使用postman中的一些心得，发现的一些小技巧。

# 使用前的准备
在学习使用前你需要知道以下几个小知识：
- postman的基本原理不是用我们平常熟知的，用js去构建XHR这种前端发送请求的方法去构建数据包的，因为他是chrome里面的应用，chrome给应用提供了许多接口，其中就包含了sockets接口（[文档在这里](https://developer.chrome.com/apps/socket)），这个接口支持TCP和UDP协议，使得chrome应用网络通信成为可能，所以postman是使用chrome里面的接口，把chrome当作网络爬虫一样的工具来实现发送数据包的，所以我们要把他当作一个独立的应用程序来看待。
- postman是一个chrome应用而不是chrome插件，定位不同所以功能是有区别的。
- chrome应用市场[地址](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop?utm_source=chrome-ntp-icon)
- 使用插件不需要翻墙，但是应用需要登录才能用，登录需要翻墙，翻墙手段自己摸索:)

# postman基本用法
首先看整个界面，图1
![主界面](/images/postman/postman1.jpg)图1
1 Runner 测试人员用来进行集成化测试的功能，要配合Collections使用
2 用来传递测试数据的，就是用户之间分享构建好的测试数据包
3 History 记录测试历史，每一次发送数据包就会产生一个历史数据
4 Collections 用来保存你构建好的数据包，登录用户且在翻墙的情况下，会自动同步你的数据，当然可以仔设置里面关掉
5 我们主要的工作环境-构建器，稍后会详细说明
6 配合postman的另一个chrome插件，注意是插件，其作用是接受来自目标服务器的cookies
7 postman设置，可以设置操作习惯，主题和同步等等

# postman构建器的基本使用
下面我们就先来尝试构建一个数据包
这里我们用来做实验的例子是向[多说](https://duoshuo.com/)发送请求获取文章的评论数，以及转发数
相关的API文档在[这里](http://dev.duoshuo.com/docs/50615732a834c63c56004257)

从文档我们知道有以下几个要点：
- 请求地址是http://api.duoshuo.com/threads/counts.json（可选有json或者jsonp）
- 请求方式是GET
- 需要两个参数，一个short_name，一个threads
- 不需要登录
- 我在这里可以提供下测试数据 short_name:jackfred   threads:2016/10/22/postman奇技淫巧/

ok,让我们现在开始搞事情 ![搞事情](/images/emotions/gsq.jpg)

见图2
![构建请求](/images/postman/postman2.jpg)
1 选择请求方式GET
2 填入地址

见图3
![构建请求](/images/postman/postman3.jpg)
1 点击
2 填入参数

最后我们点击Send按钮，成功！
![成功结果](/images/postman/postman4.jpg)
我们看到有一个评论，一个喜欢，对比下结果是正确的（喜欢是我自己点的，怎么啦）

# 奇技淫巧（重点）
前面我们已经学会了怎么构造一个简单的测试数据，先不要慌着关掉窗口，重点来了，postman为用户提供了一个非常贴心的功能
因为postman可以非常详细的自定义数据包，构建headers cookies 包括各种登录Authorization，如果我们想用各种编程语言构建一个一模一样的数据，肯定免不了要各种编写调试一下，在这里postman提供了一个非常让人暖心的功能
点击这里
![code](/images/postman/postman5.jpg)
我们发现postman竟然可以为你提供各种语言版本构建器的代码！
![code](/images/postman/postman6.jpg)
点击左上角可以切换语言！
![code](/images/postman/postman7.jpg)
如图，nodejs的构建器代码

这样我们就能很方便的去把这些代码选择粘贴到我们的工作环境中，而且不用担心代码有问题！

文章到这里就结束了，postman还有很多强大的功能等你发掘，以后我也许还会在以后的文章理继续介绍postman  :)