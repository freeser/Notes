---
title: javascript常用小方法
date: 2017-11-16 11:59:37
tags: [js, libs]
categories: [收集整理, 摘抄备份]
---

收集整理工作中重复用到的`日期格式化`、`url参数转对象`、`浏览器类型判断`、`节流函数`等一类函数，这些工具类函数

<!-- more --> 

# 常用函数汇总

## Array

### arrayEqual

```js
/**
 * 
 * @desc 判断两个数组是否相等
 * @param {Array} arr1 
 * @param {Array} arr2 
 * @return {Boolean}
 */
function arrayEqual(arr1, arr2) {
    if (arr1 === arr2) return true;
    if (arr1.length != arr2.length) return false;
    for (var i = 0; i < arr1.length; ++i) {
        if (arr1[i] !== arr2[i]) return false;
    }
    return true;
}
```

### indexOf()

`indexOf()`方法 返回根据给定元素找到的第一个索引值，否则返回-1。
`array.indexOf(searchElement[, fromIndex = 0])`

兼容IE6～8的写法：
```js
if (!Array.prototype.indexOf) {
    Array.prototype.indexOf = function(ele) {
        // 获取数组长度
        var len = this.length;
        // 检查值为数字的第二个参数是否存在，默认值为0
        var fromIndex = Number(arguments[1]) || 0;
        // 当第二个参数小于0时，为倒序查找，相当于查找索引值为该索引加上数组长度后的值
        if(fromIndex < 0) {
            fromIndex += len;
        }
        // 从fromIndex起循环数组
        while(fromIndex < len) {
            // 检查fromIndex是否存在且对应的数组元素是否等于ele
            if(fromIndex in this && this[fromIndex] === ele) {
                return fromIndex;
            }
            fromIndex++;
        }
        // 当数组长度为0时返回不存在的信号：-1
        if (len === 0) {
            return -1;
        }
    }
}
```


### forEach()

`forEach()`方法让数组的每一项都执行一次给定的函数。forEach()方法可以修改原数组。
**语法：**
`array.forEach(callback[, thisArg])`
**参数：** 
1. `callback` ：在数组每一项上执行的函数，接收三个参数：`currentValue`（当前项的值）、`index`（当前项的索引）和`array`（数组本身）。
2. `thisArg` ：可选参数。用来当作`callback` 函数内`this`的值的对象，即`callback` 函数的执行上下文；
`forEach` 方法按升序为数组中含有效值的每一项执行一次`callback` 函数，那些已删除（使用`delete`方法等情况）或者从未赋值的项将被跳过（但不包括哪些值为 `undefined` 的项）。

兼容IE6～8的写法：
```js
if ( !Array.prototype.forEach) {
  Array.prototype.forEach = function forEach(callback) {
    // 获取数组长度
    var len = this.length;
    if(typeof callback != "function") {
        throw new TypeError();
    }
    // thisArg为callback 函数的执行上下文环境
    var thisArg = arguments[1];
    for(var i = 0; i < len; i++) {
        if(i in this) {
            // callback函数接收三个参数：当前项的值、当前项的索引和数组本身
            callback.call(thisArg, this[i], i, this);
        }
    }
  }
}
```

### map()

`map()`方法返回一个由原数组中的每个元素调用一个指定方法后的返回值组成的新数组。
`array.map(callback[, thisArg])`

兼容IE6～8的写法：
```js
if (!Array.prototype.map) {
  Array.prototype.map = function(callback) {
      // 获取数组长度
      var len = this.length;
      if(typeof callback != "function") {
          throw new TypeError();
      }
      // 创建跟原数组相同长度的新数组，用于承载经回调函数修改后的数组元素
      var newArr = new Array(len);
      // thisArg为callback 函数的执行上下文环境
      var thisArg = arguments[1];
      for(var i = 0; i < len; i++) {
          if(i in this) {
              newArr[i] = callback.call(thisArg, this[i], i, this);
          }
      }
      return newArr;
  }    
}
```

### filter()

`filter()` 方法利用所有通过指定函数测试的元素创建一个新的数组，并返回。
`array.filter(callback[, thisArg])`

兼容IE6～8的写法：
```js
if (!Array.prototype.filter) {
    Array.prototype.filter = function(callback) {
      // 获取数组长度
      var len = this.length;
      if(typeof callback != "function") {
          throw new TypeError();
      }
      // 创建新数组，用于承载经回调函数修改后的数组元素
      var newArr = new Array();
      // thisArg为callback 函数的执行上下文环境
      var thisArg = arguments[1];
      for(var i = 0; i < len; i++) {
          if(i in this) {
              if(callback.call(thisArg, this[i], i, this)) {
                  newArr.push(val);
              }
          }
      }
      return newArr;
  }
}
```


### some()

