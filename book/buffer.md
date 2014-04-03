# Buffer
> 在原生的JavaScript这门语言中，它对操作字符串比较友好，而没有任何涉及到二进制操作的方法与类。
>因为在JavaScript以前的应用场景--浏览器端，根本无需处理二进制数据。但是在Nodo.js中，需要处
>理网络与文件，这样会接触到很多二进制数据。所以，Node.js自己构建了一个Buffer类，用于处理二
>进制数据。

由于Buffer对象使用很频繁，所以把它作为一个全局的对象，无需使用`require`。

一个Buffer对象很像一个整型数组，但它其实是使用的V8堆外内存。同时，Buffer对象的大小在生成之后是不可变的。
Buffer对象与JavaScript中的字符串对象进行转换时，需要一个编码格式。当前Node支持的几种格式有:

  * ascii  使用7位ASCII码格式编码，这种编码速度很快，但国际通用字符支持不好。
  * utf8   Unicode字符集下的一种可变字符长度编码，支持范围最广，是Node默认的编码格式。
  * utf16le/ucs2 2到4字节，小端编码的Unicode字符集编码格式。
  * base64 Base64字符串编码格式。
  * hex  一个字节使用两个十六进制字符编码。



------

## API

### Buffer {Class}
Buffer类有多种构造函数。分别为:
```
  * new Buffer(size)             使用字节数来创建
    - size {Number}                需要的字节数
  * new Buffer(array)            使用十六进制数的数组来创建
    - array {Array}                十六进制数的数组
  * new Buffer(str, [encoding])  使用字符串来创建
    - str {String}                 初始化字符串
    - encoding {String}            字符串的编码格式，默认utf8
```

***类方法***
------

### Buffer.isEncoding
**isEncoding(encoding)**用来判断编码格式是否被Node支持用来创建Buffer。

参数解析:
```
  * encoding {String}     字符编码格式
  + return {Boolean}      是否被支持用来创建Buffer
```

用法示例:
```js
  console.log(Buffer.isEncoding('utf8'));       // true
  console.log(Buffer.isEncoding('utf9'));       // false
  console.log(Buffer.isEncoding('gbk'));        // false
  console.log(Buffer.isEncoding('ascii'));      // true
  console.log(Buffer.isEncoding('aScIi'));      // true
```

### Buffer.isBuffer
**isBuffer(obj)**用来检测是否是一个Buffer对象。

参数解析:
```
  * obj {Object}          待检测对象
  + return {Boolean}      返回是否是Buffer对象
```

用法示例:
```js
  var b = new Buffer(1);
  console.log(Buffer.isBuffer(b));
  console.log(Buffer.isBuffer(null));
```

### Buffer.byteLength
**byteLength(string, [encoding])**给出一个字符串所占用的字节数，而不是字符数。

参数解析:
```
  * string {String}       待检测的字符串
  * encoding {String}     字符串的编码格式，默认为utf8
  + return {Number}       字符串所占用的字节数
```

用法示例:
```js
  var str = '你好,中国!';
  console.log(str.length);              // 6
  console.log(Buffer.byteLength(str));  // 14
```

### Buffer.concat
**concat(list, [totalLength])**用来拼接多个Buffer对象。

参数解析:
```
  * list {Array}            Buffer对象数组
  * totalLength {Number}    待拼接字符串的总长度
  * return {Buffer}         接接完成后的Buffer对象
```

当不提供totalLength时，Node会循环遍历所有Buffer对象的长度进行累加。如果提供了totalLength，
但totalLength大于实际总长度，则会拼接所有Buffer对象，剩余空间会是一些随机值；
如果totalLength长度小于实际总长度，则会有以下两种情况:

  1. totalLength 大于直到倒数第二个Buffer对象的长度，则剩余空间拼接最后一个Buffer对象的前某些字节。
  2. totalLength 小于直到倒数第二个Buffer对象的长度，则抛出一个错误。

用法示例:
```js
  var buf1 = new Buffer('Hello, node.js!');
  var buf2 = new Buffer('Hello, world.');
  var len1 = buf1.length;
  var len2 = buf2.length;
  var arr = [buf1, buf2];
  var buf = Buffer.concat(arr);      // 'Hello, node.js!Hello, world.'
  // var buf = Buffer.concat(arr, (len1 + len2 - 2));   // 'Hello, node.js!Hello, worl'
  // var buf = Buffer.concat(arr, len1);       // throw new RangeError('targetStart out of bounds');
```


