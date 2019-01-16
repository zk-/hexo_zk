---
title: 移动端开发教程（一）
date: 2019-01-16 09:23:34
categories: [博客]
tags: [移动端]
---
第一节课我们主要是讲解移动端前端开发viewport相关的概念和搭建基础
<!-- more -->
# viewport的概念
在平常pc端开发的过程中，我们根据设计师给出的设计稿，按照设计稿里面每个元素的大小，在页面上用px这个单位按原样用css画出来，这在pc端开发是在正常不过的事，但是在手机上的移动端开发，我们却并不能这样。下面是一个例子。
```html
<!doctype html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Test01</title>
<style>
	*{margin: 0;padding: 0}
	.pic{
		width: 640px;
		height: 1136px;
		background-color: #FFE872;
		color: white;
		font-size: 150px;
		text-align: center;
		line-height: 1136px;
		font-family: cursive; 
	}
</style>
</head>
<body>
	<div class="pic"> Yellow </div>
</body>
</html>
```
Iphone5手机的分辨率为640x1136，我们在这个页面中用css画出了一个像素为640x1136的黄色方块，按照直觉在手机上应该满屏显示。但是实际效果不是这样，如下图。![iphone5](/images/mobile-adaptive/mobile-iPhone.png)
我们看到黄色方块没有预想的那样占满整个屏幕。原因是什么呢？
## 移动端1px并不等于设备的1px
在css中我们一般使用px作为单位，在桌面浏览器中css的1个像素往往都是对应着电脑屏幕的1个物理像素，这可能会造成我们的一个错觉，那就是css中的像素就是设备的物理像素。

但实际情况却并非如此，css中的像素只是一个抽象的单位，在不同的设备或不同的环境中，css中的1px所代表的设备物理像素是不同的。在为桌面浏览器设计的网页中，我们无需对这个津津计较，但在移动设备上，必须弄明白这点。

在早先的移动设备中，屏幕像素密度都比较低，如iphone3，它的分辨率为320x480，在iphone3上，一个css像素确实是等于一个屏幕物理像素的。后来随着技术的发展，移动设备的屏幕像素密度越来越高，从iphone4开始，苹果公司便推出了所谓的Retina屏，分辨率提高了一倍，变成640x960，但屏幕尺寸却没变化，这就意味着同样大小的屏幕上，像素却多了一倍，这时，一个css像素是等于两个物理像素的。

其他品牌的移动设备也是这个道理。例如安卓设备根据屏幕像素密度可分为ldpi、mdpi、hdpi、xhdpi等不同的等级，分辨率也是五花八门，安卓设备上的一个css像素相当于多少个屏幕物理像素，也因设备的不同而不同，没有一个定论。

在移动端浏览器中以及某些桌面浏览器中，window对象有一个devicePixelRatio属性，它的官方的定义为：设备物理像素和设备独立像素的比例，也就是 devicePixelRatio = 物理像素 / 独立像素。css中的px就可以看做是设备的独立像素，所以通过devicePixelRatio，我们可以知道该设备上一个css像素代表多少个物理像素。例如，在Retina屏的iphone上，devicePixelRatio的值为2，也就是说1个css像素相当于2个物理像素。下图为devicePixelRatio的兼容性在各个手机浏览器上的表现
![devicePixelRatio的兼容性](/images/mobile-adaptive/300958496962833.png)
## 利用viewport对手机缩放进行控制
关于viewport已经有大神对此有详细的研究（[第一篇](https://www.quirksmode.org/mobile/viewports.html)，[第二篇](http://www.quirksmode.org/mobile/viewports2.html)，[第三篇](http://www.quirksmode.org/mobile/metaviewport/)），有兴趣的同学可以去看一下

我在开发移动设备的网站时，最常见的的一个动作就是把下面这个东西复制到我的head标签中
```html
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=0">
```
该meta标签的作用是让当前viewport的宽度等于设备的宽度，同时不允许用户手动缩放。也许允不允许用户缩放不同的网站有不同的要求，但让viewport的宽度等于设备的宽度，这个应该是大家都想要的效果，如果你不这样的设定的话，那就会使用那个比屏幕宽的默认viewport，也就是说会出现横向滚动条。


meta viewport 标签首先是由苹果公司在其safari浏览器中引入的，目的就是解决移动设备的viewport问题。后来安卓以及各大浏览器厂商也都纷纷效仿，引入对meta viewport的支持，事实也证明这个东西还是非常有用的。
meta viewport 有6个属性(暂且把content中的那些东西称为一个个属性和值)，如下：
```
width	        设置layout viewport  的宽度，为一个正整数，或字符串"device-width"
initial-scale	设置页面的初始缩放值，为一个数字，可以带小数
minimum-scale	允许用户的最小缩放值，为一个数字，可以带小数
maximum-scale	允许用户的最大缩放值，为一个数字，可以带小数
height	        设置layout viewport  的高度，这个属性对我们并不重要，很少使用
user-scalable	是否允许用户进行缩放，值为"no"或"yes", no 代表不允许，yes代表允许
```
# 利用rem对不同移动端进行适配
看一个例子
```html
.con {
      width: 10rem;
      height: 10rem;
      background-color: red;
 }
<div class="con">
        
</div>
```
效果图：
![rem](/images/mobile-adaptive/555.png)
我们在修改一下html根元素的font-size
```html
html {
    font-size: 25px;
}
.con {
      width: 10rem;
      height: 10rem;
      background-color: red;
 }
<div class="con">
        
</div>
```
效果图：
![rem](/images/mobile-adaptive/666.png)
由此我们可以知道rem的特性是根据根元素的font-size值来改变自身的值，我们可以给html设定不同的值，从而达到我们css样式中的适配效果。

例如我们要是在iPhone6下写一个元素来横向占满整个屏幕，那么代码可以这么写：
```html
html {
    font-size: 0.5px;
}
.con {
      width: 750rem;
      height: 10rem;
      background-color: red;
 }
<div class="con">
        
</div>
```
因为iPhone6的devicePixelRatio的值为2，在根据前面设置的meta，无缩放，我们可知道，在屏幕是1个物理像素为0.5个独立像素，所以设置font-size为0.5即可直接使用设计稿的尺寸。


但是这样只是适配了iPhone6，无法适配所有手机，所以在这里我们可以用js动态控制font-size
```js
var dpr = window.devicePixelRatio,
meta = document.getElementsByTagName("meta")[1];
meta.setAttribute('content', 'initial-scale=' + 1 / dpr + ', maximum-scale=' + 1 / dpr + ', minimum-scale=' + 1 / dpr + ', user-scalable=no');
; (function (doc, win) {
    var docEl = doc.documentElement,
        resizeEvt = 'orientationchange' in window ? 'orientationchange' : 'resize',
        recalc = function () {
            var clientWidth = docEl.clientWidth;
            if (!clientWidth) return;
            docEl.style.fontSize = clientWidth / 750 + 'px';
        };

    if (!doc.addEventListener) return;
    win.addEventListener(resizeEvt, recalc, false);
    doc.addEventListener('DOMContentLoaded', recalc, false);

})(document, window);
```
这段代码解决了几个痛点
> 这样就可以完全按照视觉稿上的尺寸来了。

> 解决了图片高清问题。

> 解决了border 1px问题（我们设置的1px，在iphone上，由于viewport的scale是0.5，所以就自然缩放成0.5px）