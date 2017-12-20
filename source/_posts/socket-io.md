---
title: socket.io实践
date: 2017-11-02 15:39:28
tags: [nodejs, js, api,libs]
categories: [文档留存, 实例讲介]
---

[Socket.IO](https://github.com/socketio/socket.io) 实现了基于事件的实时双向通信。
可以工作在任意平台、浏览器或设备，专注于可靠性和速度。
配方旨在记录作者的实践过程，以及备注一些常用的api使用方法。[示例](https://github.com/freeser/example/tree/master/socket.io.chat)
<!-- more --> 

## 后台服务

我们使用了基于 Node.JS 的 web 框架 `express` 。 请确保安装了 `[Node.JS](https://nodejs.org/)`。
后台依赖如下：

{% codeblock lang:js %}
"dependencies": {
    "express": "4.13.4",
    "socket.io": "^2.0.4"
}
{% endcodeblock %}

要保存 `dependencies` 信息， 可以用 `npm install --save express`;
然后新建一个 `index.js` 文件来创建应用.

{% codeblock lang:js %}
var express = require('express');
var app = express();
var path = require('path');
var server = require('http').createServer(app);

var port = process.env.PORT || 3000;

server.listen(port, function () {
  console.log('Server listening at port %d', port);
});

// Routing
app.use(express.static(path.join(__dirname, 'client')));
{% endcodeblock %}

接下来就是主角登场了。。。

{% codeblock lang:js %}
var io = require('socket.io')(server);
{% endcodeblock %}

我们通过传入 `http` （HTTP 服务器） 对象初始化了 `socket.io` 的一个实例。 然后监听 `connection` 事件来接收 `sockets`， 并将连接信息打印到控制台。当然有连接当然就有挂断了。

{% codeblock lang:js %}
//监听客户端连接,回调函数会传递本次连接的socket
io.on('connection', function(socket){
  console.log('a connected');
  socket.on('disconnect', function(){
    console.log('disconnected');
  });
});
{% endcodeblock %}

`connection` 事件很重要，以后所有的事件回送都建立在连接成功的基础上。
Socket.IO 的核心理念就是允许发送、接收任意事件和任意数据。任意能被编码为 `JSON` 的对象都可以用于传输。二进制数据 也是支持的。

接下来的目标就是让服务器将消息发送给其他用户。
要将事件发送给每个用户，Socket.IO 提供了 io.emit 方法：

{% codeblock lang:js %}
io.emit('some event', { for: 'everyone' }); 
//可以是一个字符串、json对象，也可以是多个参数对象
{% endcodeblock %}

要将消息发给除特定 socket 外的其他用户，可以用 broadcast 标志：

{% codeblock lang:js %}
io.on('connection', function(socket){
  socket.broadcast.emit('hi'); //参数对象同上个例子
});
{% endcodeblock %}

## 前端页面

前端页面要做的就很简单了；
在 index.html 的 `</body>` 标签中添加如下内容：

{% codeblock lang:html %}
<script src="https://cdnjs.cloudflare.com/ajax/libs/socket.io/2.0.4/socket.io.js"></script>
<script>
  var socket = io(); 
  // 等价于 io('http://localhost')
</script>
{% endcodeblock %}

ES6、AMD加载方法如下：

{% codeblock lang:js %}
const io = require('socket.io-client');
// or with import syntax
import io from 'socket.io-client';
{% endcodeblock %}

这样就加载了 `socket.io-client`。 `socket.io-client` 暴露了一个 `io` 全局变量，然后连接服务器。
请注意我们在调用 `io()` 时没有指定任何 URL，因为它默认将尝试连接到提供当前页面的主机。

现在前后端已经联通，node控制台将会出现如下信息：
```
a connected
disconnected
a connected
disconnected
a connected
disconnected
```
多次刷新会出现上面的信息。

现在前端页面与后台的交互方法如下：

{% codeblock lang:js %}
$(function () {
    var socket = io();
    $('form').submit(function(){
        // 向后台传输数据，可以是一个字符串、json对象，也可以是多个参数对象
        socket.emit('chat message', $('#m').val());
    });
    // 监听后台传输过来的数据
    socket.on('chat message', function(msg){
      $('#messages').append($('<li>').text(msg));
    });
});
{% endcodeblock %}

就这样就实现了与后台的数据聊天交互。

## 进阶实例

目标我想实现一对一的交流、或者只跟后台的交互，不要跟其它用户交流。
思路，只有一个人的群聊？后台知道前台的唯一id？
首先第个页面要有个唯一id，那先用地址栏传参数吧。

{% codeblock lang:js %}
function getParam(name, href) {
    name = name.replace(/[\[]/, "\\\[").replace(/[\]]/, "\\\]");
    var regexS = "[\\?&]" + name + "=([^&#]*)";
    var regex = new RegExp(regexS);
    var results = regex.exec(href || window.location.href);
    if (results == null)
        return "";
    else
        return results[1];
}
{% endcodeblock %}

使用 `connect` 事件监听是否连接成功

{% codeblock lang:js %}
console.log(socket.id); // undefined
socket.on('connect', () => {
    console.log(socket.id); // 'G5p5...'此时连接已经成功
});
{% endcodeblock %}

发现有 `socket.id` 了就用不着地址栏传参数了吧
不过传参数也是一种方法，前台监听这个id，后台emit这个id

### 与后台交流

{% codeblock lang:js %}
// 后台server.js
io.on('connection', function (socket) {
  var socketId = socket.id;
  socket.on('order', function (message) {
    var msg = '回执：' + JSON.stringify(message);
    // io.sockets.sockets[socketId].emit('clientMsg', msg); 给指定的socketId发送消息，适用于在其它connect里面
    socket.emit('clientMsg', msg);
  });
});

// 前台index.html
var socket = io();
socket.on('connect', () => {
    log('连接成功：' + socket.id);
});

//接收消息
socket.on('clientMsg', function (data) {
    log(data);
    console.log('clientMsg', data);
});

//断开连接（系统方法disconnct不可更改为其它）
socket.on('disconnect', function () {
    log('连接断开!');
});
{% endcodeblock %}

### 群聊

1、方法一，借助socket,设置groupid

{% codeblock lang:js %}
// 后台server.js
socket.on('order', function (message, groupId) {
    var msg = socket.userName + ': ' + '回执：' + JSON.stringify(message);
    //推送给群组其它人，不包括自己
    socket.to(socket.groupId).broadcast.emit('group.emit', msg);
    //推送给群组所有人，包括自己
    //io.sockets.in(socket.groupId).emit('group.emit', msg); //方法1.2
});
// 加入群组
socket.on('group', function (groupId) {
    socket.groupId = groupId;
    socket.userName = userName;
    socket.join(groupId); //加入群组
    //推送给群组其它人，不包括自己
    socket.to(socket.groupId).broadcast.emit('group.emit', userName + '加入群组'+ groupId +'成功');
    //推送给群组所有人，包括自己
    // io.sockets.in(groupId).emit('group.emit', userName + '加入群组'+ groupId +'成功');
});

// 前台index.html
var socket = io();
var groupId = getParam('group');
var userName = getParam('user');
socket.on('connect', () => {
    // 加入群组
    groupId && socket.emit('group', groupId, userName);
});

//群组消息
socket.on('group.emit', function (data) {
    console.log('group.emit', data);
});
{% endcodeblock %}

2、方法二，借助io.of('/room1')

{% codeblock lang:js %}
// 后台server.js
io.of('/room1').on('connection', function (socket) {
  socket.on('order', function (message) {
    var msg = socket.userName + ': ' + '回执：' + JSON.stringify(message);
    socket.broadcast.emit('group.emit', msg);
  });
  // 加入群组
  socket.on('join', function (userName) {
    socket.userName = userName;
    socket.broadcast.emit('group.emit', userName + '加入群组room1成功');
  });
});

// 前台group.html
var socket = io.connect('http://localhost:3000/room1');
var userName = getParam('user');
socket.on('connect', () => {
    log('连接成功：' + socket.id);
    // 加入群组
    userName && socket.emit('join', userName);
});
socket.on('group.emit',function(data){
   console.log(data);
})
{% endcodeblock %}

但是这个方法有个限制，只能后台控制群组名称，不能前台自定义设置（个人粗浅理解，如有错误还请指正）

## Socket.IO API

### 服务端API

{% codeblock lang:js %}
var path = require('path');
var server = require('http').createServer(app);
var io = require('socket.io')(server);

// 监听客户端连接,回调函数会传递本次连接的socket
io.on('connection',function(socket){
    //socketid = socket.id
    //其实这个id可以做为roomid，默认一个connection生成的socket可以做为
});

// 给所有socket的客户端广播消息包括自己
io.sockets.emit('String',data);

// 给指定的客户端发送消息
io.sockets.sockets[socketid].emit('String', data);

// 监听客户端发送过来的信息
socket.on('String',function(data){

});

// 给该socket的客户端发送消息包括自己
socket.emit('String', data);

// 给该socket的客户端发消息不包括自己
socket.broadcast.emit('String', data);

// 推送给群组其它人，不包括自己
socket.to(socket.groupId).broadcast.emit('String', data);

// 推送给群组所有人，包括自己
io.sockets.in(socket.groupId).emit('String', data);

// 离开群组
socket.leave(room, fn);

// 离开所加入的所有群组
socket.leaveAll();

{% endcodeblock %}

另外，Socket.IO提供了4个配置的API：io.configure, io.set, io.enable, io.disable。
其中io.set对单项进行设置，io.enable和io.disable用于单项设置布尔型的配置。
io.configure 可以让你对不同的生产环境（如devlopment，test等等）配置不同的参数。

#### new Server
1、new Server(httpServer[, options])
* httpServer (http.Server) 需要绑定的 `server`.
* options (Object)
  * path (String): 要捕获的路径的名称 (`/socket.io`)
  * serveClient (Boolean): 是否为客户端文件提供服务 (`true`)
  * adapter (Adapter): 要使用的适配器。默认为带有套接字的适配器，是基于内存的
  * origins (String):  允许的域名，默认：`*`
  * parser (Parser):  要使用的解析器，要与前端设置的一样.

{% codeblock lang:js %}
const io = require('socket.io')();
// or
const Server = require('socket.io');
const io = new Server();
{% endcodeblock %}

2、new Server(port[, options])
3、new Server(options)

#### server.sockets

默认命名空间为： `/`


#### server.serveClient

server.serveClient([value])
* `value` *(Boolean)*
* **Returns** `Server|Boolean`

如果值为`true`，连接的服务器将为客户端文件提供服务，此方法在调用`attach`后无效. 
如果未提供参数, 此方法将返回当前值。

{% codeblock lang:js %}
// 设置`server`的 `serveClient` 选项
const io = require('socket.io')(http, { serveClient: false });

// 或不传递任何`server`, 然后您可以调用该方法
const io = require('socket.io')();
io.serveClient(false);
io.attach(http);
{% endcodeblock %}

#### server.path

server.path([value])
* `value` *(Boolean)*
* **Returns** `Server|Boolean`

个人理解主要是命名空间与前台配合，相当于分组

{% codeblock lang:js %}
const io = require('socket.io')();
io.path('/myownpath');

// 前端html
const socket = io({
  path: '/myownpath'
});
{% endcodeblock %}

#### server.adapter([value])

#### server.origins

1、[value], string
设置允许的域名，默认所有的都允许(`*`)。
2、fn
提供一个回调方法包括两个参数，`origin:String` 和 `callback(error, success)`，
返回一个布尔值，表示是否允许某个源访问

{% codeblock lang:js %}
io.origins((origin, callback) => {
  if (origin !== 'https://foo.example.com') {
    return callback('origin not allowed', false);
  }
  callback(null, true);
});
{% endcodeblock %}

#### server.attach
1、server.attach(httpServer[, options])
2、server.attach(port[, options])
#### server.listen
1、server.listen(httpServer[, options])
2、server.listen(port[, options])

#### server.of(nsp)

初始化并查找一个路径的命名空间；
如果命名空间已经初始化, 它将立即返回。

{% codeblock lang:js %}
const adminNamespace = io.of('/admin');
{% endcodeblock %}

#### server.close

参数：`[callback]`
关闭一个`socket.io`的服务，当服务关闭的时候回调方法将会被调用。

{% codeblock lang:js %}
const Server = require('socket.io');
const PORT   = 3030;
const server = require('http').Server();

const io = Server(PORT);

io.close(); // Close current server

server.listen(PORT); // PORT is free to use

io = Server(server);
{% endcodeblock %}

#### namespace.to(room)

与 `namespace.in(room)` 等价
只推送给客户端加入的房间
要发给多个房间，你可以多次调用 `to`

{% codeblock lang:js %}
const io = require('socket.io')();
const adminNamespace = io.of('/admin');

adminNamespace.to('level1').emit('an event', { some: 'data' });
{% endcodeblock %}

#### namespace.emit

namespace.emit(eventName[, ...args])

推送事件到已连接的客户端。两个示例：

{% codeblock lang:js %}
const io = require('socket.io')();
io.emit('an event sent to all connected clients'); // main namespace

const chat = io.of('/chat');
chat.emit('an event sent to all connected clients in chat namespace');
{% endcodeblock %}

#### namespace.clients(callback)

得到已连接到当前命名空间的客户端 IDs

{% codeblock lang:js %}
const io = require('socket.io')();
io.of('/chat').clients((error, clients) => {
  if (error) throw error;
  console.log(clients); // => [PZDoMHjiu8PYfRiKAAAF, Anw2LatarvGVVXEIAAAD]
});
{% endcodeblock %}

一个等到当前命名空间下某个房间的所有客户端

{% codeblock lang:js %}
io.of('/chat').in('general').clients((error, clients) => {
  if (error) throw error;
  console.log(clients); // => [Anw2LatarvGVVXEIAAAD]
});
{% endcodeblock %}

与广播一样, 默认的是默认命名空间中的所有客户端

{% codeblock lang:js %}
io.clients((error, clients) => {
  if (error) throw error;
  console.log(clients); // => [6em3d4TJP8Et9EMNAAAA, G5p55dHhGgUnLUctAAAB]
});
{% endcodeblock %}

#### namespace.use(fn)

注册一个中间件，它是为每个传入的 `socket` 执行的函数, 并接收作为参数的`socket`和一个函数, 可以选择延迟执行到下一个注册的中间件。
传递给中间件回调的 `error` 作为特殊的错误数据包发送到客户端。

{% codeblock lang:js %}
io.use((socket, next) => {
  if (socket.request.headers.cookie) return next();
  next(new Error('Authentication error'));
});
{% endcodeblock %}

#### socket.id

会话的唯一标识符，来自底层`Client`。

#### socket.rooms

一个字符串哈希, 用于标识此客户端所位于的文件室, 按房间名称进行索引。
{% codeblock lang:js %}
io.on('connection', (socket) => {
  socket.join('room 237', () => {
    let rooms = Objects.keys(socket.rooms);
    console.log(rooms); // [ <socket.id>, 'room 237' ]
  });
});
{% endcodeblock %}

#### socket.client
#### socket.use
#### socket.send
#### socket.emit
#### socket.on(eventName, listener)
#### socket.once(eventName, listener)
#### socket.removeListener(eventName, listener)
#### socket.join(room[, callback])
#### socket.join(rooms[, callback])
#### socket.leave(room[, callback])
#### socket.to(room)
#### socket.in(room)
#### socket.compress(value)
#### socket.disconnect(close)

### 客户端API

{% codeblock lang %}
客户端`socket.on()`监听的事件：
connect：连接成功, 在连接包括成功重新连接时被触发。
connecting：正在连接
disconnect：断开连接
connect_failed：连接失败
error：错误发生，并且无法被其他事件类型所处理
ping：当 `ping` 数据包写入服务器时触发。
pong：从服务器接收到 `pong` 时被点燃。
reconnect_failed：重连失败
reconnect：成功重连
reconnecting：正在重连当第一次连接时，事件触发顺序为：connecting -> connect；
当失去连接时，事件触发顺序为：
disconnect -> reconnecting（可能进行多次）-> connecting -> reconnect -> connect。
{% endcodeblock %}


#### io

`io([url][, options])`, 始一个 `socket` 实例。
* `url` (String) (默认 `window.location`)
* `options` (Object)
    * `forceNew` (Boolean) 是否重用现有连接
* 返回 `Socket`

#### 初始化示例

##### 多路复用

默认情况下，连接到不同的命名空间时使用单个连接（以最小化资源）：

{% codeblock lang:js %}
const socket = io();
const adminSocket = io('/admin');
// 将建立一个连接
{% endcodeblock %}

可以通过 `forceNew` 选项禁用该行为：

{% codeblock lang:js %}
const socket = io();
const adminSocket = io('/admin', { forceNew: true });
// 将创建两个不同的连接
{% endcodeblock %}

注意：重用同一个命名空间也将创建两个连接

{% codeblock lang:js %}
const socket = io();
const socket2 = io();
// 也将创建两个不同的连接
{% endcodeblock %}

##### 用自定义 `path`

{% codeblock lang:js %}
const socket = io('http://localhost', {
  path: '/myownpath'
});

// 后台服务端
const io = require('socket.io')({
  path: '/myownpath'
});
{% endcodeblock %}

上面示例的请求网址将会是这样： `localhost/myownpath/?EIO=3&transport=polling&sid=<id>`

##### 带有查询参数

{% codeblock lang:js %}
// 前端html 带上参数token
const socket = io('http://localhost?token=abc');

// 后台服务端
const io = require('socket.io')();

// 中间件、路由
io.use((socket, next) => {
  let token = socket.handshake.query.token;
  if (isValid(token)) {
    return next();
  }
  return next(new Error('authentication error'));
});

// 然后后台连接
io.on('connection', (socket) => {
  let token = socket.handshake.query.token;
  // ...
});
{% endcodeblock %}

##### 带查询选项

{% codeblock lang:js %}
// 前端html 带上参数token
const socket = io({
  query: {
    token: 'cde'
  }
});
{% endcodeblock %}

还可以在重新连接时更新查询内容：

{% codeblock lang:js %}
// 前端html
socket.on('reconnect_attempt', () => {
  socket.io.opts.query = {
    token: 'fgh'
  }
});
{% endcodeblock %}

##### 带自定义消息头

这仅在`polling`参数里有效，默认是启用的。
使用websocket传输时不会附加自定义标题。
因为WebSocket握手不符合自定义标头。
（对于后台请参阅[WebSocket协议RFC](https://tools.ietf.org/html/rfc6455#section-4)）

{% codeblock lang:js %}
// 前端html 带上参数token
const socket = io({
  transportOptions: {
    polling: {
      extraHeaders: {
        'x-clientid': 'abc'
      }
    }
  }
});

// server-side
const io = require('socket.io')();

// middleware
io.use((socket, next) => {
  let clientId = socket.handshake.headers['x-clientid'];
  if (isValid(clientId)) {
    return next();
  }
  return next(new Error('authentication error'));
});
{% endcodeblock %}

##### 使用 `websocket` 传输

默认情况下，首先建立长轮询连接，然后升级为“更好”的传输（如WebSocket）。对传输要求不太高，可以跳过这个部分：

{% codeblock lang:js %}
// 前端html
const socket = io({
  transports: ['websocket']
});

// 在 `reconnection` 重连时可以重置 `transports` 选项, 如：`websocket`、 `polling`
// 连接时有可能会失败 (原因可能是 跨域问题, 防火墙, 浏览器, 等)
socket.on('reconnect_attempt', () => {
  socket.io.opts.transports = ['polling', 'websocket'];
});
{% endcodeblock %}

##### 用自定义解析器

默认的[解析器](https://github.com/socketio/socket.io-parser)（支持Blob，File，二进制）传输时会有性能牺牲。
但可以提供自定义解析器来满足应用程序的需求。请看这里的例子。

{% codeblock lang:js %}
// 前端html
const parser = require('socket.io-msgpack-parser'); // or require('socket.io-json-parser')
const socket = io({
  parser: parser
});

// 后台必需用相同的解析器才能连通
const io = require('socket.io')({
  parser: parser
});
{% endcodeblock %}

#### Socket

##### socket.id 

`socket.id` 为20位随机string，`socket`实例的唯一标识符，在触发连接事件后设置, 并在重新连接事件之后更新。

{% codeblock lang:js %}
// 前端html
const socket = io('http://localhost');

console.log(socket.id); // undefined

socket.on('connect', () => {
  console.log(socket.id); // 'G5p5...'
});
{% endcodeblock %}

##### socket.open()

同 `socket.connect()`返回 `Socket`, 手动打开与后台的连接。

{% codeblock lang:js %}
// 前端html
const socket = io({
  autoConnect: false
});

// ...
socket.open();
{% endcodeblock %}

它也可以用于手动重新连接：

{% codeblock lang:js %}
// 前端html
socket.on('disconnect', () => {
  socket.open();
});
{% endcodeblock %}

##### socket.emit

向后台发送消息
socket.emit(eventName [，... args] [，ack]),
socket.send([... args] [，ack])二者等价

{% codeblock lang:js %}
socket.emit('hello', 'world');
socket.emit('with-binary', 1, '2', { 3: '4', 5: new Buffer(6) });
{% endcodeblock %}

该 `ack` 参数是可选的，用于服务器回调。

{% codeblock lang:js %}
socket.emit('ferret', 'tobi', (data) => {
  console.log(data); // 将打印 'woot'
});

// server:
//  io.on('connection', (socket) => {
//    socket.on('ferret', (name, fn) => {
//      fn('woot');
//    });
//  });
{% endcodeblock %}

##### socket.on

注册事件监听, 不能放在connect里面，否则在联通重联时会多次注册
socket.on(eventName，callback)

{% codeblock lang:js %}
socket.on('news', (data) => {
  console.log(data);
});

// with multiple arguments
socket.on('news', (arg1, arg2, arg3, arg4) => {
  // ...
});
// with callback
socket.on('news', (cb) => {
  cb(0);
});
{% endcodeblock %}

类似的方法 `off` 解除绑定与 `on` 相反，其它 `once` 绑定后仅可以调用一次就失效， `hasListeners` 暂未知道其用意

##### socket.compress

为后续事件的释放设置一个修饰符, 仅当该值为 true 时, 事件数据才会被压缩，在不调用 `socket.compress` 方法时, 默认为 true。

{% codeblock lang:js %}
socket.emit('an event', { some: 'data' }); // 等价于 socket.compress(true).emit('', '')
socket.compress(false).emit('an event', { some: 'data' });
{% endcodeblock %}

##### socket.close

手动关闭 `socket` 实例连接，等价于 `socket.disconnect()`

> 不太擅长写作，组织有些混乱。。。费了老大劲。。。