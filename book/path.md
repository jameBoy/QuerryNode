# Path
Node.js中的**Path**模块主要用于路径的处理。使用` require('path') `来引入并使用它。

------

## API

### path.normalize
**normalize()**方法主要是将路径中的` .. `和` . `进行处理，将多个`/`替换成一个，保留路径尾部的`/`。   
用法示例:   
```javascript
  var path = require('path');
  var url = '/foo/bar/var///module/./inner/../';
  path.normalize(url);     //返回 '/foo/bar/var/module/'
```

### path.join
**join()**方法用于将多个路径合并成一个。参数必须都是字符串！   
用法示例:   
```javascript
  var path = require('path');
  path.join('/foo', 'bar', 'baz/asdf', 'quux', '..');  //返回 '/foo/bar/baz/asdf'
```

### path.resolve
**resolve([from ...], to)**方法返回一个解析完成后的绝对路径，它的使用可以想象成Shell中`cd`命令的执行。但有所不同的是，**resolve**中参数可以是不存在的路径或者是文件。   
```
  path.resolve('foo/bar', '/tmp/file/', '..', 'a/../subfile');
                       ||
                       \/
  // cd foo/bar
  // cd /tmp/file/
  // cd ..
  // cd a/../subfile
  // pwd
```   
由于**resolve**方法返回的是一个绝对路径，所以，当参数的结果不是一个绝对路径时，将会以当前目录为基础，构建一个绝对路径。

### path.relative
**relative(from, to)**返回一个表示从第一个路径参数到第二个路径参数的需要移动的路径。如果参数不为绝对路径，则使用**path.resolve**方法将其变为一个绝对路径。   
```javascript
  var path = require('path');
  path.relative('/usr/local/bin/node', '/usr/local/lib/foo/bar');
  //返回值为:  '../../lib/foo/bar'
```

### path.dirname
**dirname(p)**方法返回路径的目录，其后不带`/`。   
用法示例:   
```javascript
  var path = require('path');
  path.dirname('foo/bar');   // 返回: 'foo'
```

### path.basename
**basename(p, [ext])**方法返回路径中的最后一部份。如果提供了第二个参数作为已知文件的后缀名，则只会返回文件名不带后缀。   
用法示例:   
```javascript
  var path = require('path');
  path.basename('/usr/local/lib/node.js');         //返回: 'node.js'
  path.basename('/usr/local/lib/node.js', 'js');   //返回: 'node'
  path.basename('/foo/bar/');                      //返回: 'bar'
```

### path.extname
**extname(p)**返回后缀名。   
用法示例:   
```javascript
  var path = require('path');
  path.extname('index.html');        //返回: '.html'
  path.extname('index.coffee.md');   //返回: '.md'
  path.extname('index');             //返回: ''
```

### path.sep
**path.sep**表示系统的路径分隔符。   
在windows下，**path.sep**为`\\`;   
在*nix下，**path.sep**为`/`;

### path.delimiter
**path.delimiter**表示系统路径分隔符。   
在windows下，其为`;`。   
在*ninx下，其为`:`。
