---
title: requestAnimationFrame 动画(一)
date: 2016-10-21 12:21:35
categories: [博客]
tags: [js动画]
---

让我们写点能动的东西吧!
<!-- more -->
今天让我们写点有趣的东西,用js实现动画
# 动画的定义
在维基百科里面,动画是这样定义的:
> 动画是指由许多帧静止的画面，以一定的速度（如每秒16张）连续播放时，肉眼因视觉残象产生错觉，而误以为画面活动的作品。

也就是说每秒播放的画面张数决定了动画的流畅度,下面图1是每秒10帧的动画,图2是每秒2帧的动画
![每秒10帧的动画](/images/requestAnimationFrame/Animexample1.gif)图1
![每秒2帧的动画](/images/requestAnimationFrame/Animexample2.gif)图2

可以看到图1明显比图2的小球运动的流畅了很多,我们在js中也能实现类似的效果
根据最新的官方标准,画面更新的频率至少要达到每秒60次才能让肉眼感受到流畅的动画效果,即每一张画面占用时间是1000/60=16.6666ms
# 使用setTimeout实现动画
在以前我们要实现一个动画,可能要借助与js的定时函数setTimeout来实现,即类似下面的代码

```
setTimeout(function() {
	// do something here
	setTimeout(arguments.callee)
},1000/60)
```

这样子的缺点是有的,setTimeout不会立即执行,用过setTimeout的同学都知道,如果给setTimeout设置200ms后执行,通常需要等待大于200ms的时间函数才会被执行,这就造成了卡顿
(原因:setTimeout中的定时器并不是准确的时间，实际中它需要在执行完前面的函数后才定时执行)
# 使用requestAnimationFrame来实现动画
在最新的标准里面,js为我们提供了一个专门用来执行动画的函数 requestAnimationFrame ,调用它可以很完美的让函数在1000/60ms时执行,当然对应的自然有cancelAnimationFrame函数来取消执行动画
现在我们就用他来实现一个简单的让小球运动的动画:
```
var ball = document.getElementsByTagName('div')[0];
ball.style.left = getComputedStyle(ball)['left']; //设置好小球的初始值
requestAnimationFrame(function() {
	ball.style.left = (parseFloat(ball.style.left) + 5) +'px';
	if (parseFloat(ball.style.left) < 500) {
		requestAnimationFrame(arguments.callee)
	}
})
```
[点击这里预览效果](https://codepen.io/jackfred/pen/bwOBPV)
# 小例子
是不是很流畅?其实如果要说教程的话,到这里是可以结束了,很多很炫目的效果其实都是这些基础的代码搭建起来的,里面会涉及到很多数学知识,只要能理解清楚其中的数学原理,就可以很轻松的实现各种效果,最后让我们实现一个文章里面图1的效果,一个弹跳的小球
在此之前,我们要了解以下几个要点:
- 小球升起时候做减速运动,落下的时候做加速运动
- 小球如果从地面开是运动,肯定有一个向上的初速度
- 用requestAnimationFrame控制速度的话,在小球接触地面的时候,要将速度重置成原来的向上的速度

好,以下就是成果代码:
```
var ball = document.getElementsByTagName('div')[0];
var beginSpeed = 20,speed = 20,a = 0.5;// 设置好小球的初始速度,现速度和加速度
var beginHeight = getComputedStyle(ball)['top']
ball.style.top = beginHeight; //设置好小球的初始值
requestAnimationFrame(function() {
	ball.style.top = (parseFloat(ball.style.top) - speed) +'px';
	speed = speed - a;
	if (parseFloat(ball.style.top) < 500) {
		requestAnimationFrame(arguments.callee)
	}else{ //小球回落地面的时候,重置高度和速度
		ball.style.top = beginHeight;
		speed = beginSpeed;
		requestAnimationFrame(arguments.callee)
	}
})
```
[点击这里预览效果](https://codepen.io/jackfred/pen/PGXWPw)