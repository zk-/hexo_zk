---
title: 用js来做图像处理（二）
date: 2017-02-22 08:55:00
categories: [博客]
tags: [canvas,高斯模糊,灰度处理,反色,图像处理]
---
高斯模糊是根据一个像素周围的像素的值通过复杂的计算获得一个与周围像素近似值的技术，如果只是简单的判断，那就是马赛克了。
<!-- more -->
资料参考：阮老师的[高斯模糊的算法](http://blog.csdn.net/jiandanjinxin/article/details/51281828)
# 基础知识
读过阮老师的文章我们知道以下几点
- 所谓"模糊"，可以理解成每一个像素都取周边像素的平均值。
- "中间点"取"周围点"的平均值，就会失去细节。
- 取值范围越大，"模糊效果"越强烈。
- 正态分布显然是一种可取的权重分配模式。
- 利用状态发布的权重值可以算出中心点合适的加权平均值。
- 图像都是二维的，所以我们需要二维正态分布。
- 如果一个点处于边界，周边没有足够的点，就把已有的点拷贝到另一面的对应位置，模拟出完整的矩阵。

矩阵函数如下：
![canvas](/images/images2canvas/canvas5.png)
# 实现
有了理论和函数支持，做起来就比较容易了
我们先尝试做一个模糊半径为1像素的图。
在半径为1像素的情况下，中心点需要知道周围8个点的权重值，利用前面的函数，写出下面的代码
```javascript
var lisanzhi = 0.9;  //离散值
var r = 1;  //设半径为1像素
var zhijing = 2*r + 1;  //直径
var pointNums = zhijing*zhijing;  //需要计算点的数量
var middleValue = Math.ceil(pointNums/2);  //中值
var temp = 1/(2 * Math.PI * lisanzhi * lisanzhi);  //公式中一样的下半部分
var gaosiData = [];  //存储结果
for (var i = 0; i < pointNums; i++) {
	var x = Math.abs(i%zhijing - r);  //获取对应值的x、y值
	var y = Math.abs(r - parseInt(i/zhijing));
	gaosiData[i] = Math.pow(Math.E , 0 - (x*x + y*y)/(2 * lisanzhi * lisanzhi)) * temp;  //套用公式计算
}
var sum = gaosiData.reduce(function(a,b) {  //求和
		return a + b;
	})
gaosiData = gaosiData.map(function(item) {  //求权重
	return item/sum;
})
```
结果如下
![canvas](/images/images2canvas/canvas6.png)
接下来就是求中心点周围的点在数组数据中的下标值，实现如下
```javascript
var roundArray = [],  //用来存放结果
maxIndex = canvas.width * canvas.height * 4;  //数组数据中最大下标
for (var k = 0; k < pointNums; k++) {  //根据下标和中心点的下标距离，求出真正下标
	var juli = k - middleValue + 1,
	beishu = parseInt(juli/zhijing),
	yushu = juli%zhijing;
	roundArray[k] = i + 4*(beishu*canvas.width+yushu);
}
for (var j = 0; j < roundArray.length; j++) {  //因为部分点在边缘，所以一些下标超过正常范围的就取对应点的下标
	if (roundArray[j] < 0 || roundArray[j] > maxIndex) {
		roundArray[j] = roundArray[pointNums - 1 - j];
	}
}
```
做完这些，我们就可以根据前面两步的结果，计算最终中心点的值了
```javascript
var temp=0,temp1=0,temp2=0;
for (var m = 0; m < pointNums; m++) {  //求和
	temp += imageDatas.data[roundArray[m]] * gaosiData[m];
	temp1 += imageDatas.data[roundArray[m]+1] * gaosiData[m];
	temp2 += imageDatas.data[roundArray[m]+2] * gaosiData[m];
}
imageDatas.data[i] = temp;
imageDatas.data[i+1] = temp1;
imageDatas.data[i+2] = temp2;
imageDatas.data[i+3] = 255;
```
效果图如下
![canvas](/images/images2canvas/canvas7.png)
# 结论
这一篇是在前面的文章基础上一步步推导过来的，高斯模糊本身不难，只要掌握原理即可很容易做到，本篇所写代码没有做性能优化，读者可以进一步开发，优化性能，功能解耦。
实验平台：[点这里](https://codepen.io/jackfredzk/pen/egqBPG)