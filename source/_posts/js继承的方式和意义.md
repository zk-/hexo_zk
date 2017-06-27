---
title: js继承的方式和意义
date: 2016-10-26 19:01:29
categories: [博客]
tags: [js,继承,面向对象]
---
在写这篇博客的时候，我就做好了撕逼的准备
<!-- more -->
js是一个无类语言，在日常编码中，我们可能需要js来创建一些类似面向对象里面“类”的东西，来实现代码复用。
js是一个基于对象的语言，没有类的概念，但是我们可以通过一些办法来模拟实现类的功能。
在写这篇博客的时候，我先在百度上搜了一下，网上说js的继承有五种办法
> 1.使用对象冒充实现继承
> 2.采用call方法改变函数上下文实现继承
> 3.采用Apply方法改变函数上下文实现继承
> 4.采用原型链的方式实现继承
> 5.采用混合模式实现继承

具体的文章大家可以自己百度一下，这样的文章网上很多，而且长得都一样。
但是我对此有一点不同的看法，js模拟实现类功能从实现的意义上来说最多只有3种，即
1.原型链继承
2.构造器继承
3.原型链和构造器混用继承

让我们一点点深入，来慢慢了解，为什么我说只有三种方法
# 原型链继承
原型链的概念其实不难理解，就是一种类似于职责链一样模式的模式，什么是职责链呢？
举个例子，我们去银行取钱，如果我们只是取几百块前，最快最好的办法是直接去ATM机取钱
如果我们要取几十万，就必须要去柜台，让银行的工作人员帮你取钱
如果我们要是取几个亿 ==！ 银行人员一定非常的紧张，告诉你过几天再来，柜台上没有那么多钱，然后再把事情报告给当地行长，等到前到位的时候通知取钱
咳咳，如果我们想和银行谈一个价值几百个亿的合作♂ 这件事都不是ATM机、银行工作人员、当地行长能接手的事，他们就需要把这件事上报到总部，让总部的人过来和我们来谈合作的事
在这个实例中，我们能看到的就是职责链模式，就是
消费者产生一个需求的时候，生产者接手这个需求，如果不能处理它，就往上传递，直到有一个生产者能够处理它

原型链就是一样的结构，js内置了许多对象，所有的对象，除了字符串、数字、布尔值、null和undefined以外，他们的原型链的终点都是Object
> 这句话不是很准确，字符串、数字和布尔值是一种不可变的对象，但是最终点也是Object
> Object的原型链是null

