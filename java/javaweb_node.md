### Ajax跨域问题

​	ajax出现请求跨域错误问题， 主要原因就是因为浏览器的“**同源策略**”， 

- 有三种方法规避这个限制。
  1. JSONP
  2. WebSocket
  3. CORS

#### JSONP

​	JSONP是服务器与客户端跨源通信的常用方法。最大特点就是简单适用，老式浏览器全部支持，服务器改造非常小。

​	它的基本思想是，网页通过添加一个`<script>`元素，向服务器请求JSON数据，这种做法不受同源政策限制；服务器收到请求后，将数据放在一个指定名字的回调函数里传回来。

> 首先，网页动态插入`<script>`元素，由它向跨源网址发出请求。

```javascript
function addScriptTag(src) {
  var script = document.createElement('script');
  script.setAttribute("type","text/javascript");
  script.src = src;
  document.body.appendChild(script);
}

window.onload = function () {
  addScriptTag('http://example.com/ip?callback=foo');
}

function foo(data) {
  console.log('Your public IP address is: ' + data.ip);
};
```

#### WebSocket

​	WebSocket是一种通信协议，使用`ws://`（非加密）和`wss://`（加密）作为协议前缀。该协议不实行同源政策，只要服务器支持，就可以通过它进行跨源通信。

>  浏览器发出的WebSocket请求的头信息

```javascript
GET /chat HTTP/1.1
Host: server.example.com
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: x3JJHMbDL1EzLkh9GBhXDw==
Sec-WebSocket-Protocol: chat, superchat
Sec-WebSocket-Version: 13
Origin: http://example.com
```

​	正是因为有了`Origin`这个字段，所以WebSocket才没有实行同源政策。因为服务器可以根据这个字段，判断是否许可本次通信。如果该域名在白名单内，服务器就会做出如下回应。

```javascript
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: HSmrc0sMlYUkAGmm5OPpG2HaGWk=
Sec-WebSocket-Protocol: chat
```

#### CORS

​	CORS是跨源资源分享（Cross-Origin Resource Sharing）的缩写。它是W3C标准，是跨源AJAX请求的根本解决方法。相比JSONP只能发`GET`请求，CORS允许任何类型的请求。

##### 两种请求

​	浏览器将CORS请求分成两类：简单请求（simple request）和非简单请求（not-so-simple request）。

1. 简单请求

   请求方法是以下三种方法之一：

   - HEAD
   - GET
   - POST

   （2）HTTP的头信息不超出以下几种字段：

   - Accept
   - Accept-Language
   - Content-Language
   - Last-Event-ID
   - Content-Type：只限于三个值`application/x-www-form-urlencoded`、`multipart/form-data`、`text/plain`

2. 非简单请求

#### 同源策略

- 所谓同源指的是三个相同

  1. 协议相同
  2. 域名相同
  3. 端口相同

- 同源策略的目的

  为了保证用户信息的安全，防止恶意的网站窃取数据。

- 同源的限制范围

  1. Cookie、 LocalStorage 和 IndexDB 无法读取。
  2. DOM 无法获得。
  3. AJAX 请求不能发送。

  > 虽然这些限制是必要的， 但是有时候很不方便， 合理的用途也受到影响，如何规避上面三种限制

##### Cookie

​	Cookie 是服务器写入浏览器的一小段信息， 只有同源的网页才能共享， 但是， **两个网页一级域名相同， 只是二级域名不同， 浏览器允许通过设置 document.domain 共享 Cookie。**

> A 网页    http://w1.example.com/a.html
>
> B 网页    http://w2.example.com/a.html
>
> 一级域名相同， 二级域名不同

服务器可以在设置 Cookie 的时候， 指定 Cookie 的所属域名为一级域名， 比如 .example.com 。

``` java
set-Cooke: key=value; domain=.example.com; path=/
```

这样二级域名和三级域名不用做任何设置， 都可以读取这个 Cookie。

> 这种方法适用于 Cookie 与 iframe

##### iframe

- 如果是完全不同源的网站， 目前有三种方法， 可以解决跨域窗口的通信问题。
  1. 片段识别符 (fragment identifier)
  2. window.name
  3. 跨文档通信API (Cross-document messaging)

###### 片段识别符

​	指的是， URL的#号后面的部分

> http://example.com/x.html#fragment

​	\#fragmen就是片段识别符

> 父窗口可以把信息， 写入子窗口的片段标识符。

```javascript
var src = originURL + '#' + data;
document.getElementById('myIFrame').src = src;
```

> 子窗口通过监听 hashchange 事件得到通知

```javascript
window.onhashchange = checkMessage;
function checkMessage() {
    var message = window.location.hash;
}
```

> 同样的， 子窗口也可以改变父窗口的片段标识符。

```javascript
parent.location.href = target + "#" + hash;
```

###### window.name

​	浏览器窗口 window.name 属性。 这个属性最大的特点是， 无论是否同源， 只要在同一个窗口里， 前一个网页设置了这个属性， 后一个网页可以读取它。

> 父窗口先打开一个子窗口， 载入一个不同源的网页， 该网页将信息写入**window.name** 属性。

```javascript
window.name = data;
```

> 接着， 子窗口跳回一个与主窗口同域的网址

```javascript
location = 'http://parent.url.com/xxx.html';
```

> 然后， 主窗口就可以读取子窗口的 window.name 了

```javascript
var data = document.getElementById('myFrame').contentWindow.name;
```

- 优点是window.name容量很大
- 缺点是必须监听子窗口window.name 属性变化， 影响网页性能。

###### window.postMessage

​	上面两种方法都属于破解， HTML5 为了解决这个问题， 引入了一个全新的API ： 跨文档通信API （Cross-document-messaging）。

> 父窗口 http://aaa.com
>
> 子窗口 http://bbb.com

父窗口向子窗口发消息

```javascript
var popup = window.open('http://bbb.com', 'title');
popup.postMessage('Hello World!', 'http://bbb.com');
```

​	`postMessage`方法的第一个参数是具体的信息内容，第二个参数是接收消息的窗口的源（origin），即"协议 + 域名 + 端口"。也可以设为`*`，表示不限制域名，向所有窗口发送。

子窗口向父窗口发送消息

```javascript
window.opener.postMessage('Nice to see you', 'http://aaa.com');
```

父窗口和子窗口都可以通过 message 事件， 监听对方的消息。

```javascript
window.addEventListener('message', function(e) {
    console.log(e.data);
}, false);
```

message事件的事件对象event, 提供了三个属性

1. event.sourcee 发送消息的窗口
2. event.origin 消息发向的网址
3. event.data 消息内容