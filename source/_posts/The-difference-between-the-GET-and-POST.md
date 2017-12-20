---
title: GET与POST的差异
date: 2017-11-10 10:40:11
tags: [js, http, ajax]
categories: [收集整理, 摘抄备份]
---

`GET`和`POST`是`HTTP`请求的两种基本方法（`GET`, `POST`, `PUT`, `DELETE`），最直观的区别就是`GET`把参数包含在`URL`中，`POST`通过`request body`传递参数。

<!-- more --> 

## 异同点

* `GET`在浏览器回退时是无害的，而`POST`会再次提交请求。
* `GET`产生的`URL`地址可以被`Bookmark`，而`POST`不可以。
* `GET`请求会被浏览器主动`cache`，而`POST`不会，除非手动设置。
* `GET`请求只能进行`URL`编码，而`POST`支持多种编码方式。
* `GET`请求参数会被完整保留在浏览器历史记录里，而`POST`中的参数不会被保留。
* `GET`请求在`URL`中传送的参数是有长度限制的，而`POST`么有。
* 对参数的数据类型，`GET`只接受`ASCII`字符，而`POST`没有限制。
* `GET`比`POST`更不安全，因为参数直接暴露在`URL`上，所以不能用来传递敏感信息。
* `GET`参数通过`URL`传递，`POST`放在`Request body`中。

### HTTP请求

Http定义了与服务器交互的不同方法，最基本的方法有4种，分别是GET，POST，PUT，DELETE  

URL全称是资源描述符，我们可以这样认为：一个URL地址，它用于描述一个网络上的资源，而HTTP中的GET，POST，PUT，DELETE就对应着对这个资源的查 ，改 ，增 ，删 4个操作。
到这里，大家应该有个大概的了解了，GET一般用于获取/查询 资源信息，而POST一般用于更新 资源信息(个人认为这是GET和POST的本质区别，也是协议设计者的本意，其它区别都是具体表现形式的差异 )。

为了理解两者在传输过程中的不同，我们先看一下HTTP协议的格式：  

#### HTTP请求
```
<request line>  
<headers>  
<blank line>  
[<request-body>]
```

在HTTP请求中，第一行必须是一个请求行（`request line`），用来说明请求类型、要访问的资源以及使用的HTTP版本。
紧接着是一个首部（`header`）小节，用来说明服务器要使用的附加信息。
在首部之后是一个空行，再此之后可以添加任意的其他数据[称之为主体（`body`）]。

GET与POST请求方法实例：
```
GET /books/?sex=man&name=Professional HTTP/1.1  
Host: www.wrox.com  
User-Agent: Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US; rv:1.7.6)  
Gecko/20050225 Firefox/1.0.1  
Connection: Keep-Alive  

POST / HTTP/1.1  
Host: www.wrox.com  
User-Agent: Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US; rv:1.7.6)  
Gecko/20050225 Firefox/1.0.1  
Content-Type: application/x-www-form-urlencoded  
Content-Length: 40  
Connection: Keep-Alive  
  （----此处空一行----）  
name=Professional%20Ajax&publisher=Wiley  
```

#### HTTP响应
```
<status line>  
<headers>  
<blank line>  
[<request-body>]
```
在响应中唯一真正的区别在于第一行中用状态信息代替了请求信息。状态行（status line）通过提供一个状态码来说明所请求的资源情况。

```
HTTP/1.1 200 OK  
Date: Sat, 31 Dec 2005 23:59:59 GMT  
Content-Type: text/html;charset=ISO-8859-1  
Content-Length: 122

<html>  
<head>  
<title>Wrox Homepage</title>  
</head>  
<body>  
<!-- body goes here -->  
</body>  
</html>  
```

### 实例对比

有了以上对HTTP请求的了解和示例，我们再来看两种提交方式的区别：

