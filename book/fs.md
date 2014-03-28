# fs
> fs = file system 文件系统

在Node.js中，加入了浏览器端JavaScript受限的文件操作。由于在JavaScript中没有原生的文件操作API可用，
所以Node采用了标准的POSIX风格接口来创建了一套文件API，并加入Node自身的异步回调风格。
在使用这些API之前，需要使用` require('fs') `来使用它。
文件的基本操作方法有同步与异步的版本，可以根据需要来调用相应接口。

------

## API

### fs.open
**open(path, flags, [mode], callback)**方法用于打开一个文件。   
参数解析:   
```
  * path {String}                 文件路径
  * flags {String}                打开文件方式标志
  * mode {String|Number}          [可选]打开的文件的权限，默认为0666。
  * callback {Function} (err, fd) 回调函数
    - err                           打开文件出错时的错误对象
    - fd                            打开文件的文件描述符
```
标志**flags**可选值有12个，为
> 'r', 'r+', 'rs', 'rs+', 'w', 'wx', 'w+', 'wx+', 'a', 'ax', 'a+', 'ax+'

具体在使用中可以根据需要来选择。

**mode**参数可选，它只有在文件不存在需要创建时有效。而且，在某些平台下可能会遇到创建文件权限与给定参数不同。
其中，当**mode**参数为字符串时，会将其当作八进制来处理，而为数字时会以十进制方式来处理。

回调函数接受两个参数，一个是**err**错误对象，一个是**fd**文件描述符。文件描述符可以传递给**read**或**write**等方法使用。

用法示例:
```javascript
  var fs = require('fs');
  fs.open('./a.txt', 'w+', '0644', function(err, fd) {
    if(err) {
      console.log('Error: ' + err);
      return;
    }
    console.log(fd);
  });
```

同步方法**openSync(path, flags, [mode])**用法类似，其返回一个**fd**文件描述符。

### fs.close
**close(fd, callback)**方法用于关闭一个文件。   
用法示例:
```javascript
  var fs = require('fs');
  var fd = fs.openSync('./b.txt', 'w+', '0664');
  fs.close(fd, function(err) {
    if(err) {
      console.log(err);
    }
  });
```

### fs.read
**read(fd, buffer, offset, length, position, callback)**方法用于读取某个文件内容。   
参数解析:   
```
  * fd {Number}             文件描述符
  * buffer {Buffer}         缓存读取内容的Buffer对象
  * offset {Number}         向Buffer对象写入时偏移位置
  * length {Number}         要读取的长度
  * position {Number}       读文件的起始偏移位置
  * callback {Function}     回调
      err {Error}             出现错误时的错误对象
      bytesRead {Number}      已经读取的字节数
      buff {Buffer}           读内容存储的Buffer对象( === buffer)
```
   
用法示例:   
```javascript
  var fs = require('fs');
  var filepath = './hello.txt';
  fs.open(filepath, 'r', function(err, fd) {
    fs.fstat(fd, function(err, stats) {
      var bufferSize = stats.size;
      var buffer = new Buffer(bufferSize);
      fs.read(fd, buffer, 0, bufferSize, 0, function(err, bytesRead, buff) {
        console.log(buff.toString());
        // 可以在这里这样关闭文件
        // if(bytesRead === bufferSize) {
        //   fs.close(fd);
        // }
      });
    });
    // fs.close(fd);   // 小心使用!!!,过早的关闭文件会引发错误。
  });
```
由于**node.js**的文件接口很多直接模仿POSIX，所以可能会很复杂。在实际应用中，可以使用简便方式代替。


### fs.write
**write(fd, buffer, offset, length, position, callback)**用于写内容到一个文件中去。   
它还有另外一种使用方式: `fs.write(fd, string[, position[, encoding]], callback);`。即可以写字符串内容到文件中去。

参数解析:   
```
  * fd {Number}           写入文件的文件描述符
  * buffer {Buffer}       存储写入到文件中内容的Buffer对象
  * offset {Number}       从Buffer中读取写内容的开始位置
  * length {Number}       从Buffer中读取写内容的长度
  * position {Number}     在文件中开始写的位置
  * callback {Function}   回调函数
    - err {Error}             写入文件出错时的错误对象
    - written {Number}        已写到文件中的字节数
    - buffer {Buffer}         存储内容的Buffer对象
```

