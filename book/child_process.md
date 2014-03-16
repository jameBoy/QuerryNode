# Child Process
Node.js及浏览器端的JavaScript都是运行在单进程中的，这样对于多核CPU的利用率不高，可能造成单个CPU负载过高而其它CPU处于空闲状态。因此，Node.js提供了**child_process**这个模块，用于创建多个进程，这样有效地利用多核CPU。

------

## API

### child_process.spawn
**spawn(command, [args], [options])**方法是**child_process**模块中很重要的一个方法，此模块中的其它几个方法都是基于它修改而来。**spawn**方法会创建一个子进程来执行命令。   
参数详解:   
```  
  * command {String}        要运行的命令
  * args {Array}            [可选]一个字符串形式的参数数组，如果为空，则默认为一个空的数组
  * options {Object}        [可选]运行命令时可用的一些选项，默认为: { cwd: undefined, env: process.env }
    * cwd {String}             指定子进程运行时的当前目录
    * stdio {Array|String}     子进程标准输入流配置，参见下面的[stdio]
    * env {Object}             子进程的环境变量
    * detached {Boolean}       子进程是否为一个进程组的管理者
    * uid {Number}             子进程的用户ID
    * gid {Number}             子进程的组ID
  + return {ChildProcess}   返回一个**ChildProcess对象  
```   
用法示例:   
```javascript
  var spawn = require('child_process').spawn;
  var ls = spawn('ls', ['-al', '/usr']);
  ls.stdout.on('data', function(data) {
    console.log('out: ' + data);
  });
  ls.stderr.on('data', function(data) {
    console.log('err: ' + data);
  });
  ls.on('close', function(code) {
    console.log('Exit: ' + code);
  });
```

如果**detached**选项被设置了，子进程将会成为一个新进程组的管理者。它使得在父进程退出后子进程仍然可以运行。
默认情况下，父进程会等待使用了**detached**选项的子进程退出。可以使用**child.unref()**方法来阻止父进程等待这样的子进程，这个方法会使父进程的循环引用计数中不包括这个子进程。   

当使用**detached**选项来开启一个长时间运行的进程，除非它的**stdio**配置与父进程的没有联系，否则它不会保持在后台运行。如果它继承了父进程的**stdio**，那么这个子进程将会保持联接在控制终端。

#### stdio
**stdio**选项是子进程中一个数组对象，其每个索引对应于一个文件描述符。它的值有:   

  1. **pipe** - 在父子进程之间创建一个管道，其用一个**child_process**对象中形如`ChildProcess.stdio[fd]`的属性暴露给父进程端。管道创建文件描述符0-2对应于ChildProcess.`stdin`,`stdout`,`stderr`。
  2. **ipc** - 在父子进程之间创建一个IPC通道来传递消息及文件描述符。一个子进程最多只能有一个IPC标准流描述符。设置了这个选项后可以使用ChildProcess.send()方法。如果子进程写入JSON格式消息到此文件描述符，会触发ChildProcess.on('message')。如果子进程是个Node.js程序，那么**process.send()**和**process.on('message')这两个方法将可以被使用。
  3. **ignore** - 在子进程中不设置文件描述符。注意，因为Node始终会为它复制的子进程打开文件描述符0-2，所以当这些都被设置为忽略时，它会将`/dev/null`绑定到子进程的文件描述符上。
  4. **Stream Object** - 与子进程共享指向tty、file、socket或pipe的可写或可读流。这个流下层的文件描述符将会复用给对应于标准流数组中索引的文件描述符。
  5. **正整数** - 这个正整数对应于父进程中打开的文件描述符。它会像流对象那样与子进程共享。
  6. **null, undefined** - 使用默认的值。对应于文件描述符0-2,将会创一个管道。对应于大于3的文件描述符，默认使用**ignore**。

作为一个简便的使用方式，也可以使用下面的字符串而不是一个数组:   
  
  * ignore - ['ignore', 'ignore', 'ignore']
  * pipe - ['pipe', 'pipe', 'pipe']
  * inherit - [process.stdin, process.stdout, process.stderr] 或者是 [0, 1, 2]


