---
title: '函数式编程:用自带的js原型链方法解决问题'
date: 2016-10-23 12:28:21
categories: [博客]
tags: [数组,Math,函数式编程,原型链]
---

凡是存在的就是合理的，那些说函数式编程可读性差的，你就是不会啊！
<!-- more -->
其实代码写久了，有时候会对一个非常长的代码文件产生厌烦感，在阅读别人的代码或者回顾自己以前写的代码时候更是如此。
当你阅读到一个长长的for循环而没有注释这个for是用来干啥时，内心的戾气就更是要爆发出来。其实很多情况我们是没有必要写出这种臃肿的代码的，函数式也是可以做到。
# 前提
本篇会深入浅出的指出日常代码可以简化的地方，包括但是不止函数式编程，在以后的文章里，我们还会进一步探讨函数式编程的各种运用。
# 数组
在实际编码的过程中会遇到很多类数组的数据，或者一个长字符串排序等问题，以此我们可以接住数组中自带的原型方法去解决问题。
## Array.prototype.every()
这个方法是用来测试数组的所有元素是否都通过了指定函数的测试
> every 方法为数组中的每个元素执行一次 callback 函数，直到它找到一个使 callback 返回 false（表示可转换为布尔值 false 的值）的元素。如果发现了一个这样的元素，every 方法将会立即返回 false。否则，callback 为每一个元素返回 true，every 就会返回 true。callback 只会为那些已经被赋值的索引调用。不会为那些被删除或从来没被赋值的索引调用。
> callback 被调用时传入三个参数：元素值，元素的索引，原数组。

举个例子

```
// 检测数组中是否全都小于100的数字
var flag = [1，32，55，67，98，101，33，44，43].every(function(item， index， array){
	return item <= 100
})
console.log(flag) //结果为false，101大于100
```

通过这个例子我们已经看到every的作用，下面看看在实际场景中的运用
我们在写复选框组这个组件的时候，有一个“全选”这个复选框，当我们在手动勾选全部的子复选框时，全选框会自动勾选上，取消其中一个的时候，全选框就会取消勾选，在实际编码过程中，我们可能要写一个for循环来轮询一遍每个子组件的状态，现在我们可以用every实现

```
var allCheckBoxDom = document.getElementsByClassName('quanxuan')[0] //获取全选复选框
var childrenCheckBoxDoms = document.getElementsByClassName('zixuanze') //获取子选择框组，这是一个类数组
var flag = [].every.call(childrenCheckBoxDoms， function(item， index， array) {
	item.checked === true
})
if(flag){
	allCheckBoxDom.checked = true;
}else{
	allCheckBoxDom.checked = false;
}
```

这样就方便的实现了这个功能
> 对应的方法有Array.prototype.some()，其作用是只要数组中有一个满足条件，就会返回true，用法与every相同，在这里就不赘述了
## Array.prototype.map()
这个方法返回一个由原数组中的每个元素调用一个指定方法后的返回值组成的新数组。
举个例子
我们通过ajax获取到的一组数据，类似这样的
```
var jobs = [
	{name:'zk'，age:22}
	{name:'ab'，age:12}
	{name:'cd'，age:33}
	{name:'pol'，age:5}
]
```
如果我们想把这个数组里面的name提取出来组成一个数组，可以这样做
```
var nameList = jobs.map(function(item， index， array){
	return item.name
})
```
## Array.prototype.reduce()
这个方法接收一个函数作为累加器（accumulator），数组中的每个值（从左到右）开始合并，最终为一个值。
也就是说是一种迭代器，另一种意义上的for循环，它传给callback函数的参数有四个，分别是
previousValue 上一次调用回调返回的值，或者是提供的初始值（initialValue）
currentValue 数组中当前被处理的元素
index 当前元素在数组中的索引
array 调用 reduce 的数组
另外reduce还可以接受一个初始值
举个例子
```
[1，2，3，4].reduce(function(previousValue， currentValue， index， array){
	return previousValue + currentValue
})
```
其结果就是把数组里面的值求和，结果是10，如果给redece加一个初始值
```
[1，2，3，4].reduce(function(previousValue， currentValue， index， array){
	return previousValue + currentValue
}，5)
```
这样子结果就是15
# Math
Math是js自带的一个数学库，可以方便的做各种计算，我们编码过程中可以巧妙借用他来实现功能，避免冗余的代码。
## Math.max
看名字你们也知道和Math.min对应，求最大值，简单的用法是直接传值
```
console.log(Math.max(1，2，4，5，6，7)) //输出7
```
但是如果我们用于数组，就可以很简单的求出一个数组里面的最大值（或者最小值）
```
var array = [44，12，42，11，87，33，49，3，487，111]
var maxNum = Math.max.call(Math， array)
console.log(maxNum) //输出487
```
## Math.min
和Math.max用法相同，不再赘述

# 结语
函数式编程还有很多用法，这只是第一篇，我会在以后的文章里面介绍更多的用法。