# Console
> **Console**接口源于浏览器，Firefox的插件Firebug建立了一个**Console** API的事实标准。
> Node.js的**Console**接口与大部份浏览器表现一样，其主要用于打印标准输出(stdout)和标准错误(stderr)。

**Console**接口的使用场景大部份是同步的，只有在用于管道时，它的调用是异步的。

------

## API

### console.log

```javascript
  // console.log 源代码
  Console.prototype.log = function() {
    this._stdout.write(util.format.apply(this, arguments) + '\n');
  };
```

由上可以看出，**console.log**实质上调用的是[util.format](./util.md#utilformat)方法，在标准输出里打印返回的结果。

### console.info
` console.info = console.log `

### console.warn

```javascript
  // console.warn 源代码
  Console.prototype.warn = function() {
    this._stderr.write(util.format.apply(this, arguments) + '\n');
  };
```

由上可知，**console.warn**也是调用[util.format](./#util.md#utilformat)方法，只是在标准错误里打印返回结果。

### console.error
` console.error = console.warn `

### console.dir
**console.dir**调用[util.inspect](./util.md#utilinspect)方法，并在标准输出里打印结果。

### console.time
### console.timeEnd
这两个方法一起使用，**console.time(label)**设置一个标记时间并开始计时，**console.timeEnd(label)**终止标记时间的计时，并打印所用时间。

用法示例:
```javascript
  console.time('nodejs');
  for(var i = 0; i < 10000; i++) {}
  console.timeEnd('nodejs');
```

### console.trace
在标准错误中打印参数对应的栈信息。

### console.assert
**console.assert(expression, [message])**与**assert.ok()**函数基本一致，当表达式不为真(true)时，就会抛出一个错误。