**(1) 数据位置**

  **GET提交**：请求的数据会附在URL之后（就是把数据放置在HTTP协议头中），以?分割URL和传输数据，多个参数用&连接；
  例如：`login.action?name=hyddd&password=idontknow&verify=%E4%BD%A0 %E5%A5%BD`。
  如果数据是英文字母/数字，原样发送，如果是空格，转换为+，如果是中文/其他字符，则直接把字符串用BASE64加密，得出如： `%E4%BD%A0%E5%A5%BD`，其中`％XX`中的`XX`为该符号以16进制表示的`ASCII`。  

  **POST提交**：把提交的数据放置在是HTTP包的包体中。上文示例中红色字体标明的就是实际的传输数据  

  因此，GET提交的数据会在地址栏中显示出来，而POST提交，地址栏不会改变  

**(2) 传输数据的大小：**

  首先声明：HTTP协议没有对传输的数据大小进行限制，HTTP协议规范也没有对URL长度进行限制。  

  而在实际开发中存在的限制主要有：  

  **GET:** 特定浏览器和服务器对URL长度有限制，例如IE对URL长度的限制是2083字节(2K+35)。对于其他浏览器，如`Netscape、FireFox`等，理论上没有长度限制，其限制取决于操作系统的支持。因此对于GET提交时，传输数据就会受到URL长度的限制。  

  **POST:** 由于不是通过URL传值，理论上数据不受限。但实际各个WEB服务器会规定对post提交数据大小进行限制，`Apache、IIS6`都有各自的配置。  

**(3) 安全性：**

  POST的安全性要比GET的安全性高。
  注意：这里所说的安全性和上面GET提到的“安全”不是同个概念。上面“安全”的含义仅仅是不作数据修改，而这里安全的含义是真正的Security的含义，比如：通过GET提交数据，用户名和密码将明文出现在URL上，因为
  * 登录页面有可能被浏览器缓存 
  * 其他人查看浏览器的历史纪录，那么别人就可以拿到你的账号和密码了
  除此之外，使用GET提交数据还可能会造成`Cross-site request forgery`攻击  

**(4) Http get, post, soap协议都是在http上运行的**  

  **1）get**：请求参数是作为一个`key/value`对的序列（查询字符串）附加到URL上的；查询字符串的长度受到web浏览器和web服务器的限制（如IE最多支持2048个字符），不适合传输大型数据集同时，它很不安全  
  **2）post**：请求参数是在http标题的一个不同部分（名为`entity body`）传输的，这一部分用来传输表单信息，因此必须将`Content-type`设置为:`application/x-www-form-urlencoded`。post设计用来支持web窗体上的用户字段，其参数也是作为`key/value`对传输。但是：它不支持复杂数据类型，因为post没有定义传输数据结构的语义和规则。  
  **3）soap**：是`http post`的一个专用版本，遵循一种特殊的xml消息格式。`Content-type`设置为:` text/xml` 任何数据都可以xml化  

### 其它

关于『GET 发一个包，POST 发两个包』

对于GET方式的请求，浏览器会把http header和data一并发送出去，服务器响应200（返回数据）；
~~而对于POST，浏览器先发送header，服务器响应100 continue，浏览器再发送data，服务器响应200 ok（返回数据）。~~

测试结果如下：

* Chrome 55.0.2883.95，two packets: **YES**
* Safari 10.0.2, two packets: **YES**
* Firefox 49.0, two packets: **NO**

不论哪一种浏览器，在发送 `POST` 的时候都没有带 `Expect` 头，`server` 也自然不会发 `100 continue`。通过抓包发现，尽管会分两次，`body` 就是紧随在 `header` 后面发送的，根本不存在『等待服务器响应』这一说。

也就是说，『`XHR` 发送两个 `TCP packets`』是关于 `implementation` 的知识，而不是关于 `specification` 的知识。你不能说『Chrome 在 AJAX POST 的时候会发两个 TCP packets，GET 只会发一个』是 GET 和 POST 的区别，正如你不能因为北京 PM 2.5 经常爆表就说国家关于工业废气排放的标准有问题。

从另一个角度说，TCP 是传输层协议。别人问你应用层协议里的 GET 和 POST 有啥区别，你确回答说这俩在传输层上发送数据的时候不一样。

## Http请求方法

http协议中的请求方法是请求一定的Web页面的程序或用于特定的URL。可选用下列几种：

