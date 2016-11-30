---
title: ES6新特性初探
date: 2016-11-30 10:34:35
categories: [博客]
tags: [ES6]
---
最近学的东西有点多，主要是最近从追求深度的一个坑里爬了出来，因为一无所获==！
这样下去就是浪费时间，所以把一些计划上学的东西学一遍，开始技能树乱加点模式。。
<!-- more -->
这次ES6新增了几个新特性，解决了平常使用js时需要处理一些麻烦的东西时的不便，增加了声明函数时使用箭头函数这种更__清晰__的语法模式（我是没觉得哪里好了，感觉更乱了==），还增加了一些牛逼的自定义迭代器这样的功能（嗯，越来越像python了），现在我就列举说明一下其中几个新特性及其用法。
# 开始前的准备
因为es6的语法每个浏览器支持的程度不一样，所以我们使用一种安全的方法来调试我们的脚本
首先安装es6转码器babel提供的命令行工具
```
npm install --global babel-cli
```
因为这款工具对于es6的语法支持的不是很全，我们需要安装官方提供的转码规则集
```
npm install --save-dev babel-preset-es2015
```
然后在我们的项目下新建一个.babelrc文件并写入
```
{
    "presets": [
      "es2015"
    ],
    "plugins": []
  }
```
这一步是为了让babel在执行的时候采用上面安装的规则集。
这些都搞完我们就可以开始学习了，在学习测试的时候可以用三种命令行方式验证我们的脚本
一个是 babel yourprogarm.js
这个可以直接在命令行输出翻译出来的脚本
一个是 babel-node yourprogarm.js (用node直接输出也可以)
这个可以直接运行你的脚本，输出结果
一个是 babel yourprogarm.js -o compiled.js
将你的脚本转成es5语法并保存到指定文件里面
大家各取所需
# 块作用声明let以及常量声明const
废话不多说，直接先看代码
```
if (true) {
	var a = 4
}
console.log(a)  //4
```
我们都知道在这个例子里面，a虽然在if中声明了，但是if没有块级作用域，所以在外部也能正常访问a，这给初学者或多或少带来了理解困难，因为这是js语言的__缺陷__。在es6中，我们有了let这个块级声明，再也不用担心这类问题了
```
if (true) {
	let a = 4
}
console.log(a)  //undefined
```
另外我们在自己的程序中有时候希望声明一些常量，又不希望被别人更改，这时候就可以使用es6提供的const来声明变量
```
const a = 4;
a = 5;  //error
```
这些用法都非常简单易懂，但是要注意的是babel对这些特性的兼容是怎样实现的
在对于支持这些语法特性的浏览器来说，这些语法非常的安全，但是如果对于不支持这些语法特性的浏览器，babel需要翻译成浏览器支持的语法才能正常表现，现在我们就用babel命令来输出翻译后的代码是什么样的
拿第二段的代码举例，翻译后是这样的
![es6](/images/es6/es6.png)
翻译的结果是babel在对应的变量前加了下划线，所以我们要注意的一点是在使用babel和es6的时候，不要出现相似变量名之间只差一个下划线的情况，这样就会出现难以调试和发现的bug
# 解构
英文名DESTRUCTURE（扔到有道翻译里面意思是“变性” ==！）
实际意思是一种新的赋值模式，叫解构
解构赋值提供了一种位赋值
```
var [a, b] = [1, 2]
console.log(a, b)  //输出结果1 2
```
大家看到了，就是对应位置的复制，这样以后如果需要交换变量值的时候就很方便，只需要一行代码就能实现
```
[a, b] = [b, a]
```
解构赋值还提供了一种提取属性的赋值方法
```
var pt = {x: 123, y: 444};
var {x, y} = pt;
console.log(x, y); // 123 444
```
结果很明白，有的同学可能有疑问，如果我不想用xy这样和对象名属性一样的变量名怎么办，简单，在后面加上冒号就可以了
```
var pt = {x: 123, y: 444};
var {x:myvalue, y} = pt;
console.log(y); // 444
console.log(x); // undefined
console.log(myvalue); // 123
```
# 字面量属性可计算
这个特性很好理解，平常我们在一个对象中，读取或者写入一个动态生成的属性时，是这样做的
```
var b = 'example';
var a = {};
a[b + 'A'] = 4
console.log(a['exampleA'])  //4
```
在表达式中计算，现在es6加入的特性就是，在对象声明字面量的时候，就可以使用计算属性
```
var a = {
	['example' + 'A']:6
}
console.log(a.exampleA)  //6
```
# 箭头函数 =>
说实话这个玩意我开始用的时候真不习惯，感觉上可读性也没有多少提高，但是推出这个语法一定有他的用意，先说用法
一般函数声明是这样的
```
function abc(){
	// your code here
}
```
而es6的语法是这样的
```
var abc = () => {
	//your code here
}
```
这个__()__就是声明参数的地方，剩下的就是函数解构体，其实箭头函数就是声明了一个匿名函数并复制给了abc，所以匿名函数的写法就是这样
```
setTimeout(()=>{console.log('123')},1000)
```
就是样子变了一下，如果说有什么要值得注意的话，就是箭头函数创建实名函数的时候，它是一个表达式，所以没有函数声明提前的说法。
```
console.log(a) //undefined
var a = () => {
	// your code here
}
```
# REST AND SPREAD(延展运算符)
上面说过es6把一些常见的其它语言的特性拿到了es6中来，这就是其中的一个
在函数中，我们的参数可以通过arguments来引用进来，如果一个函数要求的参数是2个，如果调用的时候传入了1个是不会报错的，只不过第二个参数默认会赋值undefined，如果传入了3个参数，也不会报错，多出来的那一个可以通过arguments[2]访问到。
可能是es6的专家们希望js能表现的和正常的语言一样吧，在es6中，参数可以这样写
```
var abc = (...args) => {
	console.log(args)
}
abc(1, 2, 3, 4)  //1 2 3 4
```
前面的三个点表示任意多的参数，我们还可以给参数赋默认值
```
var abc = (a = 6) => {
	console.log(a)
}
abc()  //6
abc(4)  //4
```
利用这些特性我们可以很方便的取到多余的参数
```
var abc = (a, b, ...args) => {
	console.log(a + b)
	console.log(args)
}
abc(1, 2)  //3 []
abc(1, 2, 3, 4)  //3 [3, 4]
```
# class类
__ladys and 乡亲们，你们盼望已久的面向对象终于来啦！__
__ladys and 乡亲们，你们盼望已久的面向对象终于来啦！__
__ladys and 乡亲们，你们盼望已久的面向对象终于来啦！__
js面向对象编程说了辣么多年，网上的相关文章也是辣么多，现在终于出来了，刚刚看到的时候说实话我是很激动的，但是研究了一下，发现因为js的历史原因，实际上，es6对于面向对象的特性实现只是我们在写代码的时候感觉像是那么回事，实际上还是构造器以及原型链，继承方式也是这两种
![突然消沉](/images/emotions/trxc.jpg)
不管了，最起码写写代码的体验上提升了很多。
> 有对js对象继承不太理解的同学，可以看看这篇我的文章__[js继承的方式和意义](https://zk-.github.io/2016/10/26/js继承的方式和意义/)__
上代码
```
class Parent{
	constructor(name){	//这是构造器
		this.name = name;
	}
	sayHello(){		//这是原型方法
		console.log('my name is ' + this.name)
	}
}
var parent = new Parent('parent')
console.log(parent.name)  //parent
parent.sayHello()  //my name is parent
```
继承的写法也很容易，用关键词extends，父类用super表示
```
class Parent{
	constructor(name){	//这是构造器
		this.name = name;
	}
	sayHello(){		//这是原型方法
		return 'my name is ' + this.name
	}
}

class Child extends Parent{
	constructor(name, sex){
		super(name);  //构造器继承
		this.sex = sex;
	}
	sayHello(){
		console.log('i am a child,' + super.sayHello())  //原型链继承
	}
	saySex(){
		console.log(this.sex)
	}
}

var child = new Child('baby','male')
console.log(child.name)  //baby
console.log(child.sex)  //male
child.sayHello()  //i am a child,my name is baby
child.saySex()  //male
```
# 模块module
模块加载在es6中得到了原生支持
```
// Message.js
export const message = 'Hello Babel';

// other.js
import {message} from './Message';
console.log(message); // Hello Babel
```
导出可以实名导出也可以默认导出
```
// Message.js
const greeting = 'Hello';
const name = 'Babel';
const version = 'v5.0';
export const obj = {
  greeting: greeting,
  name: name,
  version: version
};

// Message.js
const greeting = 'Hello';
const name = 'Babel';
const version = 'v5.0';
export default {
  greeting: greeting,
  name: name,
  version: version
};

// other.js
import {greeting, name, version} from './Message';
```
# 迭代器
迭代器是一个大概念，等会写。。