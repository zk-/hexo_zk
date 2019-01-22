---
title: flex布局
date: 2019-01-22 09:07:40
categories: [博客]
tags: [移动端]
---
Flex 是 Flexible Box 的缩写，意为"弹性布局"，用来为盒状模型提供最大的灵活性。采用 Flex 布局的元素，称为 Flex 容器（flex container），简称"容器"。它的所有子元素自动成为容器成员，称为 Flex 项目（flex item），简称"项目"。
<!-- more -->
![flex](/images/mobile-adaptive/bg2015071004.png)
容器默认存在两根轴：水平的主轴（main axis）和垂直的交叉轴（cross axis）。
# 容器的属性
## flex-direction属性
flex-direction属性决定主轴的方向（即项目的排列方向）。
```css
.box {
    flex-direction: row | row-reverse | column | column-reverse;
}
```
![flex](/images/mobile-adaptive/bg2015071005.png)
## flex-wrap属性
默认情况下，项目都排在一条线（又称"轴线"）上。flex-wrap属性定义，如果一条轴线排不下，如何换行。
```css
.box{
    flex-wrap: nowrap | wrap | wrap-reverse;
}
```
![flex](/images/mobile-adaptive/bg2015071006.png)
## flex-flow属性
flex-flow属性是flex-direction属性和flex-wrap属性的简写形式，默认值为row nowrap。
```css
.box {
    flex-flow: <flex-direction> || <flex-wrap>;
}
```
## justify-content属性
justify-content属性定义了项目在主轴上的对齐方式。
```css
.box {
    justify-content: flex-start | flex-end | center | space-between | space-around;
}
```
![flex](/images/mobile-adaptive/bg2015071010.png)
## align-items属性
align-items属性定义项目在交叉轴上如何对齐。
```css
.box {
    align-items: flex-start | flex-end | center | baseline | stretch;
}
```
![flex](/images/mobile-adaptive/bg2015071011.png)
## align-content属性
align-content属性定义了多根轴线的对齐方式。如果项目只有一根轴线，该属性不起作用。
```css
.box {
    align-content: flex-start | flex-end | center | space-between | space-around | stretch;
}
```
![flex](/images/mobile-adaptive/bg2015071012.png)
# 容器成员的属性
## order属性
order属性定义项目的排列顺序。数值越小，排列越靠前，默认为0。
```css
.item {
    order: <integer>;
}
```
![flex](/images/mobile-adaptive/bg2015071013.png)
## flex-grow属性
flex-grow属性定义项目的放大比例，默认为0，即如果存在剩余空间，也不放大。如果所有项目的flex-grow属性都为1，则它们将等分剩余空间（如果有的话）。如果一个项目的flex-grow属性为2，其他项目都为1，则前者占据的剩余空间将比其他项多一倍。
![flex](/images/mobile-adaptive/bg2015071014.png)
## flex-shrink属性
flex-shrink属性定义了项目的缩小比例，默认为1，即如果空间不足，该项目将缩小。如果所有项目的flex-shrink属性都为1，当空间不足时，都将等比例缩小。如果一个项目的flex-shrink属性为0，其他项目都为1，则空间不足时，前者不缩小。

负值对该属性无效。
![flex](/images/mobile-adaptive/bg2015071015.jpg)
## flex-basis属性
flex-basis属性定义了在分配多余空间之前，项目占据的主轴空间（main size）。浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为auto，即项目的本来大小。它可以设为跟width或height属性一样的值（比如350px），则项目将占据固定空间。
```css
.item {
    flex-basis: <length> | auto; /* default auto */
}
```
## flex属性
flex属性是flex-grow, flex-shrink 和 flex-basis的简写，默认值为0 1 auto。后两个属性可选。该属性有两个快捷值：auto (1 1 auto) 和 none (0 0 auto)。
```css
.item {
    flex: none | [ <'flex-grow'> <'flex-shrink'>? || <'flex-basis'> ]
}
```
## align-self属性
align-self属性允许单个项目有与其他项目不一样的对齐方式，可覆盖align-items属性。默认值为auto，表示继承父元素的align-items属性，如果没有父元素，则等同于stretch。该属性可能取6个值，除了auto，其他都与align-items属性完全一致。
```css
.item {
    align-self: auto | flex-start | flex-end | center | baseline | stretch;
}
```
![flex](/images/mobile-adaptive/bg2015071016.png)
# 最后的例子和说明
flex-basis 规定的是子元素的基准值。所以是否溢出的计算与此属性息息相关。flex-basis 规定的范围取决于 box-sizing。这里主要讨论以下 flex-basis 的取值情况：
> auto：首先检索该子元素的主尺寸，如果主尺寸不为 auto，则使用值采取主尺寸之值；如果也是 auto，则使用值为 content。
> content：指根据该子元素的内容自动布局。有的用户代理没有实现取 content 值，等效的替代方案是 flex-basis 和主尺寸都取 auto。
> 百分比：根据其包含块（即伸缩父容器）的主尺寸计算。如果包含块的主尺寸未定义（即父容器的主尺寸取决于子元素），则计算结果和设为 auto 一样。
```html
<div class="parent">
    <div class="item-1"></div>
    <div class="item-2"></div>
    <div class="item-3"></div>
</div>
 
<style type="text/css">
    .parent {
        display: flex;
        width: 600px;
    }
    .parent > div {
        height: 100px;
    }
    .item-1 {
        width: 140px;
        flex: 2 1 0%;
        background: blue;
    }
    .item-2 {
        width: 100px;
        flex: 2 1 auto;
        background: darkblue;
    }
    .item-3 {
        flex: 1 1 200px;
        background: lightblue;
    }
</style>
```
主轴上父容器总尺寸为 600px
子元素的总基准值是：0% + auto + 200px = 300px，其中
- 0% 即 0 宽度
- auto 对应取主尺寸即 100px

故剩余空间为 600px - 300px = 300px
伸缩放大系数之和为： 2 + 2 + 1 = 5
剩余空间分配如下：
- item-1 和 item-2 各分配 2/5，各得 120px
- item-3 分配 1/5，得 60px

各项目最终宽度为：
- item-1 = 0% + 120px = 120px
- item-2 = auto + 120px = 220px
- item-3 = 200px + 60px = 260px

当 item-1 基准值取 0% 的时候，是把该项目视为零尺寸的，故即便声明其尺寸为 140px，也并没有什么用，形同虚设
而 item-2 基准值取 auto 的时候，根据规则基准值使用值是主尺寸值即 100px，故这 100px 不会纳入剩余空间