* GET： 请求指定的页面信息，并返回实体主体。
* HEAD： 当客户端向Web服务器请求一个资源的首部信息，而不是资源的全部信息的时候使用。
* POST： 当客户端向服务端发送信息或者数据的时候使用--例如，表单提交（向Web服务器发送大量的复杂的数据）。
* PUT： 从客户端向服务器传送的数据取代指定的文档的内容。
* DELETE： 请求服务器删除指定的页面。
* OPTIONS： 允许客户端查看服务器的性能。
* TRACE： 请求服务器在响应中的实体主体部分返回所得到的内容。
* PATCH： 实体中包含一个表，表中说明与该URI所表示的原内容的区别。
* MOVE： 请求服务器将指定的页面移至另一个网络地址。
* COPY： 请求服务器将指定的页面拷贝至另一个网络地址。
* LINK： 请求服务器建立链接关系。
* UNLINK： 断开链接关系。
* WRAPPED： 允许客户端发送经过封装的请求。
* Extension-mothed：在不改动协议的前提下，可增加另外的方法。

标准Http协议支持六种请求方法，即：

* GET
* HEAD
* PUT
* DELETE
* POST
* OPTIONS

但其实我们大部分情况下只用到了GET和POST。如果想设计一个符合RESTful规范的web应用程序，则这六种方法都会用到。不过即使暂时不想涉及REST，了解这六种方法的本质仍然是很有作用的。大家将会发现，原来web也是很简洁明了的。下面依次说明这六种方法。

### GET

