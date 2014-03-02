# HTTP
Node.js 提供了 **`http`** 模块来进行HTTP服务端与客户端操作。为了满足各种不同的需要，Node的HTTP模块API被设计得很低级，只是过行流式处理和消息的解析，而且解析的结果基本是未作修改的原生态。

   

### HTTP服务端
当使用**http**模块构建服务端对外提供服务时，使用 **` http.createServer() `** 方法创建一个Server的实例。   
先看下官网示例:   
```javascript
  var http = require('http');
  http.createServer(function (req, res) {
    res.writeHead(200, {'Content-Type': 'text/plain'});
    res.end('Hello World\n');
  }).listen(1337, '127.0.0.1');
```   

首先require **http** 模块是必须的。因为**http**模块不是所有应用都需要的。   
其中，` http.createServer() ` 方法参数是一个函数，这个函数有两个参数:   
   
   1. req < IncomingMessage < Stream.Readable
     这是一个IncomingMessage类的实例，其包括了从客户端传来的数据。能获取到的数据如:   
        
     ```javascript
       req.httpVersion           // http协议的版本号，一般为 1.1
       req.headers               // 请求头信息，包括 use-agent、accept、accept-encoding等一些信息。
       req.url                   // 请求的URL
       req.method                // 请求的方法，常见的为GET,POST,UPLOAD,DELETE等。
       req.socket                // 一个Socket对象的实例。其有一个别名:req.connection
       req.socket.remoteAddress  // 客户端IP地址
       req.socket.remotePort     // 客户端端口号
       ...
     ```   
   2. res < ServerResponse < OutgoingMessage < Stream
     这是一个ServerResponse类的实例，它包含了要返回给客户端的信息:   
        
     ```javascript
       res.socket       // res.socket === res.connection === req.socket === req.connection
       res.writeHead(statusCode, [headInfo])  // 返回给客户端的状态码及头部信息。别名: writeHeader
       res.statusCode   // 当不显式使用writeHead时，可以这样返回状态码。
       res.setHeader(name, value)   // 按键值对的形式设置返回头信息，可多次调用设置多个。
       res.headersSent  // 只读，返回头部是否发送
       res.write(chunk, [encoding])  // 写入返回主体信息，可以调用多次。如果没设置encoding, chunk为buffer。
       res.end([data], [encoding])  // 调用时表示返回头和主体都写完毕了。其还可以携带最后一次需要写的主体信息。
       ...
     ```   
   
从上面可以看出，**req**和**res**共用一个***socket***实例。关于**Socket**详见[net.Socket](./net.md#socket)。
   
**http.createServer** 返回一个**Server**对象实例。关于**Server**详见[net.Server](./net.md#server)。
   

### HTTP客户端
当使用**http**模块构建并模拟浏览器行为时，即作为客户端。其中主要是使用**` http.request() `**方法。示例:   
```javascript
    var options = {
      hostname: 'dmdgeeker.com',
      port: 80,
      path: '/index',
      method: 'GET'
    };

    var req = http.request(options, function(res) {
      console.log('STATUS: ' + res.statusCode);
      console.log('HEADERS: ' + JSON.stringify(res.headers));
      res.setEncoding('utf8');
      res.on('data', function (chunk) {
        console.log('BODY: ' + chunk);
      });
    });

    req.on('error', function(e) {
      console.log('problem with request: ' + e.message);
    });

    // write data to request body
    req.write('data\n');
    req.write('data\n');
    req.end();
```   
**!!!** 如果没有添加回调或者对响应的监听，其会丢弃所有收到的响应。如果添加了处理回调，你必须处理所受到的响应，否则响应数据会一直处于内存之中，直到发生**` process out of memory `**错误终止。
   
  1. req < ClientRequest < OutgoingMessage < Stream   
    req对象是一个**ClientRequest**的实例，它可以监听到` http.request `触发的事件，发送请求主体等。   

    ```javascript
      http.request()    // 第一个参数描述了请求的信息；第二个参数是一个接受到响应后的回调，非必需。
      <E: response>     // req可以监听到**response**事件，因而可以使用`on('response')`方法来处理响应。!!!**response**事件仅触发一次。
      <E: connect>      // **connect**事件，当使用**CONNECT**方法请求时触发。
      <E: upgrade>      // **upgrade**事件，当使用**UPGRADE**方法请求时触发。
      <E: continue>     // **continue**事件，当服务器发送**100 Continue**时触发。
      <E: socket>       // **socket**事件，当request请求分配到一个**socket**时触发。
      req.write()       // 发送请求体
      req.end()         // 请求发送结束
      ...
    ```   
  2. res < IncomingMessage < Stream.Readable   
    res是当服务器响应请求时，用来获取响应的状态，头部和主体等信息。与作为HTTP服务端的**req**用法类似。   

    ```javascript
      <E: data>         // 当服务器发送响应数据到来时触发
      <E: error>        // 当出现错误时触发
    ```
   
作为客户端常见的应用场景有: 做爬虫抓取数据、作中间代理等。   
! ` http.get() `方法是使用了默认参数的` http.request() `而已。   
   
   
---
   
### 实例