用法示例:   
```javascript
  var fs = require('fs');
  fs.open('./hello.txt', 'w+', function(err, fd) {
    var buf = new Buffer('This is a Buffer.');
    var len = buf.length;
    fs.write(fd, buf, 0, len, 0, function(err, written, buf) {
      if(err) {
        console.log(err);
        return;
      }
      if(written === len) {
        fs.close(fd);
      }
      console.log(buf.toString());
    });
  });
```

### fs.readFile
**readFile(filename, [options], callback)**方法用于读取一个文件的内容   
参数解析:   
```
  * filename {String}          文件名(包含路径)
  * options {Object}           [可选]读文件时选项
    - encoding {String}          指定读文件的编码格式，默认为null
    - flag {String}              读文件的方式，默认为'r'
  * callback {Function}        回调函数
    - err {Error}                读文件出错时的错误对象
    - data {String|Buffer}       文件内容，如果没指定编码则为Buffer，否则返回String
```

用法示例:   
```javascript
  var fs = require('fs');
  fs.readFile('./hello.txt', { encoding: 'utf8' }, function(err, data) {
    if(err) {
      console.log(err);
      return;
    }
    console.log(data);
  });
```


### fs.writeFile
**writeFile(filename, data, [options], callback)**方法用于写内容到某一个文件   
参数解析:   
```
  * filename {String}          文件名(可包含路径)
  * data {String|Buffer}       待写入的内容
  * options {Object}           [可选]写入文件时的选项
    - encoding {String}          字符编码(当内容为字符串时)，默认为'utf8'。如果内容为Buffer时，忽略此参数
    - mode {Number}              写入文件的权限，默认为438(八进制0666)
    - flag {String}              写入文件的方式，默认为'w'
  * callback {Function}        回调函数
    - err {Error}                写入文件出错时的错误对象
```

用法示例:   
```javascript
  var fs = require('fs');
  fs.writeFile('./hello.txt', 'Hello, Node.js!', function(err) {
    if(err) {
      console.log(err);
    }
  );
```

### fs.appendFile
**appendFile(filename, data, [options], callback)**方法用于向文件追加内容   
参数解析:   
```
  * filename {String}         文件名(可包含路径)
  * data {String|Buffer}      待写入的内容
  * options {Object}          [可选]追加文件时的选项
    - encoding {String}         字符编码(当内容为字符串时)，默认为'utf8'。如果内容为Buffer时，忽略此参数
    - mode {String|Number}      追加写入文件的权限，默认为438(八进制0666)
    - flag {String}             追加写入文件的方式，默认为'a'
  * callback {Function}       回调函数
    -err {Error}                追加文件出错时的错误对象
```

用法示例:   
```javascript
  var fs = require('fs');
  fs.appendFile('./hello.txt', '\nAppend To hello.txt!', function(err) {
    if(err) {
      console.log(err);
    }
  );
```

### fs.mkdir
**mkdir(path, [mode], callback)**用来创建一个目录   
参数解析:   
```
  * path {String}            待创建目录路径
  * mode {String|Number}     [可选]待创建目录权限
  * callback {Function}      回调函数
    - err {Error}              创建目录出错时错误对象
```

用法示例:   
```javascript
  var fs = require('fs');
  fs.mkdir('./hello', function(err) {
    if(err) {
      console.log(err);
    }
  );
```

使用此方法创建目录时，其父目录必须存在，否则出错。如果不确定父目录是否存在，可以使用
substack编写的[mkdirp](https://github.com/substack/node-mkdirp)模块。

### fs.stat
**stat(path, callback)**用来获取一个文件或目录的信息   
参数解析:   
```
  * path {String}            文件或目录路径
  * callback {Function}      回调函数
    - err {Error}              读取信息出错时的错误对象
    - stats {fs.Stats}         读取的状态信息
```

用法示例:   
```javascript
  var fs = require('fs');
  fs.stat('./hello.txt', function(err, stats) {
    if(err) {
      console.log(err);
      return;
    }
    console.log(stats);
  });
```



------
