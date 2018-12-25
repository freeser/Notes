---
title: Chrome中console的debugger技巧
date: 2018-12-12 20:35:53
tags: [javascript, debugger, console, chrome]
categories: [读书笔记, 备忘]
---


汇总关于Chrome中高度技巧
<!-- more --> 

# $快捷使用

## $0

在`Chrome`的`Elements`面板中，`$0`关联到当前我们选中的`html`节点。

理所当然，`$1` 是我们上一次选择的节点，`$2`是在那之前选择的节点，等等。一直到 `$4`

你可以用以上的补充参考来尝试一些相关的操作(例子: `$1.appendChild($0)`)

## $ 和 $$

```js
  $ == document.querySelector;
  Array.from(document.querySelectorAll('div')) === $$('div');
```

`$$` 能节省更多的时间，因为它不仅仅执行`document.querySelectorAll`并且返回的是一个节点的数组，而不是一个`Node list`

## $_

`$_` 变量是关联在`console`里面上次执行的结果

## $i

在`Chrome`插件:[Console Importer](https://chrome.google.com/webstore/detail/console-importer/hgajpakhafplebkdljleajgbpdmplhie/related)的帮助之下，你可以快速的在`console`中引入和把玩一些`npm`库。
直接运行例如 `$i('lodash')` 或者 `$i('moment')` 然后在几秒钟之后，你就可以获取到`lodash` / `momentjs`了。
![$i 示例](/img/console/$i.gif "$i 示例")

# copy(...)

可以通过全局的方法`copy()`在`console`里`copy`任何你能拿到的资源到我们的粘贴板里，包括我们在上一篇中提及的那些变量。例如 `copy($_)` 或者 `copy($0)`
![copy 示例](/img/console/copy.gif "copy 示例")

# Store as global

如果你在`console`中打印了一坨数据(例如 你在app中计算出来的一个数组),然后你想对这些数据做一些额外的工作，
例如使用我们刚刚提到的`copy`，你可以很容易的将它变成一个全局变量
只需要右击它，并且选择“Store as global variable” (保存为全局变量)这个选项.
第一次使用的话，它会创建一个名为 `temp1` 的变量，然后是 `temp2` 等等.所以你可以操作各样的数据而不用担心会复写(译注:修改他们原有的值)他们
![Store 示例](/img/console/store.gif "Store 示例")

# 保存(Stack trace)堆栈跟踪

对你来说，堆栈跟踪会很有用，但是对于你的同事来说也是这样。(例如 : 与其他的前端开发者合作，或者是QA (测试) 工程师).你可以直接把堆栈跟踪保存为一个文件而不是截个图。
![trace 示例](/img/console/trace.gif "trace 示例")

# Copy HTML (最快的方式)

你可能知道右击或者点击在`html`元素边上的省略号(...)就可以将它`copy`到你的操作系统的剪贴板中

但是你同样可以用非常古老的`[ctrl] + [c]`大法来实现同样的效果！

# console

> `console`本来就是默认被 `async`包裹住的，我们可以直接使用`awit`

![console 示例](/img/console/console.gif "console 示例")

## 异步 console

* `Storage` 系统的 占用数 和 空闲数

```js
  await navigator.storage.estimate()
```
![estimate 示例](/img/console/estimate.png "estimate 示例")

* 你的设备的电池信息

![battery 示例](/img/console/battery.png "battery 示例")

* 媒体能力

![media 示例](/img/console/media.png "media 示例")

* Cache storage keys

> [Cache storage keys](https://developer.mozilla.org/en-US/docs/Web/API/CacheStorage) 一般用来对 `request` 和 `response` 进行缓存

![cache 示例](/img/console/cache.png "cache 示例")

更多的例子不胜枚举。能够在 `console` 中轻松的获取 `promise` 包裹的值绝对是相当方便的。

## logs加上时间戳

如果你想要给你的应用中发生的事件加上一个确切的时间记录，开启 `timestamps`.你可以在设置(在调试工具中的 `⋮` 下拉中找到它，或者按下 `F1` )中来开启或者使用`Commands Menu`
![logstime 示例](/img/console/logstime.gif "logstime 示例")

## 监测执行时间

相对于在所有事上展示一个时间戳，你可能对脚本中的特殊的点之间的时间跨度更加感兴趣
对于这样的情况，我们可以采用一对有效的 `console` 方法

* `console.time()` — 开启一个计时器
* `console.timeEnd()` — 结束计时并且将结果在 `console` 中打印出来

如果你想一次记录多件事，往这些方法中传入不同的标签值。(例如: `console.time('loading')`, `console.timeEnd('loading')`)
![timeend 示例](/img/console/timeend.gif "timeend 示例")


## console.assert

```js
  console.assert(assertion, obj1 [, obj2, ..., objN])
  console.assert(assertion, msg [, subst1, ..., substN])
```
当我们传入的第一个参数为假时，`console.assert`打印跟在这个参数后面的值。当你需要在特殊情况下打印一些信息的时候这很有用 - 现在你可以在不使用`if`表达式的情况下做到这件事情。另外，你还可以白白得到一个堆栈的跟踪信息 ；-）
请注意，如果你使用的 `NodeJS` 版本 `≤ 10.0` ,那可能会中断后面代码的执行，但是在`. 10`的版本中被修复了(当然，在浏览器中使用是完全安全的)
![assert 示例](/img/console/assert.gif "assert 示例")

## console.table

如果你有一个数组(或者是类数组的对象，或者就是一个对象)，你可以使用`console.table`方法将它以一个漂亮的表格的形式打印出来。它不仅仅会根据数组中包含的对象的所有属性，去计算出表中的列名，而且这些列都是可以缩放，甚至...可以排序！😳
当列太多的时候，使用第二个参数，传入你想要展示的列对应的名字
![table 示例](/img/console/table.gif "table 示例")

## console.dir

最受欢迎`console.log`方法将数据以格式化的形式打印出来，在大多数的情况下这确实很有用。但是有时候那可能不是你想看到的 - 最典型的例子就是你想要打印一个`DOM`节点.`console.log`会将这个交互式的元素渲染成像是从`Elements`中剪切出来的一样.如果说你想要查看这个节点所关联到的真实的`js`对象呢？并且想要查看他的属性等等？
在那样的情况下，如果你需要更加直接的表现形式来展示你的数据，就可以使用`console.dir`。
![dir 示例](/img/console/dir.gif "dir 示例")

## console.log

你(在代码中)打印了一个对象，然后(在代码中)修改了它，然后再将他打印出来 - 然后在console中查看，你就会看到第一条打印的记录(在进行修改前的记录) 和第二条打印的记录的值是一样的！

## 根据调用堆栈自动缩进

```js
function log(message) {
  console.log('  '.repeat(new Error().stack.match(/\n/g).length - 2) + message)
}
function foo() {
  log('foo')
  return bar() + bar();
}
function bar() {
  log('bar')
  return baz() + baz()
}
function baz() {
  log('baz')
  return 12;
}
// 运行
foo();
// 输出结果
/*
  foo
    bar
      baz
      baz
    bar
      baz
      baz
  48
*/
```

## 输出增加样式

```js
// %c 样式占位
// %s 字体串占位
// %d 表示数字
// %i 表示整型数字
// %o 表示DOM元素
// %O 表示对象
console.log('%c日志输出', 'color:green; font-size: 16px;')
```

# 快捷键

## 通过'h'来隐藏

你可以通过简单的按一下'h'来隐藏你在元素面板中选择的元素。再次按下'h'可以使它出现。这在某些的时候是很有用的，例如你想截图，但是你又不想里面包含一些敏感信息的情况。
![h 示例](/img/console/h.gif "h 示例")

## ctrl + c/v/x 去移到或者复制粘贴
## ctrl + shift + P 切换命令菜单
## ctrl + shift + D

`ctrl + shift + D` 控制 `DevTools窗口` 在当前停靠的位置与上次停靠的位置之间切换

## ctrl + [/]/1-9

* 按`ctrl + [`和`ctrl + ]`分别从当前活动面板向左和向右切换面板
* 按`ctrl + 1`到`ctrl + 9`转到面板编号1..9（所以`ctrl + 1`到`Elements`，`ctrl + 4`到`网络`等）

> 注意！默认情况下，`ctrl + 1-9`处于停用状态。请转到`DevTools`的`Setting » Preferences » Appearance`并打开选项 `Enable Ctrl + 1-9 shortcut to switch panels`：

## 增加/减少

这对于样式部分最有用：通过使用带或不带修饰键的上/下箭头，您可以轻松地将任何数值增加或减少0.1,1或10：

* `↑` 增加1
* `↓` 减少1
* `Alt + ↑` 增加0.1
* `Alt + ↓` 减少0.1
* `Shift + ↑` 增加10
* `Shift + ↓` 减少10
* `Ctrl + ↑` 增加100
* `Ctrl + ↓` 减少100


# 拖动 & 放置元素 或者使用control!(按钮)

当你想要看看你页面的一部分在DOM树的不同部分是如何显示的时候，只需要拖动放置它(到指定的位置)，就像在你机器上任何其他地方一样 :-)
![drag 示例](/img/console/drag.gif "drag 示例")
如果你只是想移动你当前选中的元素，在DOM结构中往上一点或者往下一点，而不是拖动和放置，你同样可以使用`[ctrl] + [⬆]` / `[ctrl] + [⬇]` (`[⌘] + [⬆]` /`[⌘] + [⬇]` on Mac).
![ctrl 示例](/img/console/ctrl.gif "ctrl 示例")

从某一点来看，我们可以拖动，放置，编辑，复制(当然，以及使用 `[ctrl]+[v]` 来粘贴), 所以当我们发现我们可以在元素面板里把我们的HTML结构搞得一团糟，这并不奇怪。在任意一个编辑器中都有一个标准：
使用`[ctrl] + [z]` (`[⌘] + [z]` on Mac)撤销我们的任何改动。
使用`[ctrl] + [shift] + [z]`重新编辑我们的任何修改。

# Command 

* 在 `Chrome` 的调试打开的情况下 按下 `[Ctrl]+[Shift]+[P]` (or `[⌘]+[Shift]+[P]` on Mac)
* 或者使用 `DevTools` 的 `dropdown` 按钮的这个选项:
![Command 示例](/img/console/command.jpg "Command 示例")

你可以看到命令的输入和一系列的可供你选择的命令，按照我在下图所选择的类型被分组排列。
![Command 示例](/img/console/actions.png "Command 示例")

## 截屏

如果你想对一个特别的 `DOM` 节点进行截图，选中那个节点，打开 `Command` 菜单并且寻找节点截图的命令。
更有用的是什么呢，你同样可以用这种方式全屏截图 - 使用 `Capture full size screenshot`。请注意，我们说的是全屏，并不是嵌入页面的一部分。我记得这可是得使用浏览器插件才能做到的！
![截屏 示例](/img/console/cut.gif "截屏 示例")

## 快速切换面板

`DevTools` 的部分使用双面板布局（例如：元素或者资源面板）经常将它们以适合阅读的方式展示出来，根据屏幕可用的部分，将它们横向或者纵向的排列。有时候这个布局却并不是你喜欢的。
（你是否重置过`DevTools`呢？将样式面板将其从html预览的底部移动到右边或者其他的周围位置呢？是的，这就是我所说的😉）
打开 `Commands` 菜单并且输入`layout`,你会看到 2 到3个可供选择的项(当前你已经激活的选项不会在这里显示)：

* 使用横向面板布局
* 使用纵向面板布局
* 使用自动面板布局

选择你需要的
![layout 示例](/img/console/layout.gif "layout 示例")

## 快速切换主题

你是否突然开始讨厌强光，并且不能忍受一直看着白光闪闪的屏幕呢？或者你一直都在黑暗的模式下工作，突然太阳出来了，照在你的 `DevTools` 上所以你什么都看不到？
在`Commands`菜单中寻找与 `theme` 相关的选项，以实现在明亮&暗黑两种主题之间的切换。
![theme 示例](/img/console/theme.gif "theme 示例")

## Esc

打开控制台后，按`Esc`后出现另外的控制台，再次按[esc]隐藏它
要查看完整列表，请单击放置控制台面板选项卡之前的菜单图标（⋮）。或者，您也可以使用“命令”菜单并键入“Drawer”：

> 详情：`Animations`, `Changes`, `Console`, `Coverage`, `Network conditions`, `Performance monitor`, `Quick source`, `Remote devices`, `Rendering`, `Request blocking`, `Search`, `Sensors`, `What’s new`

# Color picker

![colorpicker 示例](/img/console/colorpicker.gif "colorpicker 示例")

## 选择你正在用的颜色

下面是颜色选择器的一部分，包括你可以自定义的调色板。你可以：

* 切换到一个有色调变化的 `Material` 调色板
* 自定义，可以添加和删除颜色
* 从 [CSS Variables](https://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_variables) 中选择一个你当前页面使用的样式表中存在的颜色。
* 或者所有你在页面的 `CSS` 中使用的颜色
![正在用的颜色 示例](/img/console/currentcolor.png "正在用的颜色 示例")

## 直观的选择颜色

当为了文本打开调色选择器(这个 color css 属性 但布施 background-color) 你会看到**“Contrast ratio(对比度)”**部分。它展示了文本和它（开发者工具）认为这段文本的背景颜色的对比度。如果这个数字很高，你的文本相对于背景来说有合适的可见性，如果对比度接近 1 ,这段文本几乎不能从背景中区分出来。

* 在数字边上的“🚫” 意味着不好（译者注:对比度太低）

* 一个“✅” 意味着这个颜色遵从[Web Content Accessibility Guidelines (WCAG) 2.0](https://www.w3.org/TR/UNDERSTANDING-WCAG20/conformance.html)的 AA 声明，这意味着对比度至少为 3,

* 你同样可以拥有“✅ ✅” ，那意味着满足了 AAA 声明。

![customcolor 示例](/img/console/customcolor.gif "customcolor 示例")

你可以通过点击那个记号来阅读更多关于 [accessibility rules](https://developers.google.com/web/fundamentals/accessibility/accessible-styles#color_and_contrast) 中颜色的说明。
如果你继续探索 `对比度` 的部分，你会看到额外的信息，并且可能选择一个不同的颜色作为背景颜色来进行对比，当然 `color spectrum(色谱)` 部分会显示一个对比的边界：如果你的背景颜色比这条线上面的颜色更深，兼容 `AA`，如果背景颜色比这条线上面的更亮，你需要在这条线的下面选择一个颜色。


# 自定义格式化console

`DevTools` 控制台格式化对象的默认方式对大多数情况都很有用，但有时会不满足需求。这里我们就需要`自定义格式化程序` 来自定义我们输出的对象要显示的格式，在编写之前，我们需要在`DevTools`的`设置`中打开相应的设置（在`DevTools`的`⋮`下拉列表或按`F1`键）：
![format 示例](/img/console/format.png "format 示例")

那么我们如何使用`格式化程序`呢？它是一个拥有三个方法的对象

* `header` -- 处理在控制台中显示日志的主要部分
* `hasBody` -- 如果要展开对象的▶箭头，则返回`true`
* `body` -- 定义将在展开的部分中显示的内容

让我们来看一个基本的自定义格式化程序：
```js
window.devtoolsFormatters = [{
  header: function(obj) {
    return ['div', {}, `${JSON.stringify(obj, null, 7)}`]
  },
  hasBody: function() {
    return false;
  }
}]
```
这个`header`函数返回的是一个[JsonML](http://www.jsonml.org/)数组，它包含：

1. `html`标签名称
2. `style`属性对象
3. 内容（文本值或其他元素）

> 如果这看起来很熟悉，可能是因为你熟悉`React`开发

这个格式化程序示例只是为对象的每个嵌套级别打印7个空格的缩进，因此输出现在看起来像这样：
![format1 示例](/img/console/format1.gif "format1 示例")

来一个实际的例子，我只想格式化特定的对象，并加一些样式修饰
```js
window.devtoolsFormatters = [{
  header: function(obj) {
    if (!obj.__clown) return null;
    delete obj.__clown
    const style = `
      color: red;
      border: dotted 2px gray;
      border-radius: 4px;
      padding: 5px;
    `
    const content = `😁 ${JSON.stringify(obj, null, 2)}`
    try {
      return ['div', { style }, content]
    } catch(e) {
      return null
    }
  },
  hasBody: function() {
    return false;
  }
}]

console.clown = function(obj) {
  console.log({
    ...obj,
    __clown: true
  })
}

// 普通打印
console.log({ message: 'hello!' })
// 装饰打印
console.clown({ message: 'hello!' })

```
![clown 示例](/img/console/clown.png "clown 示例")


# Shadow编辑

这只是一个小小部件，类似于颜色选择器，但更简单。您可以通过单击“样式”面板中`box-shadow`或`text-shadow`属性附近的方形阴影符号来打开它：
![shadow 示例](/img/console/shadow.gif "shadow 示例")

# Timing function编辑器

也称为`Cubic bezier`编辑器。贝塞尔曲线用于定义动画持续时间内css动画速度的变化情况。我们将其用于CSS属性`transition-timing-function`或`animation-timing-function`的值。
与颜色选择器和阴影编辑器一样，只需单击上述属性旁边的曲线符号（或`transition`, `animation`：如果未在动画里使用，则不会出现符号）
![Timing 示例](/img/console/timing.gif "Timing 示例")

# 插入样式按钮

当您将鼠标光标悬停在样式区域的末尾附近时，您将看到一些可以快速添加`Color`和`Shadow`编辑器的CSS属性的按钮：

* text-shadow
* box-shadow
* color
* background-color

![insert 示例](/img/console/insert.gif "insert 示例")

# 断点调试

有时候，当你设置断点时，它们会被循环太多次：想象一个循环超过200个元素，你只对第110次迭代中的那个元素感兴趣，或者只在某些其他特殊情况（例如值的组合）出现时感兴趣。
在这种情况下，您可以设置条件断点

* 右键单击行号，然后选择“添加条件断点...”选项
* 或右键单击已设置的断点并选择“编辑断点...”
* 然后输入任何计算结果为`truthy`或`falsy`值的表达式（它不需要是true / false）

在该表达式中，您可以访问代码中此处可用的所有内容，即此行的范围。
现在断点将仅在满足条件时暂停代码。
![pauseif 示例](/img/console/pauseif.gif "pauseif 示例")

## 高级进阶
由于条件断点，您可以开始使用一种灵巧的技术

* 每次应用程序到达该行时，提供的条件必须检查 - 即运行
* 如果该条件返回一个假的（例如未定义的）值，它将不会暂停

鉴于这些特性，我们可以插入 `console.`的所有方法

因此，不用在源代码的多个位置添加`console.log / console.table / console.time`等，而只需使用条件断点在`Sources`面板中“附加”它们。它们不会停止，但它们会运行，当你不再需要它们时，你在断点列表，你可以通过两次点击鼠标将它们全部删除。他们会像一堆忍者一样消失！
![debuggerconsole 示例](/img/console/debuggerconsole.gif "debuggerconsole 示例")

## DOM断点

某些脚本会更改DOM，但你不确定什么时间什么事件，这里可以添加DOM断点，可以侦听添加子元素，或者删除元素，或者修改属性
![DOM breakpoint 示例](/img/console/breakpoint.png "breakpoint 示例")
断点会被记录在面板`DOM Breakpoints`里面，在元素里面有小圆点标示。

另外还有js版的DOM侦听，支持IE11以上浏览器

```js
const observer = new MutationObserver((mutationsList, obsercer) => {
  console.log(mutationsList, obsercer)
})
const ul = document.querySelector('ul')
observer.observe(ul, {
  attributes: true,
  childList: true,
  subtree: true
})
```

## XHR/fetch断点

如果要捕获`ajax`请求，就要使用Sources面板中的`XHR/fetch breakpoints`，可以添加部分URL作为触发器，为空就是监听所有请求
![XHR/fetch 示例](/img/console/xrhfetch.png "XHR/fetch 示例")

# queryObjects 方法

假设我们创建了几个基于某个类的实例对象，但是在某个时刻，某个执行环境内我们想要知道还存在有几个实例，这个时间 `DevTools ` 中的 `queryObjects` 就起到他应有的作用了

```js
class Person {
  constructor(name, role) {
    this.name = name;
    this.role = role;
  }
}
const = new Person('john', 'dad')

let kids = [
  new Person('Mary', 'kid'),
  new Person('Luke', 'kid')
]

new  Person('Lucius', 'uncle')

console.log('这会还剩下多少人呢？')
// 用到我们的方法了，在控制台内使用：
// queryObjects(Person)

```

注意了，上图中创建的最后一个对象是不可用的，因为对这个对象的引用没有保留在任何地方 - 在执行代码完之后，就只有3个Person对象：

![queryObjects 示例](/img/console/queryObjects.gif "queryObjects 示例")

# monitor 方法

`monitor`是一个`DevTools`函数，它允许你“监视”任何函数的调用：每当一个“间谍”函数运行时，控制台将记录这个事件，包括函数的名称和调用它的参数。

来看下下面这个示例，将会基于`Person` 扩展出两个实例对象

```js
class Person {
  constructor(name, role) {
    this.name = name;
    this.role = role;
  }
  
  greet() {
    return this.getMessage('greeting');
  }
  getMessage(type) {
    if (type === 'greeting') {
      return `Hello, I'm ${this.name}!`;
    }
  }
}
```

示例中，`greet`将会用调用`getMessage`用特定的参数，让我们来看下`monitor`方法怎么跟踪`getMessage`

![monitor 示例](/img/console/monitor.gif "monitor 示例")

```js
  // 在控制台内使用：
  var john = new Person('john');
  var mary = new Person('mary');

  monitor(john.getMessage);

  john.greet();
```

# monitorEvents

我们可以使用 `monitorEvents` 监听元素的事件

```js
  // $0 参考文章前面说明
  monitorEvents($0, 'click')
  // 然后再点击元素时，控制台会打印点击事件的event事件对象
```


# workspace

1. 在`Chrome`里面编辑你的源文件，`Source --> Filesystem`，就可以在`Sources`面板中编辑`HTML`和`JavaScript`，然后`ctrl + s`保存到文件系统中。
2. 工作区可以将您实时调试的样式保存到你的样式文件内。
3. 如果要向现有选择器添加新样式，则很容易：只需在`Elements`面板的`Styles`部分中找到该选择器，然后开始编写css。但是如果还没有这样的选择器，则需要按下`+ New Style Rule`按钮。但是，当您使用工作区时，新样式规则的默认位置 - “inspector-stylesheet：1”位置 - 不是您希望添加的位置。在这种情况下，只需按住`+ New Style Rule`按钮，会列出所有css文件的选项。可以选择要保存到的文件！
4. 在`workspace`内更改的css样式会立即被浏览器渲染的，不用刷新。

# Snippets

1. 在`Chrome`里面增加代码片断，`Source --> Snippets`，增加一个常用代码片断，方便下次使用。可以使用右键`Run`或者`ctrl + enter`来运行
2. 一旦设置了一组很棒的片段，您甚至不必访问`Sources`来运行它们。最快捷的方法是使用`Command Menu`（`ctrl + p`）。如果你输入`!`，您可以按名称选择一个片段：

# network

1. `View：`后面两个图标用途，第一个大列表模式，第二个，是否显示`OverView`时序预览图
2. 请求列表`Initiator`可以查看调用堆栈，方便追踪XHR调用位置
3. 过滤请求地址的时候，可以加列表列名称，如：`Method:GET`，`status-code:200`等，要查看更多的关键字列表，请在空输入上按`[ctrl] + [space]`（快捷键冲突请自行解决）
4. 自主义请求列表列显示，要自定义显示哪些列，只需右键单击请求表标题上的任意位置

# coverage

要获得一个更好的摘要 - 以及冗余代码的详细信息，请使用`Coverage`面板。要打开它，请使用`Drawer`菜单，或者更快，使用`Command Menu`：
`DevTools`的`Coverage`工具可以跟踪当前加载的JS和CSS文件的哪些行已执行过，并显示未使用字节的百分比。它还标记以绿色运行的线条和以红色运行的线条。
![coverage 示例](/img/console/coverage.gif "coverage 示例")

# changes

在浏览器中进行设计，调整这些css值，使用代码进行评估 - 这既方便又有趣。但在某些时候，您可能希望将已更改的内容与最初加载的样式表进行比较。为此，请使用驻留在`DevTools`中的`Changes`工具。
它不仅会向您展示使用像Git这样的源控制工具熟悉的差异形式的变化，还可以撤消它们。
![changes 示例](/img/console/changes.gif "changes 示例")

# live expressions

`DevTools`在`Console`面板有一个非常漂亮的附加功能，这是一个名为`Live expression`的工具。只需按下眼睛符号，您就可以在那里定义任何`JavaScript`表达式。它会不断更新，所以表达的结果将永远执行着，您还可以同时定义多个：
![expressions 示例](/img/console/expressions.gif "expressions 示例")

# ctrl + F

`elements, logs, sources & network`都支持搜索

参考：
* [你不知道的Chrome调试工具技巧](https://juejin.im/post/5c09a80151882521c81168a2)