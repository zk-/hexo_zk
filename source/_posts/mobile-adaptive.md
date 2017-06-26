---
title: 移动端适配讲解
date: 2017-06-26 13:42:26
categories: [博客]
tags: [移动端]
---
从乔布斯推出智能手机以来，市面上已经有各种各样的牌子不同大小的智能手机，手机的分辨率也各有不同，同时移动互联网的发展，移动端网页技术也开始被人重视，初次接触移动端的同学曾经可能也和我一样迷茫过，在公司要求做一些移动端的网页时，没有足够的移动开发经验和基础知识，慌忙的在网上选择“阿里移动端适配方案”或者“网易移动端适配方案”，网页做完了，可是还是会迷惑为什么传统的网页在不同的手机上表现都如此不同，还有rem是什么，viewport又是什么？
今天就带大家梳理一下知识点，打好基础的同时选择适合自己的开发方案。
<!-- more -->
# 引入问题
首先我们先看这么一段代码
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
同时我们在浏览器模拟手机时，看到工具栏上方还显示这这样一个数据![iphone5](/images/mobile-adaptive/mobile-iPhone1.png)
为啥会显示320x568呢，还有DPR是什么？
# 什么是DPR？
DPR的全程是设备像素缩放比(device PixelRatio)。
因为屏幕尺寸大小的相同的手机分辨率是不一样的，Iphone5的分辨率为640x1136，这就代表手机的屏幕上有640x1136个物理像素，但是屏幕尺寸320x568那么大，所以需要缩放以显示。
在这里640÷320=2，同时1136÷568=2，所以DPR=2，我们一般把DPR大于等于2的屏幕称为高清屏(retina屏)。 由此可知，同样像素大小的元素在iPhone5上会缩小一倍。
## 进一步的疑问
我们解释了DPR是什么，但是还是会有疑惑，因为从理论上来说，即使这样，黄色方块也应该占满全部屏幕啊，不应该像图片那样，既不占满屏幕，也不是屏幕大小的1/2，更不是屏幕的2倍，这就很让人疑惑。
而这些呢，也牵扯到一段手机厂商的历史，在手机厂商制造手机时，考虑到自家手机可能会访问一些pc端的网页，为了有一个良好的页面体验，会默认将屏幕的宽度定为某一个默认值，然后在页面超出这个默认值的时候，又会自动缩放，让屏幕的宽度刚好可以适应整个网页。
在这里我们可以做个实验，如下图![iphone5](/images/mobile-adaptive/mobile-iPhone2.gif)
从这张图上我们可以看到元素宽度在980px的时候正好占满屏幕，所以iPhone5的默认宽度是980px，而当元素宽度大于980px的时候，从元素的高度可以看出屏幕明显将元素进行了缩放已达到完整显示的目的。
# 如何控制浏览器缩放？
可以说手机厂商自作聪明的作法(至少我是这么认为的)给很多前端开发者带来了麻烦，因为手机各自的分辨率不同，默认宽度不同，同样的页面在不同的手机上显示可能就会错位，如果用百分比做度量单位，就无法解决元素高度(高度的百分比是父元素的宽度的比例)还有边框宽度和元素定位等问题。
在这里我们可以通过meta标签的viewport来控制页面缩放。
```html
<!doctype html>
<html lang="en">
<head>
<meta name="viewport" content="width=640">
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
如图所示，我们在meta头设置了viewport的值为"width=640"，这样手机的默认宽度就定成了640px，元素正好覆盖了全部页面。![iphone5](/images/mobile-adaptive/mobile-iPhone3.png)
可是还有缺陷，就是浏览器还是会自动缩放。
这时候我们在meta表现中加入这行代码
```html
<meta name="viewport" content="width=640,maximum-scale=1, minimum-scale=1,initial-scale=1,user-scalable=no">
```
在这里scale的意思是缩放，maximum-scale和minimum-scale是最大缩放和最小缩放倍数，initial-scale为初始缩放倍数，user-scalable代表用户是否可以缩放。
此时页面效果变成了下面这样![iphone5](/images/mobile-adaptive/mobile-iPhone4.gif)
这是因为禁止了页面缩放，而iPhone5的屏幕像素大小只有320，所以页面是屏幕的2倍，且不可缩放。
如果还是需要达到我们之前满屏的目的，则需要改动viewport的width值和元素的宽度值。
```html
<!doctype html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=320,maximum-scale=1, minimum-scale=1,initial-scale=1,user-scalable=no">
<title>Test01</title>
<style>
*{margin: 0;padding: 0}
	.pic{
		width: 320px;
		height: 568px;
		background-color: #FFE872;
		color: white;
		font-size: 50px;
		text-align: center;
		line-height: 568px;
		font-family: cursive; 
	}
