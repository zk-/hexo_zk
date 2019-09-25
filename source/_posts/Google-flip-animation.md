---
title: Google的动画技术Flip初探
date: 2019-09-25 12:18:04
categories: [博客]
tags: [js动画]
---
最近对Vue的`<transition-group>`的动画效果感兴趣，于是研究了一下实现原理。
<!-- more -->
在官方文档中，内部的实现，是Vue 使用了一个叫 FLIP 简单的动画队列，一个来自谷歌官方的动画技术。  
搜了一下国内的博客，发现对其介绍非常的少（实际上就只有1篇，剩下的都是互相抄，换汤不换药）  
在一番摸索后，我大概了解了这个技术，下面尽量用简单的语言说一下Flip的原理和使用。
# 什么是Flip？
简单来说，就是在css动画中，除了 transform 和 opacity 之外的动画开销都比较大（包括left、top等定位元素），动画会有一点迟滞，因为浏览器必须递归检查其他元素的布局是否因此而改变。
所以Flip就是一个仅仅通过transform这个属性来实现流畅无延时的动画的技术。
# Flip的实现原理
简单来说，Flip就是记录一个元素变化前和变化后的状态,然后再通过js模拟重播这一动画过程，（注：Flip的技术思维还可以用作更高级的动画运用，这篇文章只探讨初级的运用）  
这里记录元素的状态包括元素的位置的长宽。
Flip是First, Last, Invert, Play的缩写，引用一下官方对此的解释。
- First 元素的初始状态。 在发生任何事情之前，记录将要过渡的元素的当前（即，first）位置和尺寸。 可以使用element.getBoundingClientRect() 来做到这一点，如下所示。
- Last 执行导致过渡瞬时发生的代码，并记录元素的最后（即 last）位置和尺寸。
- Invert 由于元素处于最后位置，所以我们希望通过使用transform修改其位置和尺寸来创建它处于第一位置的错觉。这需要一点数学，但不是太困难。
- Play 将元素倒置（假装在第一个位置），我们可以通过将其 transform 设置为 none，将其移回到最后位置。

# 官方Flip使用教程
因为官方没有正式的文档，只有demo和视频教程（这一点有点让人无语。。）而demo实现的效果虽然很赞，但是代码太多了，实现的复杂，对于初学者不是很友好，所以我在这里做个最简单的演示。
```html
<script src="https://raw.githubusercontent.com/googlearchive/flipjs/master/dist/flip.js"></script>
<style>
.hide-box{
    display: none;
    width: 200px;
    height: 200px;
}
.flip-box{
    width: 200px;
    height: 200px;
    font-size: 40px;
    line-height: 200px;
    text-align: center;
    font-weight: bold;
    box-shadow: 0 0 7px 1px rgba(222, 222, 222, 0.9);
}
</style>
<div class="content">
    <div class="hide-box" id="hide"></div>
    <div class="flip-box" id="flip">Flip</div>
</div>
```
在这里我们引入了官方的Flip.js，然后创建了两个div，一个是实验对象flip-box，还有一个在其文档流上方的隐藏div  
效果图：
![flip](/images/flip/flip.png)
```javascript
var hidebox = document.querySelector("#hide");
var flipbox = document.querySelector("#flip");
let flip = new FLIP({  //初始化
    element: flipbox,
    duration: 500
});
flip.first(); //记录flipbox的初始状态
hidebox.style.display = "block";// 能够引起flipbox位置和形状变化的操作
flip.last(); //记录被挤下去的flipbox的状态
flip.invert();  //还原flipbox的初始状态
flip.play(); //播放动画
```
这里我们直接让隐藏的div显示，下面的flipbox会被挤下去，这里是效果图：
![flip](/images/flip/flip.gif)
我们注意到这里动画是平滑过渡过去的，可能比较单调，官方在这里预留了运动曲线函数，可以高度自定义。
```javascript
let flip = new FLIP({
  element: target,
  easing: function (t) {
    return t * t;
  }
});
```
## 如何在react和vue中使用
基于Flip的思想，我们发现实际上和现有的主流框架的生命周期思想是不谋而合的，所以使用起来也非常容易。  
只需要在元素初始生命周期调用Flip记录状态，在元素更新时的生命周期记录并播放即可。
```javascript
mounted(){
    flip.first();
},
updated(){
    flip.last();
    flip.invert();
    flip.play();
}
```
我们也可以直接封装成一个mixin文件，这样就可以在我们需要动画的元素上一键加上动画效果。