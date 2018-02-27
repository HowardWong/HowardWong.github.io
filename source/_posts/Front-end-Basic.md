title: Front-end Basic
author: Howard Wong
tags: []
categories: []
date: 2017-02-27 20:35:00
---
# html


## meta

```html
<!-- 设置缩放 -->

<meta name="viewport" content="width=device-width, initial-scale=1, user-scalable=no, minimal-ui" />

<!-- 可隐藏地址栏，仅针对IOS的Safari（注：IOS7.0版本以后，safari上已看不到效果） -->

<meta name="apple-mobile-web-app-capable" content="yes" />

<!-- 仅针对IOS的Safari顶端状态条的样式（可选default/black/black-translucent ） -->

<meta name="apple-mobile-web-app-status-bar-style" content="black" />

<!-- IOS中禁用将数字识别为电话号码/忽略Android平台中对邮箱地址的识别 -->

<meta name="format-detection"content="telephone=no, email=no" />


其他meta标签

<!-- 启用360浏览器的极速模式(webkit) -->

<meta name="renderer" content="webkit">

<!-- 避免IE使用兼容模式 -->

<meta http-equiv="X-UA-Compatible" content="IE=edge">

<!-- 针对手持设备优化，主要是针对一些老的不识别viewport的浏览器，比如黑莓 -->

<meta name="HandheldFriendly" content="true">

<!-- 微软的老式浏览器 -->

<meta name="MobileOptimized" content="320">

<!-- uc强制竖屏 -->

<meta name="screen-orientation" content="portrait">

<!-- QQ强制竖屏 -->

<meta name="x5-orientation" content="portrait">

<!-- UC强制全屏 -->

<meta name="full-screen" content="yes">

<!-- QQ强制全屏 -->

<meta name="x5-fullscreen" content="true">

<!-- UC应用模式 -->

<meta name="browsermode" content="application">

<!-- QQ应用模式 -->

<meta name="x5-page-mode" content="app">

<!-- windows phone 点击无高光 -->

<meta name="msapplication-tap-highlight" content="no">
```

## viewport

```
// template
<meta name="viewport" content="width=device-width,initial-scale=1.0,minimum-scale=1.0,maximum-scale=1.0,user-scalable=no" />
```

## 1px in different device
```
<meta name="viewport" content="width=device-width, initial-scale=0.5" />
```

## Page Optimization

- prefer `css3 animation` rather than `javascript animation`
- compose big images, use `base64` to replace small img
- Gzip response
- use CDN, Cache
- reduce direct modification of DOM
- using non-block javascript

-------


# css


## 消除transition闪屏

```css
.css {
    transform-style: preserve-3d;
    backface-visibility: hidden;
}
```

## 启用硬件加速

```css
.css {
    transform: translate3d(0,0,0);
}
```

## css text overflow

```css
.overflow {
	overflow: hidden;
    text-overflow:ellipsis;
	white-space: nowrap;
}
```

## css textarea overflow

```css
.overflow {
	display: -webkit-box;
	-webkit-box-orient: vertical;
	-webkit-line-clamp: 3;
	overflow: hidden;
}
```

## vertical align

- table

```css
.outter {
	display: table-cell;
    vertical-align: middle;
}
.align {
	margin: 0 auto;
}
```

- translate

```css
.align {
	position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
}
```

- flex

```css
.outer {
	display: flex;
    align-center: center;
    justify-content: center;
}
```



-----


# ECMAScript

## cross-domain

- Nginx
- Server rewrite safe domain
- Jsonp

```javascript
function cb(res) {
}

const script = document.createElement('script');
script.type = 'text/javascript';
script.src = '...';

document.body.append(script);

```

## JS 判断设备来源


```javascript
function deviceType(){
    var ua = navigator.userAgent;
    var agent = ["Android", "iPhone", "SymbianOS", "Windows Phone", "iPad", "iPod"]; 
    var len = agent.length
    var i = 0
    for(; i<len; i++){
        if(ua.indexOf(agent[i])>0){         
            break;
        }
    }
    return agent[i]
}

deviceType();

window.addEventListener('resize', function(){
    deviceType();
})

function isWeixin(){
    var ua = navigator.userAgent.toLowerCase();  if(ua.match(/MicroMessenger/i)=='micromessenger'){
        return true;
    }else{
        return false;
    }
}
```