</style>
</head>
<body>
	<div class="pic"> Yellow </div>
</body>
</html>
```
这样，页面就可以满屏显示了。
## 不同手机下显示问题
通过测试我们发现，经过我们的设置黄色方块在不同的手机分辨率下保持了一样的大小，但是有一个小问题，那就是在不管页面内的元素的大小和多少的情况下，如果viewport的width设置一个固定的值，比如320，那么屏幕逻辑像素宽度小于320的手机总是可以左右滑动的，在一般情况下这些不符合我们的预期，所以我们需要给width设置一个特殊值
```html
<meta name="viewport" content="width=device-width,initial-scale=1,user-scalable=no">
```
这样，页面的默认宽度就确定为手机逻辑像素宽度了。
# 什么是rem
问题说到现在，似乎还没有解决，我们虽然搞定了手机的缩放问题，但是仍然无法做到让黄色方块在各个手机的分辨率下满屏显示，px在是个精确的度量单位，我们更需要一种按照手机分辨率，按比例缩放的单位！
而这就是rem存在的意义，rem与我们平常所见的em单位很像，实际上多出来的这个r就是response的意思
rem就是相对于页面根元素字体大小的度量单位，也就是说如果
```css
html {
	font-size:16px;
}
```
那么1rem = 16px，2rem = 2x16px，就是这么简单。
根据这个简单的特性，我们可以通过js先算出当前手机的宽度，然后通过一个事先约定好的比例公式算出的值给html的font-size赋值。这样rem的值就是一个响应式的值了
这样的代码有很多，在这里我给出一段参考代码
```javascript
;(function (doc, win) {
    var docEl = doc.documentElement,
            resizeEvt = 'orientationchange' in window ? 'orientationchange' : 'resize',
            recalc = function () {
                var clientWidth = docEl.clientWidth;
                if (!clientWidth) return;
                docEl.style.fontSize = 20 * (clientWidth / 375) + 'px';
            };

    if (!doc.addEventListener) return;
    win.addEventListener(resizeEvt, recalc, false);
    doc.addEventListener('DOMContentLoaded', recalc, false);

})(document, window);
```
这一段代码中是将iPhone6为底稿，所有的元素的像素宽度÷20，得到的值就是元素的rem值
# 彻底解决问题
根据前面的知识点，我们来终结之前的满屏问题，因为上述代码是已iPhone6(375x667)为底稿的，所以如果要满屏的话，黄色方块的宽应该设置为375÷20=18.75rem，高设置为667÷20=33.35rem。
所以最终代码是这样的
```html
<!doctype html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1,user-scalable=no">
<title>Test01</title>
<style>
*{margin: 0;padding: 0}
	.pic{
		width: 18.75rem;
		height: 33.35rem;
		background-color: #FFE872;
		color: white;
		font-size: 50px;
		text-align: center;
		line-height: 568px;
		font-family: cursive; 
	}
</style>
</head>
<body>
	<div class="pic"> Yellow </div>
<script type="text/javascript">
	;(function (doc, win) {
	    var docEl = doc.documentElement,
	            resizeEvt = 'orientationchange' in window ? 'orientationchange' : 'resize',
	            recalc = function () {
	                var clientWidth = docEl.clientWidth;
	                if (!clientWidth) return;
	                docEl.style.fontSize = 20 * (clientWidth / 375) + 'px';
	            };

	    if (!doc.addEventListener) return;
	    win.addEventListener(resizeEvt, recalc, false);
	    doc.addEventListener('DOMContentLoaded', recalc, false);

	})(document, window);
</script>
</body>
</html>
```
效果图如下
![iphone5](/images/mobile-adaptive/mobile-iPhone5.gif)