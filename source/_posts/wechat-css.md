---
title: 微信小程序的知识补充和答疑
date: 2019-04-17 09:04:47
categories: [博客]
tags: [微信小程序]
---
通过9节课培训的结果，了解到了部分同学还存在某些小程序相关的疑问，在这里补充一下相关的知识，以及答疑。
<!-- more -->
# 布局问题
在这里我们主要讨论行内元素、块级元素、以及行内块级元素，元素的浮动在小程序里面会有怪异的表现，这里就不讨论了

行内元素会在一条直线上排列（默认宽度只与内容有关），都是同一行的，水平方向排列。
块级元素各占据一行（默认宽度是它本身父容器的100%（和父元素的宽度一致），与内容无关），垂直方向排列。块级元素从新行开始，结束接着一个断行。
行内元素与块级元素属性的不同，主要是盒模型属性上：行内元素设置width无效，height无效(可以设置line-height)，margin上下无效，padding上下无效。
而行内块级元素即可以设置宽高的特性，同时又具有 inline 元素默认不换行的特性
```html
<text class='ha'>行内元素a</text>
<text class='hb'>行内元素设置了宽高b</text>
<view class='ka'>块级元素a</view>
<view class='kb'>块级元素设置了宽高b</view><view class='oa'>行内块级元素a</view><view class='ob'>行内块级元素b</view>
```
```css
.ha{
  color: red;
}
.hb{
  width: 200rpx;
  height: 200rpx;
}
.ka{
  background: red;
}
.kb{
  width: 200rpx;
  height: 200rpx;
  background: blue;
}
.oa{
  display: inline-block;
  width: 200rpx;
  height: 200rpx;
  background: red;
}
.ob{
  display: inline-block;
  width: 400rpx;
  height: 400rpx;
  background: blue;
}
.ka,.kb,.oa,.ob{
  color: white;
  vertical-align: top;
}
```
![微信小程序](/images/weixinxiaochenxu/block.png)

在上一节课，有同学问我，有时候设计图，想让一个元素居右，要怎么写，虽然小程序里面float不好用，但是想实现的方法还是有很多的，先结合本篇文章说一个方法
第一种方法，通过填充的方式使元素居右
```html
<view class='a'></view>
<view class='b'></view>
```
```css
.a{
  width: 60%;
  height: 50rpx;
  background: red;
  display: inline-block;
}
.b{
  width: 40%;
  height: 50rpx;
  background: blue;
  display: inline-block;
}
```
第二种，巧用行内元素的text-align属性
```html
<view class='a'>
  <view class='b'></view>
</view>
```
```css
.a{
  text-align: right;
}
.b{
  width: 50rpx;
  height: 50rpx;
  background: blue;
  display: inline-block;
}
```
第三种方法，就是使用flex布局实现，具体知识可以看我以前的文章[flex布局](http://www.jackfred.cn/2019/01/22/flex-mobile/)
```html
<view class='a'>
  <view class='b'></view>
</view>
```
```css
.a{
  display: flex;
  justify-content: flex-end;
}
.b{
  width: 50rpx;
  height: 50rpx;
  background: blue;
}
```
# this指向问题
在小程序中，全局环境下，this指向的是undefined（与js不同）
当函数作为对象里的方法被调用时，它们的 this 是调用该函数的对象。
```javascript
var o = {
  prop: 37,
  f: function() {
    return this.prop;
  }
};

console.log(o.f()); // logs 37
```
当一个函数在其主体中使用 this 关键字时，可以通过使用函数继承自Function.prototype 的 call 或 apply 方法将 this 值绑定到调用中的特定对象。
```javascript
function add(c, d) {
  return this.a + this.b + c + d;
}

var o = {a: 1, b: 3};

// 第一个参数是作为‘this’使用的对象
// 后续参数作为参数传递给函数调用
add.call(o, 5, 7); // 1 + 3 + 5 + 7 = 16
```
想了解更多关于this的知识，可以看看我以前的文章：[js的继承](http://www.jackfred.cn/2016/10/26/js%E7%BB%A7%E6%89%BF%E7%9A%84%E6%96%B9%E5%BC%8F%E5%92%8C%E6%84%8F%E4%B9%89/)
# wxs用法
这是对于小程序方面知识介绍的最后一点补充
wxs是在小程序中一个比较鸡肋的功能（个人见解），其作用就是可以让你在wxml文件中插入js脚本，js脚本需要统一的通过module.exports来输出一个对象或者值。在后面的代码中则可以调用它。
```html
<wxs module="test">
module.exports = {
    test:function(a){
        return a + ' test';
    }
}
</wxs>
<view>{{test.test(data)}}</view>
```
这个功能不能从上下文中获取页面的数据，只能内部自定义一些数据，使用上和声明一个函数没什么区别，而且插在wxml文件中还会增加代码的混乱度，所以我觉得是个比较鸡肋的功能，但是在微信小程序官方文档里，它依旧给了不少的篇幅介绍，所以我在这里补充一下。