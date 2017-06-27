---
title: impress简易教程
date: 2016-12-23 10:26:37
categories: [博客]
tags: [impress,js框架]
---
快过年了，作为一个前端工程师，要写年终总结ppt啥的了，这里介绍一个炫酷的js框架，用网页实现比ppt还炫酷的效果。
<!-- more -->
# 效果展示
[聚众编码！](https://juzoom.github.io/)
# 原理介绍
这里用的impress.js框架，这个框架帮你做到的是，把指定的div标签变成一个个独占整个页面的元素，通过给出的接口进行定位，是三维上的定位！然后通过js控制把每个元素转到当前窗口来，在浏览器支持css3的情况下使用css3的动画功能来实现的。
# 项目地址和文档地址
[项目地址](https://github.com/impress/impress.js)
[文档地址](https://github.com/impress/impress.js/blob/master/DOCUMENTATION.md)
# 起步
首先要引入impress.js文件，和普通的js引入方式是一样的
然后在文档里加入代码
```html
<div id="impress"></div> //id可以自定义
```
然后在引入的impress文件下插入这段代码
```javascript
impress( "root" ); //这里是你的id，如果id是impress，则这一步省略
impress().init();
```
# 插入ppt
## 基础
在你指定的根div中间插入ppt类的div
```html
<div id="impress">
	<div id="test" class="step" data-x="0" data-y="0">
	    test
	</div>
</div>
```
这里div的class必须要有'step'，id可以有可以没有，如果没有，impress就会把你的id默认变成'step-1'这样的。
'data-x'和'data-y'这样的就是ppt的定位
## 2D平移
如果我们再加入一段ppt类的div，页面就可以平移动起来了
```html
<div id="impress">
	<div id="test1" class="step" data-x="0" data-y="0">
	    test1
	</div>
	<div id="test2" class="step" data-x="1600" data-y="0">
	    test2
	</div>
</div>
```
## 缩放
我们想加入缩放的话只要加入'data-scale'这个属性就可以了，赋值为数字，越大缩放越大
```html
<div id="test" class="step" data-x="1600" data-y="0" data-scale="3">
    test
</div>
```
## 2D旋转
如果想做旋转效果的ppt，加入'data-rotate'属性，赋值范围0~360
```html
<div id="test" class="step" data-x="1600" data-y="0" data-rotate="90" data-scale="3">
    test
</div>
```
## 3D定位
加入'data-z'属性实现3D空间上的定位
## 3D翻滚
可能最炫酷的效果就是这个了，需要配合3D定位，属性有两个'data-rotate-x'和'data-rotate-y'，分别实现横向和纵向翻滚。
```html
<div id="test" class="step" data-x="6200" data-y="4300" data-z="-100" data-rotate-x="-40" data-rotate-y="10">
    test
</div>
```
# 方法接口
impress还给出了方法接口
## .init()
初始化用途
```javascript
var api = impress();
api.init();
```
## .next()
切换下一个ppt的功能
```javascript
var api = impress();
api.init();
api.next(); //使用方法下面都一样
```
## .prev()
切换前一个ppt的功能
## .goto( stepIndex | stepElementId | stepElement, [ duration ] )
切换到指定ppt的功能，接受id和数字和dom元素，还能配置切换时间
# 事件监听接口
impress给出了两个事件监听接口
impress:stepenter 切入当前ppt的事件
impress:stepleave 切出当前ppt的事件
```javascript
var rootElement = document.getElementById( "impress" );
rootElement.addEventListener( "impress:stepenter", function() {
	var currentStep = document.querySelector( ".present" );
	console.log( "Entered the Step Element '" + currentStep.id + "'" );
});

rootElement.addEventListener( "impress:stepleave", function(event) {
	var currentStep = event.target
	console.log( "Left the Step Element '" + currentStep.id + "'" );
});
```
# 样式
impress还给出了一些样式特效，具体的读者自己去文档看吧！我就偷懒啦！