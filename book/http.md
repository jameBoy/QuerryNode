# HTTP
Node.js 提供了 **`http`** 模块来进行HTTP服务端与客户端操作。为了满足各种不同的需要，Node的HTTP模块API被设计得很低级，只是进行流式处理和消息的解析，而且解析的结果基本是未作修改的原生态。

------

## API

### http.createServer
**createServer([requestListener])**创建一个HTTP服务器。

### http.request
**request(options, [callback])**创建一个HTTP客户端。


### http.get
**get(options, [callback])**创建一个GET请求的客户端。


### http.globalAgent
HTTP客户端全局的代理对象。


### Class: http.Server
事件:   
```
  * request                 有请求到来时触发，每个请求触发一次
  * connection              TCP连接建立时触发
  * close                   当此服务器关闭时触发
  * checkContinue           当收到客户端发送带有Expect: 100-continue请求头时触发
  * connect                 当客户端使用**CONNECT**方式发送请求时触发
  * upgrade                 当客户端使用**UPGRADE**方式发送请求时触发
  * clientError             当客户端链接触发错误事件时触发
```

#### server.listen
  
  1. **listen(port, [hostname], [backlog], [callback])** 监听某一端口
    * port {Number}            监听的端口
    * hostname {String}        监听的主机名或IP
    * backlog {Number}         最大的等待连接数
    * callback {Function}      添加到**listening**事件上的回调函数
  2. **listen(path, [callback])** 监听socket文件
    * path {String}            socket文件路径
    * callback {Function}      添加到**listening**事件上的回调函数
  3. **listen(handle, [callback])** 对特定带有处理方法对象的监听，如server或socket对象。
    * handle {Object}          待处理的对象
    * callback {Function}      添加到**listening**事件上的回调函数

#### server.close
**close([callback])** 停止服务端对新连接的接收


#### server.setTimeout
**setTimeout(msecs, callback)** 设置超时时间及处理函数   
```
  * msecs {Number}        超时时间，默认为2分钟
  * callback {Function}   回调函数，当出现超时时调用
```   

#### server.timeout
**timeout** {Number} 超时时间，默认为120000(2分钟)

#### server.maxHeadersCount
**maxHeadersCount** {Number} 是能接收的最大请求头信息大小


### Class: http.ServerResponse
事件:   
```
  * close                   当连接在res.end()方法调用之前或者是缓冲输出之前被终止了时触发
  * finish                  当请求已经完全发离操作系统时触发，它不代表客户端已经收到信息
```

#### response.writeHead
**writeHead(statusCode, [reasonPhrase], [headers])** 发送响应头给请求。   
参数:   
```
  statusCode {Number}       响应状态码
  reasonPhrase {String}     对响应状态的解释
  headers {Object}          响应头
```   
用法示例:   
```javascript
  var http = require('http');
  var server = http.createServer(function(req, res) {
    res.writeHead(200, 'This is ok!', {'Content-Type': 'text/plain'});
    res.end('OK');
  });
  server.listen(8088);
```

#### response.writeContinue
**writeContinue()** 发送` HTTP/1.1 100 `头信息给客户端，表示应该继续发送请求体。

#### response.setTimeout
**setTimeout(msecs, callback)**设置响应超时。   
```
  * msecs {Number}          超时时间，毫秒
  * callback {Function}     回调函数
```

#### response.setHeader
**setHeader(name, value)** 设置单条响应头信息。如果已存在同名的值，则将其替换。   
用法示例:   
```
  var http = require('http');
  http.createServer(function(req, res) {
    res.setHeader('Content-Type': 'text/html');
    res.end('OK');
  }).listen(8088);
```

#### response.removeHeader
**removeHeader(name)** 删除单条响应头信息。与上面的` setHeader() `用法一致。

#### response.getHeader
**getHeader(name)** 在响应头缓存后且未发出之前，可以使用此方法来获取某单条头信息的值。

#### response.write
**write(chunk, [encoding])** 写数据到客户端。第一次调用时会发送所有已缓存的响应头，后续调用会以流式方式处理数据。   
```
  * chunk {String|Buffer}     写到客户端的数据
  * encoding {String}         数据内容的字符编码，默认为utf8
```   
用法示例:   
```javascript
  var http = require('http');
  http.createServer(function(req, res) {
    res.writeHead(200, {'Content-Type': 'text/plain'});
    res.write('Hello, Node.js!');
    res.end();
  }).listen(8088);
```

#### response.addTrailers
**addTrailers(headers)** 添加后缀头信息(添加在响应后面的头信息)到响应。它只有在数据传输方式为` Transfer-Encoding:chunked `时有效。如在**HTTP 1.0**时，就会默认丢弃。当你需要使用**addTrailers**方法时，需要在头信息里添加`Trailer`头。   
```javascript
  var http = require('http');
  http.createServer(function(req, res) {
    res.writeHead(200, {
      'Content-Type': 'text/html',
      'Trailer': 'Content-MD5'
    });
    res.write('Hello, Node!');
    res.addTrailers('Content-MD5': 'Hi,md5');
    res.end();
  }).listen(8088);
```

#### response.end
**end([data], [encoding])** 当调用此方法时，标志着所有的头信息和响应体都已发送完毕，每个响应都必须调用此方法。当传递参数时，就相当于在结束响应前，调用了**write()**方法向客户端写了一次数据。
   


------

## 总结

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
   


