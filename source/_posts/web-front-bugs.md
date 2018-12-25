---
title: 前端遇到的那些坑
date: 2018-12-25 20:28:28
tags: [js, css, bugs]
categories: [代码片断, 收集整理]
---

本文旨在收集平时遇到的那些坑

<!-- more --> 


# iOS Safari

* `Safari`下使用`border-image`，不能设置`border-color: transparent`。

* 使用`<meta name="format-detection" content="telephone=no" />`解决连续数字误识别为电话号码导致样式出错的问题。

* `Safari`的`iframe`会自动去适应内容大小而无视`CSS`中设置的`width`，该特性只能通过`scrolling="no"`属性关闭，并通过设置如下`CSS`样式设置宽度`width: 1px; min-width: 100%`;但是设置`scrolling="no"`会导致安卓下`iframe`无法滑动，目前只能通过`UA设备`判断解决。

* Safari 10以下的`flex`布局不认`width`和`flex-basis`，但是会认`min-width`，详见`Can I Us`e 中 `flex` 的 `Known issue`第一条。

* 在 `Safari` 中，`setTimeout` 无法触发 `focus` 事件，且不支持 `autofocus` 属性。可以使用`Promise.resolve().then()`来执行需要异步的 `focus` 代码。

* iOS 10 safari 会无视`meta user-scalable=no`，需要用`e.preventDefault`来解决。个人解决方案[mobile-polyfill/ios10-user-scalable-no.js](https://github.com/LeuisKen/mobile-polyfill/blob/master/ios10-user-scalable-no.js)。建议结合[ua-parser-js](https://github.com/faisalman/ua-parser-js)使用，因为iOS 10+的其他浏览器（QQ、UC）等都还是尊敬这个东西的。

* Safari下开无痕浏览模式，操作`localStorage`会直接报错，需要`try catch`。

* 部分版本的`padStart/padEnd`实现有bug，会出现`null`

* http://www.joycesong.com/archives/1082
ios版本：11.1-11.3
使用`swiper`或者`transform`属性时，有一定概率出现`transform`的元素以外的所有 元素都消失，上下滑动一下页面又出现了。给外层元素加一个`overflow:hidden`属性即可解决。

* 当使用`-webkit-overflow-scrolling: touch;`时，同时使用`::-webkit-scrollbar`伪类的`display:none`隐藏滚动条在iOS 11+出现失效的情况，需要使用如下方案解决，参考：https://stackoverflow.com/questions/38437613/css-overflow-scrolling-and-hidden-scrollbar-ios

```css
#element::-webkit-scrollbar {
    background-color: transparent;
}
#element::-webkit-scrollbar-track {
    background-color: transparent;
}
#element::-webkit-scrollbar-thumb {
    background-color: transparent;
}
```

# Andriod

* 针对部分浏览器非预期的缓存机制，需要服务端添加如下HTTP头信息：
```js
Cache-Control:no-cache, no-store, must-revalidate
Expires:0
Pragma:no-cache
```

* 部分机型`touchmove`事件不连续触发

Android的事件每次都要经过浏览器内核再发往UI线程，为了提高效率，如果浏览器内核中没有设置`preventDefault`，Android就认为该页面元素不需要`touchmove`事件，于是下次的事件就不经过内核，直接发往UI线程，于是js中就捕获不到`touchmove`事件。

解决方案：在事件响应的地方设置`preventDefault`，这样就可以源源不绝地接收到touch事件，比如在`touchstart`事件中执行`e.preventDefault()`，`touchmove`事件就会连续触发。但是这种做法会取消掉浏览器其他的默认行为，比如页面默认的滚动。。。

* 字号小于12px，或字号不是偶数，部分机型文字无法居中的问题
解决方案：使用`transform: scale(0.5)`进行缩放 or 字号大一点

* scroll 相关方法兼容问题
CSSOM 视图模型新增了一些 scroll 相关的方法，参考：https://drafts.csswg.org/cssom-view/#extension-to-the-element-interface
polyfill：https://github.com/iamdustan/smoothscroll

# 综合问题

## 禁止页面滑动

当你需要禁止移动端页面滑动的时候，在iOS下，需要禁止页面中的`touchmove`事件，在安卓下，需要给`html, body`元素加上如下`CSS：height:100%;overflow:hidden;`。

推荐使用以下代码进行处理：

```js
// scrollLock 窗口控制器，用于在弹层出现时禁止、消失时恢复屏幕的滑动
const scrollLock = (function () {
    let isLocked = false;

    function touchmoveHandler(e) {
        e.preventDefault();
    }

    function lock() {
        // 为 html、body元素设置 overflow:hidden height:100% 以限制滚动
        // https://gist.github.com/ufologist/ac1dfa6fa6192a5879e9d9dcdbd0bf54
        document.body.classList.add('sm-no-scroll');
        document.documentElement.classList.add('sm-no-scroll');
        // 限制页面在iOS下的滑动
        document.addEventListener('touchmove', touchmoveHandler);
        isLocked = true;
    }

    function unlock() {
        document.body.classList.remove('sm-no-scroll');
        document.documentElement.classList.remove('sm-no-scroll');
        document.removeEventListener('touchmove', touchmoveHandler);
        isLocked = false;
    }

    return function (on) {
        if (arguments.length === 0) {
            if (isLocked) {
                unlock();
            }
            else {
                lock();
            }
        }
        else {
            if (on && !isLocked) {
                lock();
            }
            else if(!on && isLocked) {
                unlock();
            }
        }
    };
})();
```

```css
.sm-no-scroll {
    height: 100%;
    overflow: hidden;
}
```
但由于禁掉了`touchmove`事件，导致iOS下你希望滚动的部分也无法滚动了，因此对于希望滚动的部分，通过`e.stopPropagation`保留原有滚动效果，并针对回弹动画的交互，建议使用如下代码声明一个可滚动区域：

```css
/* 以下属性添加到滚动容器上 */
-webkit-overflow-scrolling: touch;
overflow: auto;
```

```js
// el 为滚动容器
el.addEventListener('touchstart', function (e) {
    this.allowUp = (this.scrollTop > 0);
    this.allowDown = (this.scrollTop < this.scrollHeight - this.clientHeight);
    this.lastY = e.targetTouches[0].pageY;
});

el.addEventListener('touchmove', function (e) {
    let up = (e.targetTouches[0].pageY > this.lastY);
    let down = !up;

    this.lastY = e.targetTouches[0].pageY;
    if ((up && this.allowUp) || (down && this.allowDown)) {
        e.stopPropagation();
    }
    else {
        e.preventDefault();
    }
});
```

## 被屏蔽的 class

有些浏览器或者插件会通过DOM元素的class来识别是否为广告，并隐藏或者直接删除DOM。

* mask
* banner
* fixed
* sticky

## 点透

移动端的 `click` 触发顺序是`touchstart->touchmove->touchend->mousedown->mousemove->mouseenter->click`。

在重叠的区域里，被遮盖的元素绑定`click`，遮盖的元素绑定`touch`事件，且`touch`后遮盖的元素会隐藏的话，就会造成穿透，因为`click`是在`touch`之后延迟触发的，浏览器会误认为是在遮盖的元素上触发了`click`。

解决方案：`fastclick` or `point-event:none`。