`some()`方法测试数组中的某些元素是否通过了指定函数的测试。返回布尔值。被调用时不会改变数组。
`arr.some(callback[, thisArg])`

兼容IE6～8的写法：
```js
if (!Array.prototype.some) {
  Array.prototype.some = function(callback) {
      // 获取数组长度
      var len = this.length;
      if(typeof callback != "function") {
          throw new TypeError();
      }
      // thisArg为callback 函数的执行上下文环境
      var thisArg = arguments[1];
      for(var i = 0; i < len; i++) {
          if(i in this && callback.call(thisArg, this[i], i, this)) {
              return true;
          }
      }
      return false;
  }
}
```

### every()

`every()`方法测试数组的所有元素是否都通过了指定函数的测试。返回布尔值。不会改变原数组。
`arr.every(callback[, thisArg])`

兼容IE6～8的写法：
```js
if (!Array.prototype.every) {
  Array.prototype.every = function(callback) {
      // 获取数组长度
      var len = this.length;
      if(typeof callback != "function") {
          throw new TypeError();
      }
      // thisArg为callback 函数的执行上下文环境
      var thisArg = arguments[1];
      for(var i = 0; i < len; i++) {
          if(i in this && !callback.call(thisArg, this[i], i, this)) {
              return false;
          }
      }
      return true;
  }
}
```

## Class

### hasClass

```js
/**
 * 
 * @desc 判断元素是否有某个class
 * @param {HTMLElement} ele 
 * @param {String} cls 
 * @return {Boolean}
 */
function hasClass(ele, cls) {
    return (new RegExp('(\\s|^)' + cls + '(\\s|$)')).test(ele.className);
}
```


### addClass

```js
/**
 * 
 * @desc   为元素添加class
 * @param  {HTMLElement} ele 
 * @param  {String} cls 
 */

var hasClass = require('./hasClass');

function addClass(ele, cls) {
    if (!hasClass(ele, cls)) {
        ele.className += ' ' + cls;
    }
}
```


### removeClass

```js
/**
 * 
 * @desc 为元素移除class
 * @param {HTMLElement} ele 
 * @param {String} cls 
 */

var hasClass = require('./hasClass');

function removeClass(ele, cls) {
    if (hasClass(ele, cls)) {
        var reg = new RegExp('(\\s|^)' + cls + '(\\s|$)');
        ele.className = ele.className.replace(reg, ' ');
    }
}
```

## Cookie

### getCookie

```js
/**
 * 
 * @desc 根据name读取cookie
 * @param  {String} name 
 * @return {String}
 */
function getCookie(name) {
    var _cookie = document.cookie;
    var _start = _cookie.indexOf(name + "=");
    if (_start != -1) {
        _start += name.length + 1;
        var _end = _cookie.indexOf(";", _start);
        if (_end == -1) {
            _end = _cookie.length;
        }
        return unescape(_cookie.substring(_start, _end));
    }
    return "";
}
// 变种方法，替换Language
function getCookie() {
    // window.document.cookie.match('(^|;) ?' + name + '=([^;]*)(;|$)');
    return document.cookie.match(/Language=(\S*)(;|\b)/)[1]
}
```

### setCookie

```js
/**
 * 
 * @desc  设置Cookie
 * @param {String} name 
 * @param {String} value 
 * @param {Number} expires 失效时间，单位秒
 */
function setCookie(name, value, expires) {
    var _end = new Date();
    expires &&  _end.setTime(_end.getTime() + (expires * 1000));
    document.cookie = name + "=" + escape(value) + (expires ? (";expires=" + _end.toGMTString()) : "") + ";path=/;domain=" + location.host;
}
```

### removeCookie

```js
var getCookie = require('./getCookie');
/**
 * 
 * @desc 根据name删除cookie
 * @param  {String} name 
 */
function removeCookie(name) {
    var exp = new Date();
    exp.setTime(exp.getTime() - 1);
    var cval = getCookie(name);
    if (cval != null)
        document.cookie = name + "=" + cval + ";expires=" + exp.toGMTString();
}
```

## Device

### getExplore

```js
/**
 * 
 * @desc 获取浏览器类型和版本
 * @return {String} 
 */
function getExplore() {
    var sys = {},
        ua = navigator.userAgent.toLowerCase(),
        s;
    (s = ua.match(/rv:([\d.]+)\) like gecko/)) ? sys.ie = s[1]:
        (s = ua.match(/msie ([\d\.]+)/)) ? sys.ie = s[1] :
        (s = ua.match(/edge\/([\d\.]+)/)) ? sys.edge = s[1] :
        (s = ua.match(/firefox\/([\d\.]+)/)) ? sys.firefox = s[1] :
        (s = ua.match(/(?:opera|opr).([\d\.]+)/)) ? sys.opera = s[1] :
        (s = ua.match(/chrome\/([\d\.]+)/)) ? sys.chrome = s[1] :
        (s = ua.match(/version\/([\d\.]+).*safari/)) ? sys.safari = s[1] : 0;
    // 根据关系进行判断
    if (sys.ie) return ('IE: ' + sys.ie)
    if (sys.edge) return ('EDGE: ' + sys.edge)
    if (sys.firefox) return ('Firefox: ' + sys.firefox)
    if (sys.chrome) return ('Chrome: ' + sys.chrome)
    if (sys.opera) return ('Opera: ' + sys.opera)
    if (sys.safari) return ('Safari: ' + sys.safari)
    return 'Unkonwn'
}
```

