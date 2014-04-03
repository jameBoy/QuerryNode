# net
**net**模块提供了一个异步网络TCP Socket或UNIX domain Socket服务。当使用它时，需要` require('net') `来使用。

------

## API

### net.createServer
**createServer([options], [listener])**用于创建一个新的TCP服务器

参数解析:

```
  * options {Object}         创建TCP服务器时选项
    - allowHalfOpen {Boolean}  是否允许半开连接
  * listener {Function}      对'connection'事件的监听器
    - socket {net.Socket}      socket连接对象
  + return {net.Server}      创建的服务器对象
```

用法示例:
```javascript
  var net = require('net');
  var server = net.createServer(function(socket) {
    console.log('client connect.');
    socket.on('end', function() {
      console.log('disconnected.');
    });
    socket.write('Hello, client!\r\n');
    socket.pipe(socket);
  });
  server.listen(8088, function() {
    console.log('bind server.');
  );
```

------

### net.connect
### net.createConnection
**connect**与**createConnection**两者完全相同，相当于别名，
它们用于创建一个TCP客户端。其中创建客户端有几种不同的方式。

创建方式:

  * [标准方式]使用对象参数形式创建**connect(options,[listener])**
  * 使用端口号与主机名创建TCP服务端**connect(port, [host], [listener])**
  * 使用路径创建socket客户端**connect(path, [listener])**

其中，后两种是旧的API，推荐使用第一种。

参数解析:   
```
  * options {Object}         创建客户端所需参数
    // ! Tcp socket
    - port {Number}            [必需]服务端TCP端口号
    - host {String}            服务端主机名，默认'localhost'
    - localAddress {String}    本地通信使用的网络接口
    // ! UNIX socket
    - path {String}            [必需]通信使用的socket文件路径
  * listener {Function}      监听'connect'事件的监听器
  + return {net.Socket}      返回创建的Socket对象
```

用法示例:
```javascript
  var net = require('net');
  var client = net.connect({ port: 8088 }, function() {
    console.log('connect 8088');
    client.write('Hello, Node!\r\n');
  });
  client.on('data', function(data) {
    console.log(data.toString());
  });
  client.on('end', function() {
    console.log('connect end!');
  });
```


### net.isIP
**isIP(input)**用来检测是否为一个合法的IP地址。**!!!**此接口可能会移动到DNS模块中去，
保留在此是因为历史遗留问题。

参数解析:
```
  * input {String}         待检测字符串
  + return {Number}       不合法地址: 0; IPv4地址: 4; IPv6地址: 6;
```

用法示例:
```javascript
  var net = require('net');
  var version = net.isIP('127.0.0.1');
  console.log(version);        // 4
```

### net.isIPv4
### net.isIPv6
**isIPvX(input)**判断是否为IPv4/6地址。

参数解析:
```
  * input {String}         待检测字符串
  + return {Boolean}      是否是合法的地址
```

用法示例:
```javascript
  var net = require('net');
  var i4 = net.isIPv4('127.0.0.1');     // true
  var i6 = net.isIPv6('::1');        // true
```

------

## net.Server {Class}
此类用于创建一个TCP与Unix socket服务器

### server.listen
监听服务方法，有下面三种情况:

  1. listen(port, [host], [backlog], [callback])  根据端口号监听TCP连接服务
    * port {Number} 服务端端口号
    * host {String} 服务端主机名
    * backlog {Number} 最大等待连接数
    * callback {Function} 监听到'listening'事件的回调
  2. listen(path, [callback])  根据Unix socket文件路径监听socket连接服务
    * path {String} 监听的socket路径
    * callback {Function} 监听到'listening'事件的回调
  3. listen(handle, [callback])  根据处理对象监听连接服务
    * handle {Object} 真正的处理对象
    * callback {Function} 监听到'listening'事件的回调

一般情况下，前两种会用得比较多。

### server.close
**close([callback])**停止接受新请求并维持现有请求，当所有请求完成后关闭服务同时触发一个'close'事件。

参数解析:
```
  * callback {Function}    监听'close'事件的回调
```
可以不传入回调，显式地监听'close'事件。

### server.address
**address()**返回操作绑定的地址、端口号与协议，形如: `{ port: 12346, family: 'IPv4', address: '127.0.0.1' }`。
请在'listening'事件触发之后，再调用此方法。

用法示例:
```js
  var net = require('net');
  var server = net.createServer(function(socket) {
    socket.end('Bye\n');
  });
  sever.listen(8988, function() {
    var address = server.address();
    console.log(address);
  });
  server.on('error', function(e) {
    console.log(e);  
  });
```


### server.unref
**unref()**会解除服务的保持。如果Node进程中没有其它事件监听器等需要处理的东西，服务端所有连接都关闭后，
就会退出Node进程。


### server.ref
**ref()**方法与上面的**unref()**方法相对立，如果调用**unref()**方法后调用此方法，将会使服务保持，
Node进程不退出。


### server.maxConnections
**maxConnections**此属性用于设置服务端最高的请求连接数，如果超出此数，新的连接将会被拒绝。


