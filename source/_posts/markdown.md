---
title: MarkDown
date: 2017-09-9 12:12:12
tags: [markdown]
categories: [文档留存]
---

MarkDown常用语法的备份，方便忘记的时候过来查找。。。

<!-- more --> 
## 标题设置
> 让字体变大，和word的标题意思一样

在Markdown当中设置标题，有两种方式：
* 第一种：通过在文字下方添加“=”和“-”，他们分别表示一级标题和二级标题。
* 第二种：在文字开头加上 “#”，通过“#”数量表示几级标题。（一共只有1~6级标题，1级标题字体最大）

```
This is an H1
=============
This is an H2
-------------
# 这是 H1 #
## 这是 H2 ##
### 这是 H3 ######
```
## 块注释（blockquote）
> 通过在文字开头添加“>”表示块注释。

>     当>和文字之间添加五个blank时，块注释的文字会有变化。（例子：当前行）

```
> This is a blockquote with two paragraphs. Lorem ipsum dolor sit amet,
> consectetuer adipiscing elit. Aliquam hendrerit mi posuere lectus.
> Vestibulum enim wisi, viverra nec, fringilla in, laoreet vitae, risus.
> 
> Donec sit amet nisl. Aliquam semper ipsum sit amet velit. Suspendisse
> id sem consectetuer libero luctus adipiscing.

> This is a blockquote with two paragraphs. Lorem ipsum dolor sit amet,
consectetuer adipiscing elit. Aliquam hendrerit mi posuere lectus.
Vestibulum enim wisi, viverra nec, fringilla in, laoreet vitae, risus.

> Donec sit amet nisl. Aliquam semper ipsum sit amet velit. Suspendisse
id sem consectetuer libero luctus adipiscing.

#注释内写markdown其它语法
> This is the first level of quoting.
>
> > This is nested blockquote.
>
> Back to the first level.

> ## 这是一个标题。
> 
> 1.   这是第一行列表项。
> 2.   这是第二行列表项。
> 
> 给出一些例子代码：
> 
>     return shell_exec("echo $input | $markdown_script");
```

## 斜体
将需要设置为斜体的文字两端使用1个“*”或者“_”夹起来

> *我是斜体*
> _我是斜体_

```
*我是斜体*
_我是斜体_
```

## 粗体
将需要设置为斜体的文字两端使用2个“*”或者“_”夹起来

>**我是粗体**
>__我是粗体__

```
**我是粗体**
__我是粗体__
```

>_**我是斜粗体**_
>*__我是斜粗体__*

```
_**我是斜粗体**_
*__我是斜粗体__*
```

## 删除线

> ~~文字删除线~~

```
~~文字删除线~~
```

## 无序列表

在文字开头添加(\*, +, or -)实现无序列表。但是要注意在(\*, +, or -)和文字之间需要添加空格。（建议：一个文档中只是用一种无序列表的表示方式）

>*   Red
>*   Green
>*   Blue

>+   Red
>+   Green
>+   Blue

>- Red
>- Green
>  + Green
>- Blue
>  + Blue

>*   Lorem ipsum dolor sit amet, consectetuer adipiscing elit.
    Aliquam hendrerit mi posuere lectus. Vestibulum enim wisi,
    viverra nec, fringilla in, laoreet vitae, risus.
>*   Donec sit amet nisl. Aliquam semper ipsum sit amet velit.
    Suspendisse id sem consectetuer libero luctus adipiscing.

>*   Lorem ipsum dolor sit amet, consectetuer adipiscing elit.
Aliquam hendrerit mi posuere lectus. Vestibulum enim wisi,
viverra nec, fringilla in, laoreet vitae, risus.
>*   Donec sit amet nisl. Aliquam semper ipsum sit amet velit.
Suspendisse id sem consectetuer libero luctus adipiscing.

```
*   Red
*   Green
*   Blue

+   Red
+   Green
+   Blue

- Red
- Green
  + Green
- Blue
  + Blue

*   Lorem ipsum dolor sit amet, consectetuer adipiscing elit.
    Aliquam hendrerit mi posuere lectus. Vestibulum enim wisi,
    viverra nec, fringilla in, laoreet vitae, risus.
*   Donec sit amet nisl. Aliquam semper ipsum sit amet velit.
    Suspendisse id sem consectetuer libero luctus adipiscing.

*   Lorem ipsum dolor sit amet, consectetuer adipiscing elit.
Aliquam hendrerit mi posuere lectus. Vestibulum enim wisi,
viverra nec, fringilla in, laoreet vitae, risus.
>*   Donec sit amet nisl. Aliquam semper ipsum sit amet velit.
Suspendisse id sem consectetuer libero luctus adipiscing.
```

## 有序列表
使用数字后面跟上句号。（还要有空格）

>1.  Bird
>2.  McHale
>3.  Parish

><ol>
><li>Bird</li>
><li>McHale</li>
><li>Parish</li>
></ol>

>1.  Bird
>1.  McHale
>1.  Parish

>3. Bird
>1. McHale
>8. Parish