### getOS

```js
/**
 * 
 * @desc 获取操作系统类型
 * @return {String} 
 */
function getOS() {
    var userAgent = 'navigator' in window && 'userAgent' in navigator && navigator.userAgent.toLowerCase() || '';
    var vendor = 'navigator' in window && 'vendor' in navigator && navigator.vendor.toLowerCase() || '';
    var appVersion = 'navigator' in window && 'appVersion' in navigator && navigator.appVersion.toLowerCase() || '';

    if (/mac/i.test(appVersion)) return 'MacOSX'
    if (/win/i.test(appVersion)) return 'windows'
    if (/linux/i.test(appVersion)) return 'linux'
    if (/iphone/i.test(userAgent) || /ipad/i.test(userAgent) || /ipod/i.test(userAgent)) 'ios'
    if (/android/i.test(userAgent)) return 'android'
    if (/win/i.test(appVersion) && /phone/i.test(userAgent)) return 'windowsPhone'
}
```

## Dom

### offset

```js
/**
 * 
 * @desc  获取一个元素的距离文档(document)的位置，类似jQ中的offset()
 * @param {HTMLElement} ele 
 * @returns { {left: number, top: number} }
 */
function offset(ele) {
    var pos = {
        left: 0,
        top: 0
    };
    while (ele) {
        pos.left += ele.offsetLeft;
        pos.top += ele.offsetTop;
        ele = ele.offsetParent;
    };
    return pos;
}
```

### getScrollTop

```js
/**
 * 
 * @desc 获取滚动条距顶部的距离
 */
function getScrollTop() {
    return (document.documentElement && document.documentElement.scrollTop) || document.body.scrollTop;
}
```

### setScrollTop

```js
/**
 * 
 * @desc 设置滚动条距顶部的距离
 */
function setScrollTop(value) {
    window.scrollTo(0, value);
    return value;
}
```

### scrollTo

```js
var getScrollTop = require('./getScrollTop');
var setScrollTop = require('./setScrollTop');
var requestAnimFrame = (function () {
    return window.requestAnimationFrame ||
        window.webkitRequestAnimationFrame ||
        window.mozRequestAnimationFrame ||
        function (callback) {
            window.setTimeout(callback, 1000 / 60);
        };
})();
/**
 * 
 * @desc  在${duration}时间内，滚动条平滑滚动到${to}指定位置
 * @param {Number} to 
 * @param {Number} duration 
 */
function scrollTo(to, duration) {
    if (duration < 0) {
        setScrollTop(to);
        return
    }
    var diff = to - getScrollTop();
    if (diff === 0) return
    var step = diff / duration * 10;
    requestAnimationFrame(
        function () {
            if (Math.abs(step) > Math.abs(diff)) {
                setScrollTop(getScrollTop() + diff);
                return;
            }
            setScrollTop(getScrollTop() + step);
            if (diff > 0 && getScrollTop() >= to || diff < 0 && getScrollTop() <= to) {
                return;
            }
            scrollTo(to, duration - 16);
        });
}
```

## Keycode

### getKeyName

