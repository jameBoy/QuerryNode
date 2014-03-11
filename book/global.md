# Global Objects
全局对象即不使用`require`就可以使用的。其中，有一些虽然不用使用`require`，但是其实质上是模块级别的。
   

------

## API

#### global
**global**是一个全局对象，即意味着你在一个文件里为**global**赋上一个属性，在另一个文件里直接可以取来使用。   
**!!!**注意，直接在文件中使用***var***与在浏览器中表现不同，在**Node.js**文件中直接使用***var***声明的变量只是在这个文件中有效，而不像在浏览器中，会变成全局变量。

#### process
运行时进程**process**对象是全局的，参见[process](./process.md)

#### console
用来打印输出的**console**对象是全局的，参见[console](./console.md)

#### setTimeout
#### clearTimeout
#### setInterval
#### clearInterval
定时器、定时器消除、循环定时器、循环定时器消除都是全局的，参见[Timers](./timers.md)

#### Class: Buffer
Node.js中的**Buffer**类是一个全局的，参见[Buffer](./buffer.md)

#### require
**require**函数在所有Node.js文件中都可以直接使用，但它并不是一个真正的全局函数，只是在每一个模块里都有定义。   
实质上，如果熟悉[AMD](http://requirejs.org/docs/api.html)或者[seajs](http://seajs.org/docs/#quick-start)的话，就可以很容易理解这个概念。   
```javascript
  define(function(require, exports, module) {
    ...
  });
```   
**require**以及下面的**exports**和**module**都如上，你写的文件内容被***define***函数里的函数包裹起来了，它给了这三个参数，因而你可以引用得到，但它并不是全局的。   
使用示例:   
```javascript
  var http = require('util');
  util.log('Hello, Node.js!' + module.filename);
  exports.sayHello = function() {
    console.log('Hello, Node.js!');
  };
```   
   

#### require.resolve()
这个函数是用来解析某个模块位置。
用法示例:   
```javascript
  // 使用本例之前，先在此例文件所在目录使用` npm install mkdirp ` 来下载**mkdirp**包到本地
  var utilPath = require.resolve('util');
  var mkdirpPath = require.resolve('mkdirp');
  console.log(utilPath);      // 输出: util
  console.log(mkdirpPath);    // 输出: /***/node_modules/mkdirp/index.js
```   
这个函数返回的就是Node.js加载相应模块文件的路径。   
   

#### require.cache
这是一个**require**缓存已经加载了模块的对象。   
用法示例:   
```javascript
  var mkdirp = require('mkdirp');
  var util = require('util');
  console.log(util.inspect(require.cache, { showHidden: true, depth: null }));
```   
这个缓存对象并不存储Node.js中的模块，而是第三方模块数据。   

#### __filename
这是一个字符串，保存了此变量所在文件的路径信息。它也不是一个全局变量，而是在所在模块中都能引用到，与**require**类似。      
用法示例:   
```javascript
  console.log('__filename: ' + __filename);
```   

#### __dirname
这是一个保存了此变量所在文件的所在目录的全路径，它不是一个全局变量，与上面的**__filename**类似。   
**!!!**注意，它不代表当前工作目录。如果要使用当前工作目录信息，可以使用**process.cwd()**代替。   
用法示例:   
```javascript
  console.log(__dirname);
```
   

#### module
一个当前模块的引用，参见[module](./module.md)   
   

#### exports
当前模块导出的引用，**module.exports**的短路径。参见[module](./module.md)   
   