### child_process.exec
**exec(command, [options], callback)**方法将会在shell中运行命令并缓冲输出。原理实现上，它其实就是调用的下面的**execFile**方法。   
参数:   
```
  * command {String}           待运行的命令
  * options {Object}           [可选]运行时选项
    * cwd {String}               子进程的当前工作目录
    * env {Object}               运行时环境的键值对
    * encoding {String}          输出流的文本编码，默认为utf8
    * timeout {Number}           超时时间，默认为0
    * maxBuffer {Number}         最大缓冲空间，默认为 200*1024
    * killSignal {String}        正常终止信号，默认为 SIGTERM
  * callback {Function}        回调函数
    * error {Error}              子进程出错的错误对象
    * stdout {Buffer}            子进程标准输出
    * stderr {Buffer}            子进程标准错误输出
  + return {ChildProcess}      返回子进程对象
```   
用法示例:   
```javascript
  var exec = require('child_process').exec;
  var cp = exec('ls -l ./', {
    cwd: '/usr/local/'
  }, function(err, stdout, stderr) {
    if(err) {
      console.log('Error: ' + err);
    }
    console.log('stdout: ' + stdout);
    console.log('stderr: ' + stderr);
  });
```


### child_process.execFile
**execFile(file, [args], [options], [callback])**可以用来执行一个可执行的文件，它的用法和上面的**exec**相似，只是一个执行文件，一个执行命令。   
参数:   
```
  * file {String}              要运行的可执行文件
  * args {Array}               [可选]运行参数
  * options {Object}           [可选]运行时选项
    * cwd {String}               子进程的当前工作目录
    * env {Object}               运行时环境的键值对
    * encoding {String}          输出流的文本编码，默认为utf8
    * timeout {Number}           超时时间，默认为0
    * maxBuffer {Number}         最大缓冲空间，默认为 200*1024
    * killSignal {String}        正常终止信号，默认为 SIGTERM
  * callback {Function}        [可选]回调函数
    * error {Error}              子进程出错的错误对象
    * stdout {Buffer}            子进程标准输出
    * stderr {Buffer}            子进程标准错误输出
  + return {ChildProcess}      返回子进程对象
```   
用法示例:   
```javascript
  #! /usr/bin/env node

  // a.js
  console.log('This is a!');
```   
```javascript
  // b.js
  var execFile = require('child_process').execFile;
  execFile('./a.js', function(err, stdout, stderr) {
    console.log('stdout: ' + stdout);
  });
```

### child_process.fork
**fork(modulePath, [args], [options])**命令会在创建的子进程中执行参数文件里的内容。这与使用` node xxx.js `的形为大致相同。   
参数:   
```
  * modulePath {String}           要在子进程中运行的模块
  * args {Array}                  [可选]运行参数
  * options {Object}              [可选]运行时选项
      cwd {String}                  子进程工作的当前目录
      env {Object}                  子进程运行时环境变量键值对
      encoding {String}             字符编码，默认为utf8
      execPath {String}             创建子进程的可执行文件
      execArgv {Array}              传递给**node**的参数
      silent {Boolean}              默认为false。如果参数为真，那么`stdin`,`stdout`,`stderr`会以管道的形式与父进程联系，否则会直接继承父进程的。
  + return {ChildProcess}         返回子进程对象
```

用法示例:   
```javascript
  // a.js
  console.log('This is a.js!');
```   
```javascript
  // b.js
  var fork = require('child_process').fork;
  var child = fork('./a.js');   // 输出: This is a.js!
  // child.silent = true;
  // child.stdout.on('data', function(data) {
  //   console.log(data);       // 输出: This is a.js!
  // });
```   

------

### Tips

 1. 因为子进程总是会创建一个新的V8实例，而创建一个V8实例会消耗大约30毫秒的启动时间与10M的内存空间。所以，尽量少使用子进程。



