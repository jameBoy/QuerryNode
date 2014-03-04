# Process
进程对象**process**是一个全局对象，你可以任何地方使用它。   

   

### 事件
**process**可以捕捉到一些影响到进程对象的事件，如下:   

  1. exit   
    当进程准备退出时，会触发**exit**事件。当进程的**exit**事件触发时，你将不能阻止其退出，当所有的退出事件监听器都执行后，进程将退出。特别注意的是，异步方法和定时器之类的将不会执行。你只能在监听器的回调函数里添加同步执行代码。监听器的回调会传入一个参数，那就是进程退出码。   
    ```javascript
      process.on('exit', function(code) {
        // 不要使用定时器或者异步方法
        setTimeout(function() {
          console.log('这里将不会被执行！');
        }, 0);
        // 可以这样使用同步代码
        console.log('退出码是: ', code);
      });
    ```   

  2. uncaughtException   
    异常事件和浏览器中事件一样，会向上冒泡。如果一个异常未被捕捉，会冒泡到**process**对象，导致**uncaughtException**事件的触发。如果没有添加对**uncaughtException**事件的监听，默认的监听器就会触发(也就是打印栈信息并退出进程)。   
    用法示例:   
      ```javascript
        process.on('uncaughtException', function(err) {
          console.log('caught exception: ' + err);
        });
      ```   
    **!!!**注意，这样使用进程捕获**uncaughtException**是一种非常粗暴的解决方式，不推荐使用。并且，在未来的版本中可能会移除。   
    你可以使用[domain](./domain.md)来代替它。   

   

### 信号事件
当进程接收到一个信号时，会触发信号事件。    

  * SIGUSR1
  * SIGTERM
  * SIGPIPE
  * SIGHUP
  * SIGINT
  * SIGBREAK
  * SIGWINCH
  * SIGKILL
  * SIGSTOP
   
用法示例:   
```javascript
  process.stdin.resume();
  process.on('SIGINT', function() {
    console.log('Got SIGING, Press Ctrl + D to exit.');
  })
```
   

### stdout
**process.stdout**是一个写到标准输出的可写流。
**process.stdout**与**process.stderr**与Node.js中其它流不太一样，它们在写的时候，通常是阻塞的。   

   * 当它们指向普通文件或者终端时，是阻塞的。
   * 当它们指向管道时: 
     * 在Linux/Unix下是阻塞的
     * 在Windows下和其它流一样是非阻塞的

检查Node.js是否是在一个终端中执行，可以检查**isTTY**属性，如下:   
```shell
  $ node -p "Boolean(process.stdin.isTTY)"  // true
  $ echo "foo" | node -p "Boolean(process.stdin.isTTY)"  // false
  $ node -p "Boolean(process.stdout.isTTY)"  // true
  $ node -p "Boolean(process.stdout.isTTY)" | cat   // false
```   
详细参见[tty](./tty.md)

### stderr
如上[stdout](#stdout)所述。   

   

### stdin
**process.stdin**是一个对标准输入的只读流。在Node.js v0.10版本以前，流([stream](./stream.md))的处理有所不同。**process.stdin**可以兼容地使用旧的模式，参见[Node.js process.stdin](http://nodejs.org/api/process.html#process_process_stdin)。但是你在一个新项目应该尽量使用新的处理方式。   
用法示例:   
```javascript
  process.stdin.setEncoding('utf8');

  process.stdin.on('readable', function(chunk) {
    var chunk = process.stdin.read();
    if (chunk !== null) {
      process.stdout.write('data: ' + chunk);
    }
  });

  process.stdin.on('end', function() {
    process.stdout.write('end');
  });
```   

   

### argv
**process.argv**这个对象存储的是命令行参数。   
用法示例:   
编写一个文件 processArgv.js , 内容如下。   
```javascript
  process.argv.forEach(function(val, index, arr) {
    console.log(index + ' : ' + val);
  });
```   
当使用命令行执行时:   
```shell
  $ node processArgv.js one two three 3 
  0 : node
  1 : processArgv.js
  2 : one
  3 : two
  4 : three
  5 : 3
```   
所以在写程序获取参数时，经常会这样写:   
```javascript
  var args = process.argv.slice(2);   // 获取除node和文件名之后的所有参数
```   

   

### execPath
**process.execPath**是一个存储了执行此进程的Node.js程序的绝对路径，如` /usr/local/bin/node `。   

   

### execArgv
**process.execArgv**存储的是Node.js执行所带的参数，它不会出现在**process.argv**中。   
用法示例:   
```shell
  $ node --harmony script.js --version -t 123
```   
其中，**process.execArgv**为:   
```javascript
  ['--harmony']
```   
而**process.argv**则为:   
```javascript
  ['/usr/local/bin/node', 'script.js', '--version', '-t', '123']
```