![原型链](/images/yuanxinglian/yuanxinglian1.png)
原型链呢就是一个对象，你调用他的方法，比如对象A，你调用A.sayName()，如果A有sayName这个方法，则直接调用，如果没有，则通过A是原型链向上查找，如果原型链上有这个方法，则调用，没有就继续查找，如果找不到，则会返回一个Uncaught TypeError错误或者undefined。
如果对象和原型链上都有这个方法，则现有的方法会覆盖原型链上的方法。
举个例子
```javascript
function Parent() {}
Parent.prototype.sayHello = function() {
	console.log('hello parent')
}

function Child() {}
Child.prototype = new Parent;
var child = new Child;
child.sayHello()	//输出hello parent
```
我们看到child调用sayHello成功了，调用的是Parent的方法，我们再看一下覆盖的情况
```javascript
function Parent() {}
Parent.prototype.sayHello = function() {
	console.log('hello parent')
}

function Child() {}
Child.prototype = new Parent;
Child.prototype.sayHello = function() {
	console.log('hello child')
};
var child = new Child;
child.sayHello()	//输出hello child
child.constructor.prototype.sayHello()	//输出hello parent
```
我们可以看到child的sayHello覆盖了parent的方法，但是我们可以通过访问constructor看到实际上这个方法还存在的。
# 构造器继承
js函数可以被当作构造器使用，通过返回的对象完成构造
如果没有指定返回一个对象，默认返回的是this
举个例子
```javascript
function Parent() {
	this.name = 'parent'
}
var parent = new Parent
console.log(parent.name)	//输出parent

function AnotherParent() {
	this.name = 'parent'
	var obj = {name:'hahaha'};
	return obj;
}
var anotherparent = new AnotherParent;
console.log(anotherparent.name) 	//输出hahaha
```
而所谓构造器继承，就是把需要继承的对象在构造过程中，调用被继承的对象的构造方法来处理这个继承对象需要构造的目标
让我们来实现一个构造器继承
```javascript
function Parent() {
	this.name = 'parent'
}
function Child() {
	var parent = Parent.bind(this); 	//将执行环境绑定到Child来
	parent();
	this.age = 10;
}
var child = new Child;
console.log(child.name) 	//输出parent
console.log(child.age) 		//输出10
```
是不是很简单？其实这一段的代码和下面这段代码是一样的
```javascript
function Child() {
	this.name = 'parent';
	this.age = 10;
}
var child = new Child;
console.log(child.name)
console.log(child.age)
```
这就是构造器继承，实际上这个方法和上面提到的网上的方法的123方法是同一种，换汤不换药，原理都是通过构造器实现继承
可以看看网上给的例子
```javascript
//1.使用对象冒充实现继承
function Parent(firstname)  
{  
    this.fname=firstname;  
    this.age=40;  
    this.sayAge=function()  
    {  
        console.log(this.age);  
    }  
}  
function Child(firstname)  
{  
    this.parent=Parent;  
    this.parent(firstname);  //实际上这一段的意义就是将执行环境上下文改变了,和上面的例子一样
    delete this.parent;  
    this.saySomeThing=function()  
    {  
        console.log(this.fname);  
        this.sayAge();  
    }  
}  
var mychild=new Child("李");  
mychild.saySomeThing();  

// 2.采用call方法改变函数上下文实现继承
function Parent(firstname)  
{  
    this.fname=firstname;  
    this.age=40;  
    this.sayAge=function()  
    {  
        console.log(this.age);  
    }  
}  
function Child(firstname)  
{  
  
    this.saySomeThing=function()  
    {  
        console.log(this.fname);  
        this.sayAge();  
    }  
   this.getName=function()  
   {  
       return firstname;  
   }  
  
}  
var child=new Child("张");  
Parent.call(child,child.getName());  // call只是借用罢了,作用的对象还是child,原理还是构造器继承
child.saySomeThing(); 

// 3.采用Apply方法改变函数上下文实现继承 
function Parent(firstname)  
{  
    this.fname=firstname;  
    this.age=40;  
    this.sayAge=function()  
    {  
        console.log(this.age);  
    }  
}  
function Child(firstname)  
{  
  
    this.saySomeThing=function()  
    {  
        console.log(this.fname);  
        this.sayAge();  
    }  
    this.getName=function()  
    {  
        return firstname;  
    }  
  
}  
var child=new Child("张");  
Parent.apply(child,[child.getName()]);  //???换个函数就能算一种了???
child.saySomeThing();  
```
我们可以看到网上说的这三种其实是一种，都是构造器继承，只是表现的手法不一样，不能算三种
（如果有读者对我的观点有什么不同的意见，欢迎在下面评论提出来）
# 关于混合继承的吐槽
如果按照我刚才的逻辑，混合继承应该也不算一种继承方式，这点我承认，但是我还是把它拿出来了算作一种，原因很简单，就是因为它的使用场景和其他两种都不一样，而上面那三种的基本就是一样的，至于都有哪些不一样，请往下看。
# 意义以及优缺点

先说结论，原型链继承中的原型对象是共享的，构造器构造出来的对象是独立的

## 原型链继承的意义以及优缺点
我们来看这样的一段代码
```javascript
function Parent() {}
Parent.prototype = {
	testArray:[1,2,3]
};
function Child() {}
Child.prototype = new Parent;
var child1 = new Child;
var child2 = new Child;

child1.testArray.push(4)

console.log(child1.testArray) 	//输出[1, 2, 3, 4]
console.log(child2.testArray) 	//输出[1, 2, 3, 4]
```
我们可以看到child1对象的操作影响到了child2对象，原因很简单，他们用的原型对象是相同的共享的，意思就是原型对象指向的地址是一样的，任何对Parent原型的更改，都会反应到继承他的子对象中
所以在原型中是不适合写属性进去的，除非你有意把这样的功能设计进去。
同时，如果判断一下
![原型链](/images/yuanxinglian/yuanxinglian2.png)
结果发现child1属于Parent和Child，如果在复杂场景中，我们还需要找有没有别的对象继承了Child，这样才能确定一个对象是属于哪个构造器的，无疑非常的麻烦，这也是原型链的缺点

## 构造器继承的意义以及优缺点
继续看一个例子
```javascript
function Parent() {
	this.testArray = [1,2,3]
}
function Child() {
	var parent = Parent.bind(this)
}
var child1 = new Child;
var child2 = new Child;

child1.testArray.push(4)

console.log(child1.testArray) 	//输出[1, 2, 3]
console.log(child2.testArray) 	//输出[1, 2, 3, 4]
```
和原型链不同的是，构造器的继承都是独立的互不影响
查看一下类型
![原型链](/images/yuanxinglian/yuanxinglian3.png)
很准确的判断出来了类型

## 混合继承的意义以及优缺点
混合继承适用与复杂的应用场景，当需要一些对象共享一些基础的静态方法，原型链有效的减少了重复的代码，而且还能实现多态，如果子对象需要生成独立互不影响的内部对象，构造器则能够很好的维护好子对象中的内部对象而不影响其它对象
原型链因为他的特殊性，还可以用原来进行共享对象的数据交换
好处有很多，但是在实际生产环境中，为了便于代码的可读性以及可维护性，如果不是必须的话，最好使用一种来实现