***实例方法***
------

### buf.length
**length**这个属性是Buffer对象所占用的内存空间字节数。它不会随Buffer对象内容的改变而改变。
因为Buffer对象生成后，其大小不可变。所以，此属性也不可变。


### buf.copy
**copy(targetBuffer, [targetStart], [sourceStart], [sourceEnd])**
将此Buffer对象的内容拷贝到另一个Buffer对象中去。

参数解析:
```
  * targetBuffer {Buffer}   目标Buffer对象
  * targetStart {Number}    拷入到目标Buffer对象的起始位置
  * sourceStart {Number}    本Buffer对象拷贝的起始位置
  * sourceEnd {Number}      本Buffer对象拷贝的终止位置
  + return {Number}         拷贝过去字节数
```

用法示例:
```js
  var buf1 = new Buffer('Hello,Node.');
  var buf2 = new Buffer(20);
  var len = buf1.copy(buf2);
  console.log(len);   // 11
```

### buf.slice
**slice([start], [end])**对Buffer对象切片，返回一个新Buffer对象。

参数解析:
```
  * start {Number}       切片开始位置(包含)
  * end {Number}         切片终止位置(不包含)
  + return {Buffer}      返回新的Buffer对象
```

**注意**，新的Buffer对象与原Buffer对象引用同一内存位置。对新Buffer对象的修改会反映到原Buffer对象上。
同样，对原Buffer对象的修改也会影响新Buffer对象。

用法示例:
```js
  var buf1 = new Buffer('Hello');
  var buf2 = buf1.slice(0, 3);
  console.log(buf2.toString());  // Hel
```

### buf.fill
**fill(value, [offset], [end])**以特定的值填充Buffer对象。

参数解析:
```
  * value {String|Number}    填充Buffer的值，如果是数字，则转换成16进制填充
  * offset {Number}          填充时Buffer的偏移量，默认为0
  * end {Number}             填充的结束位置，默认为Buffer长度
```

**注意**，如果value是一个长字符串，则选取第一个字符进行填充。

用法示例:
```js
  var buf = new Buffer(10);
  buf.fill('h');
  // buf.fill(76);               // LLLLLLLLLL
  console.log(buf.toString());   // hhhhhhhhhh
```

### buf.write
**write(string, [offset], [length], [encoding])**写一个字符串到Buffer中去。

参数解析:
```
  * string {String}      待写入的字符串
  * offset {Number}      写入位置的偏移量， 默认为0
  * length {Number}      写入长度，默认为buffer.length - offset
  * encoding {String}    待写入字符串的编码，默认为utf8
```

用法示例:
```js
  var buf = new Buffer(20);
  var buf1 = new Buffer('Hello, Node');
  buf.write('Hello, Node', 3);
  console.log(buf1); // <Buffer 48 65 6c 6c 6f 2c 20 4e 6f 64 65>
  console.log(buf);  // <Buffer e0 70 03 48 65 6c 6c 6f 2c 20 4e 6f 64 65 00 00 a8 01 0f 84>
```

### buf.toString
**toString([encoding], [start], [end])**返回Buffer对象对应的字符串。

参数解析:
```
  * encoding {String}     返回字符串的编码，默认为utf8
  * start {Number}        Buffer对象的起始位置，默认为0
  * end {Number}          Buffer对象的终止位置，默认为buf.length
```

用法示例:
```js
  var buf = new Buffer('Hello, world.');
  var str = buf.toString('utf8', 3);
  console.log(str);  // lo, world.
```

### buf.toJSON
**toJSON()**返回Buffer中十六进制数对应的十进制数的数组。

用法示例:
```js
  var buf = new Buffer('hello');
  var s = buf.toJSON();
  console.log(buf);   // <Buffer 68 65 6c 6c 6f>
  console.log(s);     // [ 104, 101, 108, 108, 111 ]
```


------

> Buffer对象还有一大堆`readXXX`与`writeXXX`的方法，它们是以不同的方式去读写Buffer对象。

