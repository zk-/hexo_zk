---
title: PWA初尝试
date: 2019-11-21 14:02:19
categories: [博客]
tags: [PWA]
---
PWA，是提升 Web App 的体验的一种新方法，能给用户原生应用的体验。
<!-- more -->
具体的介绍，以及详细的教程，请看[这里](https://lavas.baidu.com/pwa/README)，我在这里就不在重复造轮子了  
这里我就简单的介绍下我在本博客网站下搭建PWA的具体步骤，让同学们按照我最简单的教程来个快速启动
# 注意事项
想要成功搭建并运行一个完整的PWA应用，你需要以下几个条件：
- 站点部署 manifest.json，该文件需配置如下属性：
1. short_name （用于主屏幕显示）
2. name （用于安装横幅显示）
3. icons （其中必须包含一个 mime 类型为 image/png 的图标声明）
4. start_url （应用启动地址）
5. display （必须为 standalone 或 fullscreen）
- 站点注册 Service Worker。
- 站点支持 HTTPS 访问（或者在本地环境127.0.0.1或localhost下）。
- 站点在同一浏览器中被访问至少两次，两次访问间隔至少为 5 分钟。
# ServiceWorker.js安装
首先在主页html上注册`ServiceWorker.js`
```javascript
if ('serviceWorker' in navigator) {
    window.addEventListener('load', function () {
        navigator.serviceWorker.register('/sw.js', {scope: '/'})
            .then(function (registration) {

                // 注册成功
                console.log('ServiceWorker registration successful with scope: ', registration.scope);
            })
            .catch(function (err) {

                // 注册失败:(
                console.log('ServiceWorker registration failed: ', err);
            });
    });
}
```
在`sw.js`中的内容是这样的
```javascript
this.addEventListener('fetch', function (event) {
    event.respondWith(
        caches.match(event.request).then(function (response) {
            // 来来来，代理可以搞一些代理的事情

            // 如果 Service Worker 有自己的返回，就直接返回，减少一次 http 请求
            if (response) {
                return response;
            }

            // 如果 service worker 没有返回，那就得直接请求真实远程服务
            var request = event.request.clone(); // 把原始请求拷过来
            return fetch(request).then(function (httpRes) {

                // http请求的返回已被抓到，可以处置了。

                // 请求失败了，直接返回失败的结果就好了。。
                if (!httpRes || httpRes.status !== 200) {
                    return httpRes;
                }

                // 请求成功的话，将请求缓存起来。
                var responseClone = httpRes.clone();
                caches.open('my-test-cache-v1').then(function (cache) {
                    cache.put(event.request, responseClone);
                });

                return httpRes;
            });
        })
    );
});
```
# 设置manifest.json文件
首先在主页html文件下引入`manifest.json`文件
```html
<link rel="manifest" href="/manifest.json">
```
`manifest.json`文件中内容的填写
```json
{
    "short_name": "Jackfred",
    "name": "Jackfred的博客",
    "icons": [
        {
            "src": "/favicon96.png",
            "type": "image/png",
            "sizes": "96x96"
        },
        {
            "src": "/favicon144.png",
            "type": "image/png",
            "sizes": "144x144"
        },
        {
            "src": "/favicon188.png",
            "type": "image/png",
            "sizes": "188x188"
        }
    ],
    "display": "minimal-ui",
    "theme_color": "black",
    "start_url": "index.html"
}
```
到这里，PWA的部署就结束了，为了应对不同分辨率的手机，我们可能需要不同尺寸的图标，在我部署测试的时候发现，谷歌浏览器要求
至少需要一个144x144尺寸的图标，请各位同学注意。
# 结果展示
请大家用手机扫下面的二维码
![qr](/images/pwa/url.gif)
或者直接点击这个链接[https://www.jackfred.cn/](https://www.jackfred.cn/)  
因为我站点证书是直接用的github的证书，所以，部分手机浏览器会显示证书不安全或者不是私密链接等内容，请忽略ヾ(。￣□￣)ﾂ゜゜゜  
根据PWA的设计原则，我们可以自己主动点击浏览器菜单页，会发现多了一个添加到主屏幕的选项，也可以分2次访问网站（间隔要5分钟以上），手机屏幕下方就会自动弹出提示安装站点的横幅，点击即可添加
![result](/images/pwa/t.png)