```
1.  Bird
2.  McHale
3.  Parish

<ol>
<li>Bird</li>
<li>McHale</li>
<li>Parish</li>
</ol>

1.  Bird
1.  McHale
1.  Parish

3. Bird
1. McHale
8. Parish
```

## 表格

```
| 居左 | 居中 | 居右 |
| -----|:----:| ----:|
| a    | b    | c    |
| d    | e    |  f   |
| g    | h    |   i  |

居左 | 居左 | 居左
-----|------|----
a    | b    | c
d    | e    | f
g    | h    | i
```

效果：

| 居左 | 居中 | 居右 |
| -----|:----:| ----:|
| a    | b    | c    |
| d    | e    |  f   |
| g    | h    |   i  |

居左 | 居左 | 居左
-----|------|----
a    | b    | c
d    | e    | f
g    | h    | i


## 链接（Links）
Markdown中有两种方式，实现链接，分别为内联方式和引用方式。
链接内容定义的形式为：

方括号（前面可以选择性地加上至多三个空格来缩进），里面输入链接文字
接着一个冒号
接着一个以上的空格或制表符
接着链接的网址
选择性地接着 title 内容，可以用单引号、双引号或是括弧包着

>内联方式：
>This is an [example link](http://example.com/).
>This is [an example](http://example.com/ "Title") inline link.

>自动链接：
><http://example.com/>
><address@example.com>

>引用方式：
>I get 10 times more traffic from [Google][1] than from [Yahoo][2] or [MSN][3]. 

>[1]: http://google.com/        "Google" 
>[2]: http://search.yahoo.com/  "Yahoo Search" 
>[3]: http://search.msn.com/    "MSN Search"
 
```
内联方式：
This is an [example link](http://example.com/).
This is [an example](http://example.com/ "Title") inline link.

自动链接：
<http://example.com/>
<address@example.com>

引用方式：
I get 10 times more traffic from [Google][1] than from [Yahoo][2] or [MSN][3]. 

[1]: http://google.com/        "Google" 
[2]: http://search.yahoo.com/  "Yahoo Search" 
[3]: http://search.msn.com/    "MSN Search"
```

## 图片（Images）
图片的处理方式和链接的处理方式，非常的类似。
>内联方式：![猫和狗](/img/4.jpg "猫和狗")
>引用方式：![猫狗][猫狗] 
          ![猫][猫] 

[猫狗]: /img/4.jpg "猫和狗"
[猫]: /img/3.1.jpg "猫"

```
![alt text](/path/to/img.jpg "Title") #内联方式
![alt text][猫] #引用方式

以下为引用地址：
[猫狗]: /img/4.jpg "猫和狗"
[猫]: /img/3.1.jpg "猫"
```

## 代码（HTML中所谓的Code）
实现方式有两种：
第一种：行内简单文字出现一个代码框。使用\`代码块\`。例子：`不是单引号而是左上角的ESC下面~中的`
第二种：大片文字需要实现代码框。连续三个\`，在片断开头（如果要指定代码片断的语言，在片断开头的后面紧跟上语言类型`js/css/html/bash/...`），结尾各三个。如下：

```
` ` `js
var a = 1;
// 请忽略空格
` ` `
```


## 脚注（footnote）
实现方式如下：
> hello[^hello]
> [^hello]:注释的内容
```
hello[^hello]
[^hello]:注释的内容
```

## 下划线
在空白行下方添加三条“-”横线。（前面讲过在文字下方添加“-”，实现的2级标题）

* * *
云从龙
***
虎生风
*****
老鼠会打洞
- - -
好

---------------------------------------

```
* * *
云从龙
***
虎生风
*****
老鼠会打洞
- - -
好

---------------------------------------
```

## 代码块与段落
这是一个普通段落：

    这是一个代码区块。

Here is an example of AppleScript:

    tell application "Foo"
        beep
    end tell
    <div class="footer">
        &copy; 2004 Foo Corporation
    </div>
```
这是一个普通段落：

    这是一个代码区块。

Here is an example of AppleScript:

    tell application "Foo"
        beep
    end tell
    <div class="footer">
        &copy; 2004 Foo Corporation
    </div>
```
## 反斜杠
Markdown 可以利用反斜杠来插入一些在语法中有其它意义的符号，例如：如果你想要用星号加在文字旁边的方式来做出强调效果（但不用 \<em> 标签），你可以在星号的前面加上反斜杠：
>\*literal asterisks\*

```
\*literal asterisks\*
```
>Markdown 支持以下这些符号前面加上反斜杠来帮助插入普通的符号：
>\\   反斜线
>\`   反引号
>\*   星号
>\_   底线
>\{}  花括号
>\[]  方括号
>\()  括弧
>\#   井字号
>\+   加号
>\-   减号
>\.   英文句点
>\!   惊叹号

```
\\   反斜线
\`   反引号
\*   星号
\_   底线
\{}  花括号
\[]  方括号
\()  括弧
\#   井字号
\+   加号
\-   减号
\.   英文句点
\!   惊叹号
```