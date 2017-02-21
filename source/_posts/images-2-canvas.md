---
title: 用js来做图像处理（一）
date: 2017-02-21 14:49:24
categories: [博客]
tags: [canvas,高斯模糊,灰度处理,反色,图像处理]
---
所有能被js实现的东西，最终都会以js的方式被实现
<!-- more -->
# 概述
正如上面所说，昨天开始打算尝试能不能用js做一些图像处理的工作，直到现在，本人已经用js下的canvas实现了图像的灰度处理，反色，以及高斯模糊等功能。
资料参考：阮老师的[高斯模糊的算法](http://blog.csdn.net/jiandanjinxin/article/details/51281828)
# 基础知识
## 导入图像
canvas导入图像非常简单，使用canvas自带的drawImage函数即可导入
```
var image = new Image;
image.src = './images/mm1.jpg'
image.onload = function (){
	//在图像加载完成的时候导入
	ctx.drawImage(image,0,0)  //后面的两个参数是导入图片的坐标
}
```
效果如下，左图为原图，右图为canvas导入的图
![canvas](/images/images2canvas/canvas.png)
## 获取图像数据
canvas在这里也为我们提供了简便的函数getImageData，用这个函数可以获取目标区域里图像的__数组数据__。
```
canvas.width = width;
canvas.height = height;
ctx.drawImage(image,0,0);
var imageDatas = ctx.getImageData(0,0,width,height);  //参数意思是坐标已经x、y轴的偏移量
console.log(imageDatas)
```
获取数据如图
![canvas](/images/images2canvas/canvas1.png)
## 重绘图像
上面我们拿到了图像数据，这时候假如我们处理好了，怎么用新数据重绘呢？
可以直接用canvas自带的putImageData函数
```
ctx.putImageData(imageDatas,0,0)
```
## 关于图像数据
我们知道图像的每一个像素的颜色都有rgb三个颜色构成，canvas还增加了一种，就是透明度，所以在canvas中每一个像素都是有rgba值的。
对应到数组数据中，__每四个数组元素表示一个像素的rgba值__。
由此我们可以做个小练习，图像的第二排第一个像素的位置是 0 + 4 x (图像宽度 + 1)。
![canvas](/images/images2canvas/canvas2.png)
基础只是已经了解的差不多了，现在开始简单的尝试
# 灰度处理
把图像做灰度处理很简单，只要把每个像素的rgb三值做成一样的就可以了，因为每个像素的rgb的值不同，所以我们可以取平均值。代码如下
```
for (var i = 0; i < imageDatas.data.length; i+=4) { //每四个元素代表一个像素
	var ddd = (imageDatas.data[i]+imageDatas.data[i+1]+imageDatas.data[i+2])/3;
	imageDatas.data[i]=ddd;
	imageDatas.data[i+1]=ddd;
	imageDatas.data[i+2]=ddd;
	imageDatas.data[i+3]=255; //透明度就最高即可
}
```
效果如下
![canvas](/images/images2canvas/canvas3.png)
# 反色
反色的方法是取对应rgb中相反的值，即用255减去对应的值，结果为所求的值。
```
for (var i = 0; i < imageDatas.data.length; i+=4) { //每四个元素代表一个像素
	imageDatas.data[i]=255-imageDatas.data[i];
	imageDatas.data[i+1]=255-imageDatas.data[i+1];
	imageDatas.data[i+2]=255-imageDatas.data[i+2];
	imageDatas.data[i+3]=255; //透明度就最高即可
}
```
效果如下
![canvas](/images/images2canvas/canvas4.png)
# 结语
第一篇主要是介绍canvas下处理图像数据需要的一些基本知识，以及灰度处理和反色两个简单的图像处理技术，下一章会重点介绍如何对图像进行高斯模糊，以及高斯模糊的算法。