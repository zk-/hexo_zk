---
title: 微信小程序的登录与分享
date: 2019-04-11 13:41:08
categories: [博客]
tags: [微信小程序]
---
# 登录
从微信官方给的文档上给的图看，有点复杂，让人头昏眼花，实际上我们只要理清流程，整个过程并不难。
<!-- more -->
![微信小程序](/images/weixinxiaochenxu/api-login.jpg)

首先需要调用微信的登录接口，拿到code
```javascript
wx.login({
  success(res) {
    if (res.code) {
      // 发起网络请求
      console.log(res.code)
    } else {
      console.log('登录失败！' + res.errMsg)
    }
  }
})
```
然后发送code里的数据到对应的后台
```javascript
wx.request({
    url: 'loginurl', //你对应后台的登录地址
    data: {
        code: res.code
    },
    success:function(){
        callback();
    }
})
```
后台拿到数据与微信后台交互解密，返回解密数据
```json
{
    "openid" : "otsAA5dH45UrTJaBXi-tOemf1-dg",
    "user_3rdsession" : "a85d4520e4df47f139c7dd718c179df9",

    "status":"normal" //第三方后台自定义状态数据
}
```
小程序拿到数据，存入localstorage
```javascript
wx.setStorageSync(
  'userInfo',
  {
      data:'data'
  }
)
```
封装网络请求函数，在每次发送请求时，使用getStorageSync取出数据，带上数据发送出去，小程序设置用户在没有操作的情况下session过5分钟就会过期，所以需要处理好过期情况
```javascript
function ajax_curl(url, data, cb, posttype = 'get', func_fail){
  var userInfo = wx.getStorageSync('userInfo');
  data = Object.assign(data, userInfo); //合并对象
  wx.request({
    url: url,
    data: data,
    // method: method_type,  忽略细节
    // header: {
    //   'Content-Type': headerset
    // },
    success: function (res) {
      typeof cb == "function" && cb(res.data.data);
    },
    fail: function (res) {
      if (res.data.code == -999) { //检测下线，重新登录
        login(function(){
          data.user_3rdsession = wx.getStorageSync('userInfo').user_3rdsession
          ajax_curl(url, data, cb, posttype);
        });
      } else {
        typeof func_fail == "function" && func_fail(res);
          wx.showToast({
          title: res.message,
          icon: 'none',
          duration: 2000
        })
      }
    }
  })
}
```
# 分享
小程序的分享有两种形式，一个是通过按钮__button__转发，一个是通过右上角的菜单按钮转发。
按钮转发需要设置button组件中的open-type属性，此处需要页面设置Page.onShareAppMessage事件才能出发
```html
<button open-type="share">分享</button>
```
菜单转发就简单很多，只需要在当前页里设置好onShareAppMessage事件
```javascript
Page({
  onShareAppMessage(res) {
    if (res.from === 'button') {
      // 来自页面内转发按钮,如果是来自菜单则是menu
      console.log(res.target)
    }
    return {
      title: '自定义转发标题',
      path: '/page/user?id=123',
      imageUrl:'' //可选
    }
  }
})
```
## 在微信开发者编辑器中模拟打开分享小程序的场景
![微信小程序](/images/weixinxiaochenxu/bianyi.png)
![微信小程序](/images/weixinxiaochenxu/bianyi2.png)
选择好启动页面，为你分享的那个页面路径，设置好你的启动参数，就可以模拟分享小程序的场景
## 获取群内转发的一些信息shareTicket
首先需要使用微信的接口开放shareTicket的设置
```javascript
wx.showShareMenu({
  withShareTicket: true
})
```
在转发的过程中，其他用户打开转发的小程序时，可以通过在 App.onLaunch 或 App.onShow 获取到一个 shareTicket
```javascript
App({
  onLaunch: function (opt) {
    console.log(opt.shareTicket) //一个字符串
  }
})
```
注意，这里可以用我们刚刚学到的知识来模拟分享场景，不过有一个小小的区别，看下图
![微信小程序](/images/weixinxiaochenxu/ddd.png)
然后通过调用 wx.getShareInfo 接口传入此 shareTicket 可以获取到转发信息
```javascript
wx.getShareInfo({
    shareTicket: app.globalData.tik,
    success:function(res){
        console.log(res)
    }
})
```
此处获取到的数据，实际上是包含加密字符串的对象，我们还需要将数据发送到对应的第三方服务器中解密，才能拿到正常的数据
```json
{
  "nickName": "Band",
  "gender": 1,
  "language": "zh_CN",
  "city": "Guangzhou",
  "province": "Guangdong",
  "country": "CN",
  "avatarUrl": "http://wx.qlogo.cn/mmopen/vi_32/1vZvI39NWFQ9XM4LtQpFrQJ1xlgZxx3w7bQxKARol6503Iuswjjn6nIGBiaycAjAtpujxyzYsrztuuICqIM5ibXQ/0"
}
```