---
title: Chrome中console的debugger技巧
date: 2018-12-12 20:35:53
tags: [javascript, debugger, console, chrome]
categories: [读书笔记, 备忘]
---


汇总关于Chrome中高度技巧
<!-- more --> 

# $0

在`Chrome`的`Elements`面板中，`$0`关联到当前我们选中的`html`节点。

理所当然，`$1` 是我们上一次选择的节点，`$2`是在那之前选择的节点，等等。一直到 `$4`

你可以用以上的补充参考来尝试一些相关的操作(例子: `$1.appendChild($0)`)

# $ 和 $$

```js
  $ == document.querySelector;
  Array.from(document.querySelectorAll('div')) === $$('div');
```

`$$` 能节省更多的时间，因为它不仅仅执行`document.querySelectorAll`并且返回的是一个节点的数组，而不是一个`Node list`

# $_

`$_` 变量是关联在`console`里面上次执行的结果

# $i

在`Chrome`插件:[Console Importer](https://chrome.google.com/webstore/detail/console-importer/hgajpakhafplebkdljleajgbpdmplhie/related)的帮助之下，你可以快速的在`console`中引入和把玩一些`npm`库。
直接运行例如 `$i('lodash')` 或者 `$i('moment')` 然后在几秒钟之后，你就可以获取到`lodash` / `momentjs`了。