Get方法是最简单最常用的请求方法。它被用来访问静态资源，比如HTML文档和图片等。Get请求可以通过在请求URL中包含查询参数的方法来检索动态信息。
例如，我们可以通过URL发送“name”这个参数(http://www.domain.com?name=Harsh)，在这个例子中，Harsh是通过在请求URL中包含参数“name”的动态的信息。Web服务可以通过这个“name”参数来访问动态信息。

### HEAD

根据维基百科“要求响应上等同于Get请求，但是没有响应Body（Response Body）。这在检索写在响应头(Response Header)中的信息而不需要传输全部的内容的情况下非常有用。”当客户端请求关于一个资源的一些信息而不是请求全部资源的时候使用Head方法。这意味着我们有一种快速的方式来检查针对服务端上一个给定的资源的头信息和一些服务端信息，检测一个给定的url是否可用、给定的文件是否存在等等。有时客户端仅仅需要查看相应的头信息（Content-Type或者Content-Length）。在此类案例中客户端可以使用HEAD请求方法来检测头信息。HEAD方法与GET方法类似，但是HEAD方法不希望在响应信息中包含Message Body(实际页面)。

`HEAD`和`GET`本质是一样的，区别在于`HEAD`不含有呈现数据，而仅仅是`HTTP`头信息。有的人可能觉得这个方法没什么用，其实不是这样的。想象一个业务情景：欲判断某个资源是否存在，我们通常使用`GET`，但这里用`HEAD`则意义更加明确。

### PUT

Put方法存储一个实体到指定的请求URI。该实体是Web服务端上指定请求URI下的资源。如果请求URI没有指向一个存在的资源，也能被发出请求的用户定义为一个新的资源，Web服务端将使用该URI创建资源。如果一个已经存在的资源被修改，200（OK）或者204（No Content）响应代码将被发送来表示成功修改资源。如果一个新的资源被创建，Web服务端必须向用户返回201(Created)相应代码。如果指定请求URI没有创建或者修改资源，将产生一个适当的错误响应，以反应问题的性质。

这个方法比较少见。`HTML`表单也不支持这个。本质上来讲， `PUT`和`POST`极为相似，都是向服务器发送数据，但它们之间有一个重要区别，`PUT`通常指定了资源的存放位置，而`POST`则没有，`POST`的数据存放位置由服务器自己决定。

举个例子：如一个用于提交博文的`URL`: `/addBlog`。
如果用`PUT`，则提交的`URL`会是像这样的”`/addBlog/abc123`”，其中`abc123`就是这个博文的地址。
而如果用`POST`，则这个地址会在提交后由服务器告知客户端。目前大部分博客都是这样的。
显然，`PUT`和`POST`用途是不一样的。具体用哪个还取决于当前的业务场景。

### DELETE

Delete方法通过请求URI请求Web服务删除指定资源。该方法可能在Web服务端被人为的重写。如果响应信息中包含一个实体描述删除的状态，200（OK）相应代码指出资源已经被成功删除。如果响应代码是202（Accepted）,它指出资源还没有被删除。相似的，如果相应代码是204（No Content），它指出资源已经被删除但是响应代码不包含任何实体。

### POST

Post方法一般用来访问动态资源，或者在向服务端发送大量的复杂信息的时候使用。Web服务器接收封装在Request中的实体，作为请求URI唯一标识的资源的附属。根据维基百科“提交数据被处理到唯一标识资源。数据被包含在请求的Body(Request Body)中。这可能会导致创建一个新的资源或者更新已有的资源或者两者兼之。”Get方法和Post方法的主要区别是：在Get方法请求中，请求参数是附加在请求URL最后来传输的，而在Post方法中，请求参数是被包含在请求体（Request Body）中传输的。

Post请求方法提供了如下功能：
（1）为存在的资源提供注释。
（2）发布信息给公告牌、新闻组、邮件列表或者一组类似的文章。
（3）提供一块数据，例如提交Form表单到数据处理单元的结果。
（4）通过附加操作扩展数据库。

### OPTIONS

根据维基百科“返回服务端针对指定的URL所支持的HTTP请求方法。用“*”代替资源名称，想Web服务器发送Options请求，可以测试服务器功能是否正常运作。"Options方法请求关于可请求URI所表示的请求/响应链上的用的通信选项的信息。该方法的响应信息不能被缓存。该方法允许客户端决定选项和/或要求与一种资源或者服务的功能相关联。如果Options方法包含一个实体，则media类型必须通过Content-Type字段来标明。

这个方法很有趣，但极少使用。它用于获取当前URL所支持的方法。若请求成功，则它会在HTTP头中包含一个名为“`Allow`”的头，值是所支持的方法，如“`GET, POST`”。

### TRACE

根据维基百科“回显服务器收到的请求，这样客户端可以看到（如果有）哪一些改变或者添加已经被中间服务器实现。”Trace方法被用来调用与请求消息相关联的远程应用程序层。Trace请求必须包含一个实体。客户端使用Trace方法看到接收用户输入的请求链和诊断或检测信息的另一端。

以上的六种方法，我们可以跟数据库的CRUD增删改查操作对应起来：
CREATE ：PUT
READ：GET
UPDATE：POST
DELETE：DELETE
这样一来就实现了HTTP和数据库操作（其实不光是数据库，任何数据如文件图表都是这样）的完美统一，这也是REST的精髓之一。

## Http状态码

当服务器响应时，其状态行的信息为HTTP的版本号，状态码，及解释状态码的简单说明。现将5类状态码详细列出：

① 客户方错误
100  继续
101  交换协议

② 成功
200  OK
201  已创建
202  接收
203  非认证信息
204  无内容
205  重置内容
206  部分内容

③ 重定向
300  多路选择
301  永久转移
302  暂时转移
303  参见其它
304  未修改（Not Modified）
305  使用代理

④ 客户方错误
400  错误请求（Bad Request）
401  未认证
402  需要付费
403  禁止（Forbidden）
404  未找到（Not Found）
405  方法不允许
406  不接受
407  需要代理认证
408  请求超时
409  冲突
410  失败
411  需要长度
412  条件失败
413  请求实体太大
414  请求URI太长
415  不支持媒体类型

⑤ 服务器错误
500  服务器内部错误
501  未实现（Not Implemented）
502  网关失败
504  网关超时
505  HTTP版本不支持

关于实体头部的内容还可以有：
Last Modified ：请求文档的最近修改时间。
Expires ：请求文档的过期时间。
Connect-length：文档数据的长度。
WWW-authenricate：通知客户端需要的认证信息。
Connect-encoding ：说明有无使用压缩技术。
Transfer-encoding ：说明采用的编码变换类型。
