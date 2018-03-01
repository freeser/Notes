---
title: 学习JavaScript数据结构与算法
date: 2018-02-27 09:58:09
tags: [javascript, data, structrues, book]
categories: [读书笔记, 代码片断]
---

前段时间公司一技术主管考查员工技术水平，听其说大公司面试高级程序员基本上都是数据结构和算法相关的问题，由于我对这方面知之甚少，想往高级进发就得补补知识，所以就有了这本书的阅读。
数据结构在大学也学过，不过时间久了都不记得了，在读书过程中一些比较好的知识点在此记录下来，方便以后使用。

<!-- more --> 
# 栈的创建

栈是一种 遵从后进先出（ LIFO） 原则 的 有序 集合。 新 添加 的 或 待 删除 的 元素 都 保 存在 栈 的 同 一端， 称作 栈 顶， 另一 端 就叫 栈 底。 在 栈 里， 新 元素 都 靠近 栈 顶， 旧 元素 都 接近 栈 底。

```js
function Stack() {
    let items = [];
    this.push = function(element){
        items.push(element);
    };
    this.pop = function(){
        return items.pop();
    };
    this.peek = function(){
        return items[items.length-1];
    };
    this.isEmpty = function(){
        return items.length == 0;
    };
    this.size = function(){
        return items.length;
    };
    this.clear = function(){
        items = [];
    };
    this.print = function(){
        console.log(items.toString());
    };
    this.toString = function(){
        return items.toString();
    };
}
```

# 栈的应用

以下示例代码中的`Stack`就是前文中的示例。

## 十进制转任意进制

```js
    /*
        十进制转二进制
    */
    function divideBy2(decNumber){
        var remStack = new Stack(),
            rem,
            binaryString = '';
        while (decNumber > 0){
            rem = Math.floor(decNumber % 2);
            remStack.push(rem);
            decNumber = Math.floor(decNumber / 2);
        }
        while (!remStack.isEmpty()){
            binaryString += remStack.pop().toString();
        }
        return binaryString;
    }
    console.log(divideBy2(233));
    console.log(divideBy2(10));
    console.log(divideBy2(1000));
    /*
        及于上面方法实现的十进制转任意进制
    */
    function baseConverter(decNumber, base){
        var remStack = new Stack(),
            rem,
            baseString = '',
            digits = '0123456789ABCDEF';
        while (decNumber > 0){
            rem = Math.floor(decNumber % base);
            remStack.push(rem);
            decNumber = Math.floor(decNumber / base);
        }
        while (!remStack.isEmpty()){
            baseString += digits[remStack.pop()];
        }
        return baseString;
    }
    console.log(baseConverter(100345, 2));
    console.log(baseConverter(100345, 8));
    console.log(baseConverter(100345, 16));
```

## 平衡括号

```js
function parenthesesChecker(symbols){
    let stack = new Stack(),
        balanced = true,
        index = 0,
        symbol, top,
        opens = "([{",
        closers = ")]}";
    // 遍历传进来的字符串
    while (index < symbols.length && balanced){
        symbol = symbols.charAt(index); 
        if (opens.indexOf(symbol) >= 0){ // 提取字符串中的开始括号，并添加到栈里面
            stack.push(symbol);
            console.log(`open symbol - stacking ${symbol}`);
        } else if (closers.indexOf(symbol) >= 0) {
            console.log(`close symbol ${symbol}`);
            if (stack.isEmpty()){ //  如果不是开始括号，但是栈里为空，说明没有括号或者已经匹配完成
                balanced = false;
                console.log('Stack is empty, no more symbols to pop and compare');
            } else {
                top = stack.pop(); // 取栈里第一个元素
                //if (!matches(top, symbol)){
                if (!(opens.indexOf(top) === closers.indexOf(symbol))) { // 判断开始括号和结束括号是否是同一种括号，不是，说明字符串内括号不平衡
                    balanced = false;
                    console.log(`poping symbol ${top} - is not a match compared to ${symbol}`);
                } else {
                    console.log(`poping symbol ${top} - is is a match compared to ${symbol}`);
                }
            }
        }
        index++;
    }
    if (balanced && stack.isEmpty()){ // 如果栈没有清空说明还没有匹配完
        return true;
    }
    return false;
}

console.log(parenthesesChecker('{([])}')); //true
console.log(parenthesesChecker('{{([][])}()}')); //true
console.log(parenthesesChecker('[{()]')); //false
```

## 汉诺塔

[汉诺塔](https://baike.baidu.com/item/%E6%B1%89%E8%AF%BA%E5%A1%94/3468295?fr=aladdin)：（又称河内塔）问题是源于印度一个古老传说的益智玩具。大梵天创造世界的时候做了三根金刚石柱子，在一根柱子上从下往上按照大小顺序摞着64片黄金圆盘。大梵天命令婆罗门把圆盘从下面开始按大小顺序重新摆放在另一根柱子上。并且规定，在小圆盘上不能放大圆盘，在三根柱子之间一次只能移动一个圆盘。

**分三步**：
1. 把 n-1 号盘子移动到缓冲区
2. 把1号从起点移到终点
3. 然后把缓冲区的n-1号盘子也移到终点

> 要从a到b 那c就是缓冲 move(n-1,from,to,buffer) 
> 要从a到c 那b就是缓冲 move(1,from,buffer,to)     
> 要从b到c 那a就是缓冲 move(n-1,buffer,from,to) 

```js
    function towerOfHanoi(n, from, to, helper){
        if (n > 0){
            towerOfHanoi(n-1, from, helper, to); // 步骤一
            to.push(from.pop());                 // 步骤二
            console.log('-----');
            console.log('Source: ' + from.toString());
            console.log('Dest: ' + to.toString());
            console.log('Helper: ' + helper.toString());
            towerOfHanoi(n-1, helper, to, from); // 步骤三， 不停的变更起点终点，缓冲区来达到目的
        }
    }

    var source = new Stack();
    source.push(3);
    source.push(2);
    source.push(1);

    var dest = new Stack();
    var helper = new Stack();

    towerOfHanoi(source.size(), source, dest, helper);

    source.print();
    helper.print();
    dest.print();
```