### server.getConnections
**getConnections(callback)**获取当前连接数，当发送socket给子进程时也有效。

参数解析:
```
  * callback {Function}    获得结果后的回调函数
    - err {Error}            发生错误时的错误对象
    - count {Number}         连接数
```


### Events
创建的服务端可以监听的事件:
```
  * listening      使用.listen()方法时会触发此事件
  * connection     当有新连接成功建立时会触发，回调函数会传入一个net.Socket对象
  * close          当连接关闭时触发
  * error          当连接出错时触发
```

------

## net.Socket {Class}
这是TCP与Unix套接字的一个抽象，它实现了一个双向流。使用` net.connect() `返回值以及在net.Server中回调函数里
传入的参数都是net.Socket对象。


### new net.Socket([options])
实例化一个新的Socket对象。

参数解析:
```
  * options {Object}          构建新Socket对象时参数
    - fd {Number}               构造Unix socket的文件描述符
    - type {String}             新Socket对象类型['tcp4', 'tcp6', 'unix']
    - allowHalfOpen {Boolean}   是否允许半开连接，详见'end'事件
```

### socket.connect
其与[net.connect](#net_connect)相同，实际上，net.connect就是调用此方法。

### socket.setEncoding
**setEncoding([encoding])**用于设置当Socket对象作为读流使用时，即监听到'data'事件时，
对接收到的Buffer数据以此编码方式进行编码。

### socket.write
**write(data, [encoding], [callback])**通过Socket对象发送数据。

参数解析:
```
  * data {Buffer|String}     要写入的数据
  * encoding {String}        写入字符串数据的编码格式
  * callback {Function}      写入完成后的回调
  + return {Boolean}         当数据全部缓冲到内核后返回true，否则返回false
```

用法示例:
```js
  var net = require('net');
  var server = net.createServer(function(socket) {
    socket.on('data', function(data) {
      console.log(data);    // print Buffer
      // console.log(data.toString());
    });
  });
  server.on('error', function(err) {
    console.log(err);
  });
  server.listen(8999);
  var client = net.connect(8999, function() {
    client.write('client connect!', 'utf8');
  });
  client.on('error', function(err) {
    console.log(err);
  });
```

### socket.end
**end([data], [encoding])**发送一个**FIN**包，半关闭连接。此时，服务器仍可能在发送数据过来。
当传入数据内容参数时，相当于先调用`socket.write(data)`，再调用`socket.end()`。

### socket.destroy
**destroy()**确保这个socket连接上再没有I/O事件了。只有出现错误时才有必要使用此方法。

### socket.pause
**pause()**暂停读取数据，使'data'事件不再触发。对于减速上传比较有用。

### socket.resume
**resume()**在调用`socket.pause()`后使用此方法，可以恢复socket对象进行读数据。

### socket.setTimeout
**setTimeout(timeout, [callback])**可以设置socket对象超时时间。默认情况下是没有超时的。
当超时时间到了时，会触发'timeout'事件。你可以手动去调用'end()'或'destroy()'方法。

参数解析:
```
  * timeout {Number}        超时时间(ms)。如果为0，则会禁止超时事件。
  * callback {Function}     超时后的回调，相当于监听'timeout'事件。
```

### socket.setNoDelay
**setNoDelay([noDelay])**设置不延时。TCP默认会使用Nagle算法，对要发出去的数据进行缓存。
调用此方法可以设置是否延时缓存数据。

参数解析:
```
  * noDelay {Boolean}       若为true，则不会缓存直接发送数据；为false，则使用延时。默认为true
```

### socket.setKeepAlive
**setKeepAlive([enable], [initialDelay])**设置是否启用长连接。

参数解析:
```
  * enable {Boolean}           若为true，则使用长连接，否则不使用。默认为false
  * initialDelay {Number}      最后一个数据包与第一个长连接探针之间的延时，默认为0
```

### socket.address
**address()**返回绑定的地址、端口与协议。

参数解析:
```
  + return {Object}       返回对象
    - port                  端口
    - family                协议
    - address               地址
```

### socket.unref
**unref()**调用此方法会允许Node进程在只有此socket活动的情况下退出。

### socket.ref
**ref()**调用此方法使**unref()**方法失效。

### socket.remoteAddress
**remoteAddress**返回socket另一端的地址。

### socket.remotePort
**remotePort**返回socket另一端的端口。

### socket.localAddress
**localAddress**返回本端的地址。

### socket.localPort
**localPort**返回本端的端口。

### socket.bytesRead
**bytesRead**返回已接收到的字节数。

### socket.bytesWritten
**bytesWritten**返回已发送出去的字节数。

### socket.Events
socket的事件类型有:

  * connect  当一个socket连接成功建立时触发
  * data  当socket中有数据到达时触发
  * end  当另外一端发送**FIN**包时触发
  * timeout  设置了socket超时，当超时时间到达时触发
  * drain  当写Buffer中数据全部发出时触发
  * error  当出现错误时触发
  * close  当socket完全关闭时触发


------

