---
title: 1px细线终极解决方案
date: 2018-08-24 23:54:19
categories: 移动端开发
tags: 
 - h5
 - 移动端
 - 兼容
---



>问：为什么要实现1px细线？
>
>答：颜值即正义

&emsp;&emsp;现有的解决方案有很多，各有优劣，很多文章也只是简单罗列了各种实现方式，告诉你说在合适的场景使用合适的方法，或者说结合使用。但是具体怎么做，并没有给出详细的方案。本文结合文章[《再谈Retina下1px的解决方案》](https://www.w3cplus.com/css/fix-1px-for-retina.html)中的讨论，给出了兼容ios和安卓的方法。

<!--more-->

由于ios版本的推进，支持到ios8已经是可以接受的事了，因此，可以充分利用ios8及以上支持0.5px的特性。

# 判断0.5px支持

首先判断当前设备对于0.5px的支持，有两种方案：

一是判断设备类型，对于ios8以及以上，测认为支持0.5px。

```javascript
if (/iP(hone|od|ad)/.test(navigator.userAgent)) {
    const v = (navigator.appVersion).match(/OS (\d+)_(\d+)_?(\d+)?/);
    const version = parseInt(v[1], 10);

    if (version >= 8) {
        document.documentElement.classList.add('hairlines');
    }
}
```

还有另外一种方案，经测试，在某些安卓机型上检测有误，可能是计算精度导致。

```javascript
if (window.devicePixelRatio && devicePixelRatio >= 2) { 
	var testElem = document.createElement('div'); 
	testElem.style.border = '.5px solid transparent';
	document.body.appendChild(testElem);
	if (testElem.offsetHeight == 1){
        document.querySelector('html').classList.add('hairlines'); 
    } 
    document.body.removeChild(testElem);
}
```

为了确保准确性，使用方案1。

# postcss-write-svg 

[postcss-write-svg](https://github.com/jonathantneal/postcss-write-svg)插件支持通过css的方式绘制svg，可以自由的定义颜色和尺寸。将定义的svg用于border-image

```less
@svg square {
  @rect {
    fill: none;  // content为透明
    width: 100%;
    height: 100%;
    stroke-width: 2;
    stroke: var(--color, @bg_line);  // 颜色
  }
}

.one-pixel {
  border: 1px solid transparent;
  border-image: svg(square) 2 2 stretch;
}
.hairlines {
  .one-pixel {
      border-image: none;
      border: 0.5px solid @bg_line;
  }
}
```
