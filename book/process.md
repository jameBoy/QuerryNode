# Process
进程对象**process**是一个全局对象，你可以任何地方使用它。   

------

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

   

### abort
当**process.abort()**方法被调用时，会使Node.js触发一个***abort***事件。这个方法的调用会使Node.js进程退出，并生成一个内核文件。

   

### chdir
**process.chdir(directory)**会在运行时改变当前工作目录。如果传入参数错误或找不到路径，则会抛出一个异常。   
用法示例:   
```javascript
    console.log('Starting directory: ' + process.cwd());
    try {
      process.chdir('/tmp');
      console.log('New directory: ' + process.cwd());
    }
    catch (err) {
      console.log('chdir: ' + err);
    }
```

   

### cwd
**process.cwd()**会返回当前进程的工作目录。   
用法示例:   
```javascript
  console.log('当前工作目录是: ' + process.cwd());
```

   

### env
**process.env**存储了用户运行环境信息。   
其中有:   
  
  * USER        // 用户名
  * SHELL       // 用户运行的shell环境
  * PATH        // 用户环境变量路径
  * PWD         // 当前工作目录完整路径
  * LANG        // 系统语言
  * HOME        // 用户主目录
  * OLDPWD      // 前一工作目录
  * ...
   

### exit
**process.exit([code])**当这个方法调用时，进程会退出，退出码为传入参数或者0。

   

### version
**process.version**存储当前Node.js的版本号。

   

### versions
**process.versions**是一个数组，里面存储了Node.js及其依赖类库的版本号。

   

### config
**process.config**是一个存储了编绎当前Node.js执行文件的环境配置信息，相当于` ./configure `生成的内容。

   

### pid
**process.pid**是一个存储当前进程号的数字。` console.log('pid: ' + process.pid) `。

   

### kill
**process.kill(pid, [signal])**这个方法会发送一个信息量给目标进程，默认为**SIGTERM**。虽然它名字是**kill**，但它并不一定会杀死目标进程。

   

### title
**process.title**是一个可以设置和读取的变量，是使用`ps`命令时的进程名字。   
用法示例:   
```javascript
  // 在文件app.js中
  process.title = 'node-allen';   // 设置进程名为: node-allen
  process.stdin.resume();     // 保持进程不退出
```   
在使用` $ node app.js `后，在另一个终端里，使用`ps`:   
```shell
  $ ps | grep node
  # 输出: 13779 ttys    0:00.08 node-allen
```   

   

### arch
**process.arch**当前运行电脑CPU的类型，可能的值有: **arm**, **ia21**, **x64**等。

   

### platform
**process.platform**表示当前电脑运行的系统平台，可能的值有: **drawin**(mac osx), **freebsd**, **linux**, **win32**等

   

### memoryUsage
**process.memoryUsage()**返回的是当前Node进程所占用的内存空间对象。   
用法示例:   
```
  console.log(process.memoryUsage());
  // { rss: 15106048, heapTotal: 7326976, heapUsed: 3059360 }
  // rss是当前进程占用内存，headpTotal与heapUsed是v8的。
```   

   

### nextTick
**process.nextTick(callback)**的作用类似于` setTimeout(fn, 0) `, 但是它更高效。一般来说，它会在其它I/O事件触发前触发。   
用法示例:   
```javascript
    function Tick() {
      console.log('do tick one');

      process.nextTick(function() {
        console.log('inner next tick.');
      });

      setTimeout(function() {
        console.log('setTimeout inner.');
      }, 0);

      console.log('do tick two.');
    }

    Tick.prototype.sayHello = function() {
      console.log('say hello.');
    };

    process.nextTick(function() {
      console.log('outter next tick.');
    });

    setTimeout(function() {
      console.log('setTimeout outter.');
    }, 0);

    console.log('before new.');
    var s = new Tick();
    console.log('after new.');
    s.sayHello();
    console.log('after sayHello');
```   
上面的执行打印结果为:   
```shell
  before new.
  do tick one
  do tick two.
  after new.
  say hello.
  after sayHello
  outter next tick.
  inner next tick.
  setTimeout outter.
  setTimeout inner.
```   
由上面结果可知，**process.nextTick**会在**setTimeout**之前执行，因为它会在下一次事件循环前调用，而不是直接压入事件循环队列。

   

### maxTickDepth
因为**process.nextTick**会在下一次事件循环前调用，所以，不正确地使用**process.nextTick**可能会阻塞，使得事件队列不能触发。因而引入**process.maxTickDepth**来评估是否应该继续事件队列，而不是一直阻塞。

   

### umask
**process.umask([mask])**可以用来读写Node.js创建文件权限的掩码。在Windows下可能基本不用了解文件权限，但在Linux/Unix下，文件权限是很重要的，它表示你是否可以对一个文件进行读或写。详细参见[fs](./fs.md)   
用法示例:   
```javascript
  var oldmask, newmask = 0644;
  oldmask = process.umask(newmask);
  console.log('Changed umask from: ' + oldmask.toString(8) + ' to ' + newmask.toString(8));
```   

   

### uptime
**process.uptime()**这个方法返回当前Node进程已经运行的秒数。

   

### hrtime
**process.hrtime()**返回的是一个高精度的时间间隔数组。因为CPU会分出时间片来分别执行不同的应用，所以，当我们使用类似于定时器这类定时监听回调时，其时间间隔并不准确。我们可以使用这个方法来获取一个更高精度的具体时间间隔。   
用法示例:   
```javascript
  var time = process.hrtime();
  setInterval(function() {
    var diff = process.hrtime(time);
    console.log(diff);
    console.log(diff[0] * 1e9 + diff[1]);
  }, 1000);
```   
这个数组的第一个参数是以秒为单位，第二个参数是以纳秒为单位。两者相加，即是更高精度的时间间隔。   

   
---
以下几个方法仅在**POSIX**(Linux/Uninx)系统下有效。
   
### getgid
**process.getgit()** 返回用户组ID

### setgid
**process.setgit(id)** 设置用户组ID，参数可以是数字和字符串(用户组名)。

### getuid
**process.getuid()** 返回用户ID

### setuid
**process.setuid(id)** 设置用户ID，参数可以是数字和字符串(用户名)。

### getgroups
**process.getgroups** 返回所有的组ID的数组。

### setgroups
**process.setgroups(groups)** 添加额外的组。 

### initgroups
**process.initgroups(user, extra_group)** 初始化用户的组信息。
   
---
   

   