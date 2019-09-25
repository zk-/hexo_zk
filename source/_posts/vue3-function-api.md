---
title: 浅谈Vue3的新特性Function-api
date: 2019-09-25 13:40:18
categories: [博客]
tags: [Vue]
---
最近（实际上已经几个月了）vue社区关于vue3的新特性一事一直闹得沸沸扬扬的，我因为公司以及家庭原因已经好久没有更博了（咕咕咕），最近了解了一下相关的内容，现在简单的解释一下Vue3的这个新特性，以及为什么尤雨溪大佬要力排众议坚持这么做。
<!-- more -->
在这里先贴一下[官方对此的解释](https://github.com/vuejs/rfcs/blob/function-apis/active-rfcs/0000-function-api.md#type-issues-with-class-api)，英语好的同学可以直接看这个，解释的更详细。懒得看的同学继续听我说。
# 起因
React在近期做出了一个重大更新[React Hooks](https://reactjs.org/docs/hooks-intro.html)，在这个更新中React提出了一个重要的解决方案（或者说编码思维？），然后众所周知，Vue一直是一个能够吸取Angular和React等优秀框架优点，博众人之长的框架。  
所以尤雨溪大佬就再次也跟进吸取了这个特性。
## React Hooks提出了什么思想？
简单来说，就是因为React的运行机制（生命周期思想），改变了我们传统的编写组件逻辑的方式。  
想要实现一个效果，我们可能需要把一个有完整逻辑的操作拆分放到不同的生命周期函数中。
例如我们经常会在componentDidMount函数中监听事件，然后在componentWillUnmount函数中清楚监听。  
这样的写法，拆分了一个完整的组件逻辑，并把这些拆分掉的逻辑与其他逻辑放在了一起，对于代码的可读性上以及理解上变得更加难懂，组件之间的状态逻辑也难以复用。

基于此原因，在加上React官方对国际标准中class一直都没有没有稳定的语法提案的失望，所以打算采取渐进策略，开始关注并加入Function-api模式
# Function-api模式主要的变化是什么？
在vue3中，提供了一个setup函数，在这个函数中，你可以把所有的组件逻辑都写在这里，而不用想以前一样，需要把状态放在data函数，相关逻辑放在生命周期函数等等。
一下是官方的一个例子：
```js
function useMouse() {
  const x = value(0)
  const y = value(0)
  const update = e => {
    x.value = e.pageX
    y.value = e.pageY
  }
  onMounted(() => {
    window.addEventListener('mousemove', update)
  })
  onUnmounted(() => {
    window.removeEventListener('mousemove', update)
  })
  return { x, y }
}

// in consuming component
const Component = {
  setup() {
    const { x, y } = useMouse()
    const { z } = useOtherLogic()
    return { x, y, z }
  },
  template: `<div>{{ x }} {{ y }} {{ z }}</div>`
}
```
组件数据value(0)来存取，生命周期函数也直接在setup函数中执行了，对应vue2中的一些特性比如watch也提供了函数式的表达。这其中还有一些细节变化，我们不做详细讨论。

在这里可以做个简单的概括，vue2中我们的书写方式叫option-api，在vue3中，这些类似的属性配置都提供了函数式的书写方式，这就是Function-api。
# 这样不会让开发代码变得更加混乱了吗？
以下是官方对此的解释的大概翻译（英文水平有限，只做概括翻译）：  
> 我收到一些反馈，他们觉得新手会在这个模式下写出意大利面条式的代码，我认为这些担忧是毫无根据的，Function-api模式确实比以往的模式更加的灵活，但是Function-api模式可以把你的组件逻辑写在一个封密的函数中，传统的模式会把你的组件逻辑分到props 、data()、mounted等属性中，如果你把这个组件放到你的app中，他将会变成一个怪物，组件的逻辑像碎片一样散布在每个角落，而Function-api模式则把这些逻辑组织在setup函数中，避免了这种问题发生。

原文链接[在这](https://github.com/vuejs/rfcs/blob/function-apis/active-rfcs/0000-function-api.md#spaghetti-code-in-unexperienced-hands)，大概意思就是这样，虽然我觉得尤雨溪大佬并没有直面回答问题。。。
# 这个模式是不是意味着如果我要升级我的项目，就要改写代码？
不是，Function-api是向上兼容的，是一个可选选项，从vue2升级到vue3不需要改写任何代码。
# 那会一直保持这种兼容吗？
目前会（言下之意就是未来不会），至少在这篇[RFC文档](https://github.com/vuejs/rfcs/blob/function-apis/active-rfcs/0000-function-api.md#is-this-set-in-stone)中

# 结语
拥抱未来总归是一件好事，作为一个优秀的框架，必然要跟上最新的技术和思想（虽然他越来越像React了。。），各位用惯了vue2的老程序员也不必不适应，毕竟现在前端就是这样的，还是处于变动的时代。  
> 求求你，我实在学不动了.jpg  

哈哈