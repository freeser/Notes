---
title: ES6 新特性
date: 2019-02-20 10:25:04
tags: [javascript,es6]
categories: [文档留存]
---

汇总 ES6 的一些新特性

<!-- more -->

# 可选运算符 ?

```js
// 期望数据
const data = {
  user: {
    address: {
      street: "Pennsylvania Avenue",
      neighbors: [1,2]
    }
  }
}

// 数据库查询过来数据
const data = {
  user: {}
};

// 当尝试按下面的方式访问street时，会得到报错：
console.log(data.user.address.street);
// Uncaught TypeError: Cannot read property 'street' of undefined
// 为避免这种情况，需要按如下方式访问“street”属性：
const street = data && data.user && data.user.address && data.user.address.street;
console.log(street); // undefined`

// 如果使用可选运算符，可以这样编码：
console.log(data.user?.address?.street);
// undefined
// 访问数组方式如下
console.log(data?.user?.address?.neighbors?.[number]);

```


有时我们不知道某个函数是否在对象中实现，一个常见的场景是，某些旧版浏览器可能没有某些功能，我们可以使用可选运算符接来检测函数是否已实现。看如下代码：

```js
const data = {
  user: {
    address: {
      street: 'main street',
      neighbors: [
        'john doe',
        'jane doe',
      ],
    },
    getNeighbors() {
      return data.user.address.neighbors;
    }
  },
};

function getNeighbors(data) {
  return data?.user?.getNeighbors?.();
}

test('Optional chaining also works with functions', (t) => {
  const neighbors = getNeighbors(data);
  t.is(neighbors.length, 2);
  t.is(neighbors[0], 'john doe');
});

test('Optional chaining returns undefined if a function does not exist', (t) => {
  const neighbors = getNeighbors({});
  t.is(neighbors, undefined);
});
```

如果在可选链操作符之后是 undefined 或者 null则什么都不会执行，我们可以在以下测试中看到该规则的实际应用：

```js
let neighborCount = 0;

function getNextNeighbor(neighbors) {
  return neighbors?.[++neighborCount];
}

test('It short circuits expressions', (t) => {
  const neighbors = getNeighbors(data);
  t.is(getNextNeighbor(neighbors), 'jane doe');
  t.is(getNextNeighbor(undefined), undefined);
  t.is(neighborCount, 1);
});
```

# 空值合并 ??

以下是我们在JavaScript中看到的一些常见操作：

1. 检查 null 或 undefined
2. 给变量设置默认值
3. 确保0，false和''不设置默认值

这就是新提案的用武之地，现在我们可以这样做：

```js
value ?? 'default value';
```

# 管道运算符 |>

```js
function doubleSay (str) {
  return str + ", " + str;
}
function capitalize (str) {
  return str[0].toUpperCase() + str.substring(1);
}
function exclaim (str) {
  return str + '!';
}

// 原始实现
let result = exclaim(capitalize(doubleSay("hello")));
result //=> "Hello, hello!"

// 使用新的管道运算符，可以不使用第三方库并按如下所示编写上述内容：
let result = "hello"
  |> doubleSay
  |> capitalize
  |> exclaim;

result //=> "Hello, hello!"`
```

这个提案目的是使链式调用函数更具可读性，在未来结合函数部分应用也可以很好的工作，类似下面这种使用方式：
```js
let result = 1
  |> (_ => Math.max(0, _));
result //=> 1

let result = -5
  |> (_ => Math.max(0, _));
result //=> 0

test('Async pipeline', async (t) => {
  const asyncAdd = (number) => Promise.resolve(number + 5);
  const subtractOne = (num1) => num1 - 1;
  const result = 10
    |> asyncAdd
    |> (async (num) => subtractOne(await num));

  t.is(await result, 14);
});
```