```js
var keyCodeMap = {
    8: 'Backspace',
    9: 'Tab',
    13: 'Enter',
    16: 'Shift',
    17: 'Ctrl',
    18: 'Alt',
    19: 'Pause',
    20: 'Caps Lock',
    27: 'Escape',
    32: 'Space',
    33: 'Page Up',
    34: 'Page Down',
    35: 'End',
    36: 'Home',
    37: 'Left',
    38: 'Up',
    39: 'Right',
    40: 'Down',
    42: 'Print Screen',
    45: 'Insert',
    46: 'Delete',

    48: '0',
    49: '1',
    50: '2',
    51: '3',
    52: '4',
    53: '5',
    54: '6',
    55: '7',
    56: '8',
    57: '9',

    65: 'A',
    66: 'B',
    67: 'C',
    68: 'D',
    69: 'E',
    70: 'F',
    71: 'G',
    72: 'H',
    73: 'I',
    74: 'J',
    75: 'K',
    76: 'L',
    77: 'M',
    78: 'N',
    79: 'O',
    80: 'P',
    81: 'Q',
    82: 'R',
    83: 'S',
    84: 'T',
    85: 'U',
    86: 'V',
    87: 'W',
    88: 'X',
    89: 'Y',
    90: 'Z',

    91: 'Windows',
    93: 'Right Click',

    96: 'Numpad 0',
    97: 'Numpad 1',
    98: 'Numpad 2',
    99: 'Numpad 3',
    100: 'Numpad 4',
    101: 'Numpad 5',
    102: 'Numpad 6',
    103: 'Numpad 7',
    104: 'Numpad 8',
    105: 'Numpad 9',
    106: 'Numpad *',
    107: 'Numpad +',
    109: 'Numpad -',
    110: 'Numpad .',
    111: 'Numpad /',

    112: 'F1',
    113: 'F2',
    114: 'F3',
    115: 'F4',
    116: 'F5',
    117: 'F6',
    118: 'F7',
    119: 'F8',
    120: 'F9',
    121: 'F10',
    122: 'F11',
    123: 'F12',

    144: 'Num Lock',
    145: 'Scroll Lock',
    182: 'My Computer',
    183: 'My Calculator',
    186: ';',
    187: '=',
    188: ',',
    189: '-',
    190: '.',
    191: '/',
    192: '`',
    219: '[',
    220: '\\',
    221: ']',
    222: '\''
};
/**
 * @desc 根据keycode获得键名
 * @param  {Number} keycode 
 * @return {String}
 */
function getKeyName(keycode) {
    if (keyCodeMap[keycode]) {
        return keyCodeMap[keycode];
    } else {
        console.log('Unknow Key(Key Code:' + keycode + ')');
        return '';
    }
};
```

## Object

### deepClone

```js
/**
 * @desc 深拷贝，支持常见类型
 * @param {Any} values
 */
function deepClone(values) {
    var copy;

    // Handle the 3 simple types, and null or undefined
    if (null == values || "object" != typeof values) return values;

    // Handle Date
    if (values instanceof Date) {
        copy = new Date();
        copy.setTime(values.getTime());
        return copy;
    }

    // Handle Array
    if (values instanceof Array) {
        copy = [];
        for (var i = 0, len = values.length; i < len; i++) {
            copy[i] = deepClone(values[i]);
        }
        return copy;
    }

    // Handle Object
    if (values instanceof Object) {
        copy = {};
        for (var attr in values) {
            if (values.hasOwnProperty(attr)) copy[attr] = deepClone(values[attr]);
        }
        return copy;
    }

    throw new Error("Unable to copy values! Its type isn't supported.");
}
```

### isEmptyObject

```js
/**
 * 
 * @desc   判断`obj`是否为空
 * @param  {Object} obj
 * @return {Boolean}
 */
function isEmptyObject(obj) {
    if (!obj || typeof obj !== 'object' || Array.isArray(obj))
        return false
    return !Object.keys(obj).length
}
```

## Random

### randomColor

```js
/**
 * 
 * @desc 随机生成颜色
 * @return {String} 
 */
function randomColor() {
    return '#' + ('00000' + (Math.random() * 0x1000000 << 0).toString(16)).slice(-6);
}
```

### randomNum

```js
/**
 * 
 * @desc 生成指定范围随机数
 * @param  {Number} min 
 * @param  {Number} max 
 * @return {Number} 
 */
function randomNum(min, max) {
    return Math.floor(min + Math.random() * (max - min));
}
```

## Regexp

### isEmail

```js
/**
 * 
 * @desc   判断是否为邮箱地址
 * @param  {String}  str
 * @return {Boolean} 
 */
function isEmail(str) {
    return /\w+([-+.]\w+)*@\w+([-.]\w+)*\.\w+([-.]\w+)*/.test(str);
}
```

### isIdCard

```js
/**
 * 
 * @desc  判断是否为身份证号
 * @param  {String|Number} str 
 * @return {Boolean}
 */
function isIdCard(str) {
    let reg = /^(^[1-9]\d{7}((0\d)|(1[0-2]))(([0|1|2]\d)|3[0-1])\d{3}$)|(^[1-9]\d{5}[1-9]\d{3}((0\d)|(1[0-2]))(([0|1|2]\d)|3[0-1])((\d{4})|\d{3}[Xx])$)$/
    if (reg.test(str)) {
        let idCardWi = [7, 9, 10, 5, 8, 4, 2, 1, 6, 3, 7, 9, 10, 5, 8, 4, 2] // 将前17位加权因子保存在数组里
        let idCardY = [1, 0, 10, 9, 8, 7, 6, 5, 4, 3, 2] // 这是除以11后，可能产生的11位余数、验证码，也保存成数组
        let idCardWiSum = 0 // 用来保存前17位各自乖以加权因子后的总和
        for (let i = 0; i < 17; i++) {
            idCardWiSum += idCard.substring(i, i + 1) * idCardWi[i]
        }
        let idCardMod = idCardWiSum % 11 // 计算出校验码所在数组的位置
        let idCardLast = idCard.substring(17) // 得到最后一位身份证号码
        if (idCardMod == 2) {
            return idCardLast == 'X' || idCardLast == 'x'
        } else {
            return idCardLast == idCardY[idCardMod]
        }
    }
    return false
}
```

### isPhoneNum

```js
/**
 * 
 * @desc   判断是否为手机号
 * @param  {String|Number} str 
 * @return {Boolean} 
 */
