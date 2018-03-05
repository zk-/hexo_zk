---
title: jQuery的offset的值不准的bug
date: 2018-03-05 11:25:47
tags: bug,jquery
---
记录一下这几天遇到的一个诡异的bug
<!-- more -->
最近项目需求，要做一个气泡卡片的功能，因为不想引入新的库发生未知bug，于是自己手写了一个，定位通过jquery的offset方法来获取

项目的布局是通过iframe嵌套方式来布局的，结果发现在部分页面下，会发生气泡定位不准的情况
![popover](/images/jqueryOffsetBug/popver.png)

经过多次debugger后确定不是我的代码问题，于是查看jquery的源码
```javascript
if(arguments.length) return e === t ? this : this.each(function(t) {
        v.offset.setOffset(this, e, t)
    });
    var n, r, i, s, o, u, a, f = {
            top: 0,
            left: 0
        },
        l = this[0],
        c = l && l.ownerDocument;
    if(!c) return;
        return(r = c.body) === l ? v.offset.bodyOffset(l) : (n = c.documentElement, v.contains(n, l) ? (typeof l.getBoundingClientRect != "undefined" && (f = l.getBoundingClientRect()), i = tr(c), s = n.clientTop || r.clientTop || 0, o = n.clientLeft || r.clientLeft || 0, u = i.pageYOffset || n.scrollTop, a = i.pageXOffset || n.scrollLeft, {
            top: f.top + u - s,
            left: f.left + a - o
        }) : f)
```
jquery中的offset的top是通过元素的getBoundingClientRect().top + window.scrollY - clientTop计算出来的。

这本来是没有什么问题的，但是上文提到过项目的整个页面是通过iframe来布局的，通过我不断的对比发现，window.scrollY在滚动的时候的值有时候会不正确，即在页面滚动的时候window.scrollY会少掉一个document.body.scrollTop的值。

我们只要在代码中加上这个值就可以修复这个bug 