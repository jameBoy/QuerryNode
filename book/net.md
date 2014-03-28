# net
**net**模块提供了一个异步网络TCP Socket或UNIX domain Socket服务。当使用它时，需要` require('net') `来使用。

------

## API

------

### net.createServer
**createServer([options], [listener])**用于创建一个新的TCP服务器

参数解析:

```
  * options {Object}         创建TCP服务器时选项
    - allowHalfOpen {Boolean}  是否允许半开连接
  * listener {Function}      对'connection'事件的监听器
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

### net.connect && net.createConnection
**net.connect**与**net.createConnection**两者完全相同，相当于别名，
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


### 