function isPhoneNum(str) {
    return /^(0|86|17951)?(13[0-9]|15[012356789]|17[678]|18[0-9]|14[57])[0-9]{8}$/.test(str)
}
```

### isUrl

```js
/**
 * 
 * @desc   判断是否为URL地址
 * @param  {String} str 
 * @return {Boolean}
 */
function isUrl(str) {
    return /[-a-zA-Z0-9@:%._\+~#=]{2,256}\.[a-z]{2,6}\b([-a-zA-Z0-9@:%_\+.~#?&//=]*)/i.test(str);
}
```

## String

### digitUppercase

```js
/**
 * 
 * @desc   现金额转大写
 * @param  {Number} n 
 * @return {String}
 */
function digitUppercase(n) {
    var fraction = ['角', '分'];
    var digit = [
        '零', '壹', '贰', '叁', '肆',
        '伍', '陆', '柒', '捌', '玖'
    ];
    var unit = [
        ['元', '万', '亿'],
        ['', '拾', '佰', '仟']
    ];
    var head = n < 0 ? '欠' : '';
    n = Math.abs(n);
    var s = '';
    for (var i = 0; i < fraction.length; i++) {
        s += (digit[Math.floor(n * 10 * Math.pow(10, i)) % 10] + fraction[i]).replace(/零./, '');
    }
    s = s || '整';
    n = Math.floor(n);
    for (var i = 0; i < unit[0].length && n > 0; i++) {
        var p = '';
        for (var j = 0; j < unit[1].length && n > 0; j++) {
            p = digit[n % 10] + unit[1][j] + p;
            n = Math.floor(n / 10);
        }
        s = p.replace(/(零.)*零$/, '').replace(/^$/, '零') + unit[0][i] + s;
    }
    return head + s.replace(/(零.)*零元/, '元')
        .replace(/(零.)+/g, '零')
        .replace(/^整$/, '零元整');
};
```

## Support

### isSupportWebP

```js
/**
 * 
 * @desc 判断浏览器是否支持webP格式图片
 * @return {Boolean} 
 */
function isSupportWebP() {
    return !![].map && document.createElement('canvas').toDataURL('image/webp').indexOf('data:image/webp') == 0;
}
```

## Time

### formatPassTime

```js
/**
 * @desc   格式化${startTime}距现在的已过时间
 * @param  {Date} startTime 
 * @return {String}
 */
function formatPassTime(startTime) {
    var currentTime = Date.parse(new Date()),
        time = currentTime - startTime,
        day = parseInt(time / (1000 * 60 * 60 * 24)),
        hour = parseInt(time / (1000 * 60 * 60)),
        min = parseInt(time / (1000 * 60)),
        month = parseInt(day / 30),
        year = parseInt(month / 12);
    if (year) return year + "年前"
    if (month) return month + "个月前"
    if (day) return day + "天前"
    if (hour) return hour + "小时前"
    if (min) return min + "分钟前"
    else return '刚刚'
}
```

### formatRemainTime

```js
/**
 * 
 * @desc   格式化现在距${endTime}的剩余时间
 * @param  {Date} endTime  
 * @return {String}
 */
function formatRemainTime(endTime) {
    var startDate = new Date(); //开始时间
    var endDate = new Date(endTime); //结束时间
    var t = endDate.getTime() - startDate.getTime(); //时间差
    var d = 0,
        h = 0,
        m = 0,
        s = 0;
    if (t >= 0) {
        d = Math.floor(t / 1000 / 3600 / 24);
        h = Math.floor(t / 1000 / 60 / 60 % 24);
        m = Math.floor(t / 1000 / 60 % 60);
        s = Math.floor(t / 1000 % 60);
    }
    return (d ? d + "天 " : "" ) + (h ? h + "小时 " : '') + (m ? m +"分钟 " : "") + s + "秒";
}
```

### Format
```js
/**
 * 
 * @desc 将日期格式化成特定格式
 * @param  {String} fmt 格式如'yyyy-MM-dd'
 * @return {String}
 */
Date.prototype.Format = function (fmt) { //author: meizz
    var o = {
        "M+": this.getMonth() + 1, //月份
        "d+": this.getDate(), //日
        "h+": this.getHours(), //小时
        "m+": this.getMinutes(), //分
        "s+": this.getSeconds(), //秒
        "q+": Math.floor((this.getMonth() + 3) / 3), //季度
        "S": this.getMilliseconds() //毫秒
    };
    if (/(y+)/.test(fmt))
        fmt = fmt.replace(RegExp.$1, (this.getFullYear() + "").substr(4 - RegExp.$1.length));
    for (var k in o)
        if (new RegExp("(" + k + ")").test(fmt))
            fmt = fmt.replace(RegExp.$1, (RegExp.$1.length == 1) ? (o[k]) : (("00" + o[k]).substr(("" + o[k]).length)));
    return fmt;
}
```

## Url

### parseQueryString

```js
/**
 * 
 * @desc   url参数转对象
 * @param  {String} url  default: window.location.href
 * @return {Object} 
 */
function parseQueryString(url) {
    var url = url || window.location.href
    var search = url.substring(url.lastIndexOf('?') + 1)
    if (!search) {
        return {}
    }
    return JSON.parse('{"' + decodeURIComponent(search).replace(/"/g, '\\"').replace(/&/g, '","').replace(/=/g, '":"') + '"}')
}
```

### getParam

```js
    /**
    * 
    * @desc   url参数获取值
    * @param  {String} name  要从url中提取的键值名
    * @param  {String} url  default: window.location.href
    * @return {String} 
    */
    function getParam(name, href) {
        name = name.replace(/[\[]/, "\\\[").replace(/[\]]/, "\\\]");
        var regexS = "[\\?&]" + name + "=([^&#]*)";
        var regex = new RegExp(regexS);
        var results = regex.exec(href || window.location.href);
        return results ? results[1] : "";
    }
    // 方法二
    const getQueryParams = () => {
        const params = {}
        window.location.href.replace(/([^(?|#)=&]+)(=([^&]*))?/g, ($0, $1, $2, $3) => {
            params[$1] = $3
        })
        return params
    }
```

### stringfyQueryString

```js
/**
 * 
 * @desc   对象序列化
 * @param  {Object} obj 
 * @return {String}
 */
function stringfyQueryString(obj) {
    if (!obj) return '';
    var pairs = [];
    for (var key in obj) {
        var value = obj[key];
        if (value instanceof Array) {
            for (var i = 0; i < value.length; ++i) {
                pairs.push(encodeURIComponent(key + '[' + i + ']') + '=' + encodeURIComponent(value[i]));
            }
            continue;
        }
        pairs.push(encodeURIComponent(key) + '=' + encodeURIComponent(obj[key]));
    }
    return pairs.join('&');
}
```

## Function

### throttle

```js
    /**
    * @desc   函数节流。
    * 适用于限制`resize`和`scroll`等函数的调用频率
    *
    * @param  {Number}    delay          0 或者更大的毫秒数。 对于事件回调，大约100或250毫秒（或更高）的延迟是最有用的。
    * @param  {Boolean}   noTrailing     可选，默认为false。
    *                                    如果noTrailing为true，当节流函数被调用，每过`delay`毫秒`callback`也将执行一次。
    *                                    如果noTrailing为false或者未传入，`callback`将在最后一次调用节流函数后再执行一次.
    *                                    （延迟`delay`毫秒之后，节流函数没有被调用,内部计数器会复位）
    * @param  {Function}  callback       延迟毫秒后执行的函数。`this`上下文和所有参数都是按原样传递的，
    *                                    执行去节流功能时，调用`callback`。
    * @param  {Boolean}   debounceMode   如果`debounceMode`为true，`clear`在`delay`ms后执行。
    *                                    如果debounceMode是false，`callback`在`delay` ms之后执行。
    *
    * @return {Function}  新的节流函数
    */
    function throttle(delay, noTrailing, callback, debounceMode) {

        // After wrapper has stopped being called, this timeout ensures that
        // `callback` is executed at the proper times in `throttle` and `end`
        // debounce modes.
        var timeoutID;

        // Keep track of the last time `callback` was executed.
        var lastExec = 0;

        // `noTrailing` defaults to falsy.
        if (typeof noTrailing !== 'boolean') {
            debounceMode = callback;
            callback = noTrailing;
            noTrailing = undefined;
        }

        // The `wrapper` function encapsulates all of the throttling / debouncing
        // functionality and when executed will limit the rate at which `callback`
        // is executed.
        function wrapper() {

            var self = this;
            var elapsed = Number(new Date()) - lastExec;
            var args = arguments;

            // Execute `callback` and update the `lastExec` timestamp.
            function exec() {
                lastExec = Number(new Date());
                callback.apply(self, args);
            }

            // If `debounceMode` is true (at begin) this is used to clear the flag
            // to allow future `callback` executions.
            function clear() {
                timeoutID = undefined;
            }

            if (debounceMode && !timeoutID) {
                // Since `wrapper` is being called for the first time and
                // `debounceMode` is true (at begin), execute `callback`.
                exec();
            }

            // Clear any existing timeout.
            if (timeoutID) {
                clearTimeout(timeoutID);
            }

            if (debounceMode === undefined && elapsed > delay) {
                // In throttle mode, if `delay` time has been exceeded, execute
                // `callback`.
                exec();

            } else if (noTrailing !== true) {
                // In trailing throttle mode, since `delay` time has not been
                // exceeded, schedule `callback` to execute `delay` ms after most
                // recent execution.
                //
                // If `debounceMode` is true (at begin), schedule `clear` to execute
                // after `delay` ms.
                //
                // If `debounceMode` is false (at end), schedule `callback` to
                // execute after `delay` ms.
                timeoutID = setTimeout(debounceMode ? clear : exec, debounceMode === undefined ? delay - elapsed : delay);
            }
        }
        // Return the wrapper function.
        return wrapper;
    }
```

### debounce

```js
    /**
    * @desc 函数防抖 
    * 与throttle不同的是，debounce保证一个函数在多少毫秒内不再被触发，只会执行一次，
    * 要么在第一次调用return的防抖函数时执行，要么在延迟指定毫秒后调用。
    * @example 适用场景：如在线编辑的自动存储防抖。
    * @param  {Number}   delay         0或者更大的毫秒数。 对于事件回调，大约100或250毫秒（或更高）的延迟是最有用的。
    * @param  {Boolean}  atBegin       可选，默认为false。
    *                                  如果`atBegin`为false或未传入，回调函数则在第一次调用return的防抖函数后延迟指定毫秒调用。
                                        如果`atBegin`为true，回调函数则在第一次调用return的防抖函数时直接执行
    * @param  {Function} callback      延迟毫秒后执行的函数。`this`上下文和所有参数都是按原样传递的，
    *                                  执行去抖动功能时，，调用`callback`。
    *
    * @return {Function} 新的防抖函数。
    */
    var throttle = require('./throttle');
    function debounce(delay, atBegin, callback) {
        return callback === undefined ? throttle(delay, atBegin, false) : throttle(delay, callback, atBegin !== false);
    }
```

### 金额千分位

```js
    /**
    * 
    * @desc   将金额转换千分位的，带逗号的
    * @param  {Number} num 要转换的金额 
    * @return {String}
    */
    function thousands(num) {
        num = num.toString();
        num = num.replace(/[^0-9.+-]/g, ''); // 剔除非数字的字符
        num = parseFloat(num) || 0;
        num = num.toString();
        if (/^-?\d+\.?\d+$/.test(num)) {
            if (/^-?\d+$/.test(num)) {
                num = num + ",00";
            } else {
                num = num.replace(/\./, ',');
            }
            while (/\d{4}/.test(num)) {
                num = num.replace(/(\d+)(\d{3}\,)/, '$1,$2');
            }
            num = num.replace(/\,(\d*)$/, '.$1');
        }
        return num
    }
    function thousands(num) {
        return num && num.toString().replace(/([+-]?)(\d+)([.]\d+)?/, function($0, $1, $2, $3) {
            return $1 + $2.replace(/(\d)(?=(\d{3})+$)/g, function($10, $11) {
                return $11 + ',';
            }) + ($3 || '');
        });
    }
    // 方法二（有局限性，不支持负数和小数）
    String(Number).replace(/(\d)(?=(\d{3})+$)/g, "$1,");
    // 方法三，只能精确到小数点后3位
    Number.toLocaleString('en-US');
```
参考：
[千位分隔符(逗号)表示web网页中的大数字](http://www.zhangxinxu.com/wordpress/2017/09/web-page-comma-number/)
[toLocaleString](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString)

### 转度分秒

```js
    Number.prototype.toDfm = function (l) {
        if(this) {
            return formatDegree(this);
        } else {
            return this;
        }
        function formatDegree(value) {
            value = Math.abs(value);
            var v1 = Math.floor(value);
            var v2 = Math.floor((value - v1) * 60);
            var v3 = Math.round((value - v1) * 3600 % 60);
            return v1 + '°' + v2 + '\'' + v3 + '"';
        }
    }
```

## Html

### loadScript

```js
    /*
    * LOAD SCRIPTS
    * Usage:
    * Define function = myPrettyCode ()...
    * loadScript("js/my_lovely_script.js", myPrettyCode);
    */
    function loadScript(scriptName, callback) {
        if (!jsArray) window.jsArray = {};

        if (!jsArray[scriptName]) {
            // 添加script标签到文档中
            var body = document.getElementsByTagName('body')[0],
                script = document.createElement('script');
            script.type = 'text/javascript';
            script.src = scriptName;

            // 绑定资料加载成功的回调方法
            script.onload = function () {
                jsArray[scriptName] = true;
                callback();
            };

            // 添加到dom中
            body.appendChild(script);

            // 清楚变量引用
            //body = null;
            //script = null;
        } else if (callback) {
            // 如果资源已引用则直接回调
            callback();
        }
    }
```

### loadCss

```js
    /*
    * LOAD CSSS
    * Usage:
    * Define function = myPrettyCode ()...
    * loadCss("css/style.css", myPrettyCode);
    */
    function loadCss(linkName, callback, rel) {
        if (!cssArray) window.cssArray = {};
        
        if (!cssArray[linkName]) {
            // 添加script标签到文档中
            var head = document.getElementsByTagName('head')[0],
                link = document.createElement('link');
            link.type = 'text/css';
            link.rel = 'stylesheet' + (rel ? ('/' + rel) : '');
            link.href = linkName;

            link.onload = function () {
                cssArray[linkName] = true;
                callback();
            };

            // fire the loading
            head.appendChild(link);

            // 清楚变量引用
            //head = null;
            //link = null;
        } else if (callback) {
            // 如果资源已引用则直接回调
            callback();
        }
    }
```

## auto-font-size

```js
(function () {
    function o() {
        document.documentElement.style.fontSize = parseInt((document.documentElement.clientWidth > 414 ? 414 : document.documentElement.clientWidth) * 13 / 375) + "px"
    }
    var e = null;
    window.addEventListener("resize", function () {
        clearTimeout(e), e = setTimeout(o, 100)
    }, !1), o()
})(window);
```

## Ajax

```js
function ajax(options){
    var xhr = null;
    var type = options.type || "get";
    var asyn = options.asyn || true;
    var url = options.url;            // url 接收 传输位置
    var success = options.success;    // success 接收 传输完成后的回调函数
    var data = options.data || '';    // data 接收需要附带传输的数据

    if (window.XMLHttpRequest) {
        xhr = new XMLHttpRequest();   //一般浏览器
    } else {
        xhr = new ActiveXObject("Microsoft.XMLHTTP");    //兼容处理IE6+
    }
    if (!xhr) {
        return false;
    }
    if (type == "get" && data){
        url += "/?" + stringfyQueryString(data); // 参考Url下将对象序列化的方法
    }

    //初始化ajax请求
    xhr.open( type , url , asyn );
    //规定传输数据的格式
    xhr.setRequestHeader('content-type','application/x-www-form-urlencoded');
    //发送ajax请求（包括post数据的传输）
    type == "get" ? xhr.send() : xhr.send(data);

    //处理请求
    xhr.onreadystatechange = function(){
        if(xhr.readState == 4){
            if (xhr.status == 200){
                success(xhr.responsetext);
            } else {
                alert("请求出错" + xhr.status)
            }
        }
    }
```

# 常用小工具

## 通过银行卡号得到银行卡信息

示例代码篇幅太长，[点击查看](https://github.com/freeser/example/blob/master/bankcardToinfo.js)
牛人通过[支付宝的开放API](https://ccdcapi.alipay.com/validateAndCacheCardInfo.json?cardNo=6222005865412565805&cardBinCheck=true)封装的代码库，可以直接调用，参考[github地址](https://github.com/navyxie/bankcardinfo)

## ios输入法遮挡键盘的解决方案

```js
    //解决第三方软键盘唤起时底部input输入框被遮挡问题， 改为fixed固定
    var bfscrolltop = document.body.scrollTop;//获取软键盘唤起前浏览器滚动部分的高度
    $("input.inputframe").focus(function(){//在这里‘input.inputframe’是我的底部输入栏的输入框，当它获取焦点时触发事件
        setTimeout(function(){//设置一个计时器，时间设置与软键盘弹出所需时间相近
           document.body.scrollTop = document.body.scrollHeight;//获取焦点后将浏览器内所有内容高度赋给浏览器滚动部分高度
        }, 400)
    }).blur(function(){
        //设定输入框失去焦点时的事件
        document.body.scrollTop = bfscrolltop; // 将软键盘唤起前的浏览器滚动部分高度重新赋给改变后的高度
    });
```
[参考](https://blog.csdn.net/github_37533433/article/details/66471962)

# 性能优化

## createDocumentFragment

一旦需要更新DOM,请考虑使用文档碎片来构建DOM结构，然后再将其添加到现存的文档中。
[参考](https://www.cnblogs.com/liyunhua/p/4529086.html#_label11)

# 鸣谢

1、[打造自己的JavaScript武器库](https://juejin.im/post/5a091afe6fb9a044ff30f402)
2、[JavaScript数组方法的兼容性写法](http://www.dengzhr.com/js/362)
