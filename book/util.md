# util
**util**模块提供了一系列工具类函数，需要` require('util') `来使用它。

------

## API
1. [util.inherits](#utilinherits)
2. [util.format](#utilformat)
3. [util.inspect](#utilinspect)
4. [util.log](#utillog)
5. [util.isArray](#utilisarray)
6. [util.deprecate](#utildeprecate)
7. ...

------

### util.inherits
**inherits(constructor, superConstructor)**Node对JavaScript继承的一个封装。

参数解析:
```
  * constructor {Object}       当前构造函数
  * superConstructor {Object}   父类构造函数
```

用法示例:
```javascript
  var Event = require('event').EventEmitter;
  var MyClass = function() {
    Event.call(this);
  };
  util.inherits(MyClass, Event);
```  

因为JavaScript原型继承很灵活，但很多实现都有自己各自的问题。这种方式是比较好的一种JavaScript使用继承的方式，推荐如上使用。   

### util.format
**util.format(format, [...])**被设计用来像C语言的**printf**函数风格，但它不输出而是返回格式化后的字符串。

参数解析:
```
  * format {String}              格式化模板
    - %s  ---  字符串
    - %d  ---  数字
    - %j  ---  JSON文本
  * ... {String|Number|JSON|%}   待替换的参数
```   
   
用法示例:   
```js
  var util = require('util');
  var obj = { x: 3 };
  console.log('obj: ' + obj);           // 输出: obj:[object Object]
  console.log('obj', obj);              // 输出: obj: { x: 3 }
  console.log('obj.x = %d', obj.x);     // 输出: obj.x = 3
  console.log(util.format('obj', obj)); // 输出: obj: { x: 3 }
  console.log(uti.format('obj.x = %d', obj.x))  // 输出: obj.x = 3
```
**console.log**方法其实就是调用的这个方法来进行参数处理。
唯一不同之处是**console.log**是输出，而**util.format**是返回格式化后的结果。   

### util.inspect
` util.inspect(object, options) `是一个检测函数，对于JavaScript的基本类型，会直接返回。
而对于对象类型，会检测其属性，并全部拼接成一个字符串返回。它有一个可选的参数**options**，用来描述检测和返回状态的一些条件。   
**util.inspect**是**util.format**的基础。

参数解析:
```
  * object {Object}         待检测的参数
  * options {Object}        参数是一个对象，它有如下几个属性:
    - showHidden {Boolean}    默认为false; 如果为true的话，即使不可枚举的属性也会列出来。
    - depth {Number|null}     默认为2, 递归深度。可以设置为null, 即所有子属性都列举出来。
    - colors {Boolean}        默认为false, 如果改为true的话, 就会用ANSI颜色将返回字符串样式化。
    - customInspect {Boolean} 默认为true, 是否使用自定义的**inspect**函数。如果为false，则强制使用util的。
```

用法示例:   
```js
  var util = require('util');
  var result = util.inspect(util, { showHidden: true, depth: null });
  console.log(result);
```
   
### util.log
` util.log(str) `只是一个简单的对` console.log() `的封装，在输出的前面添加了时间缀。   

用法示例:   
```js
  var util = require('util');
  util.log('Hello world!');
```

### util.isArray
` util.isArray `相当于` Array.isArray `的一个别名，用来判断一个对象是否为数组。   
类似的函数还有:

```
  util.isBoolean();         // 判断是否为布尔类型
  util.isNull();            // 判断是否为空
  util.isNullOrUndefined()  // 判断是否为空或者未定义
  util.isNumber();          // 判断是否为数字
  util.isString();          // 判断是否为字符串
  util.isSymbol();          // 判断是否为符号
  util.isUndefined();       // 判断是否为未定义
  util.isRegExp();          // 判断是否为正则表达式
  util.isObject();          // 判断是否为对象
  util.isDate();            // 判断是否为日期对象
  util.isError();           // 判断是否为错误
  util.isFunction();        // 判断是否为函数
  util.isPrimitive();       // 判断是否为基本类型
  util.isBuffer();          // 判断是否为Buffer类型
```

### util.deprecate
` util.deprecate `用来说明一个函数是被废弃的状态，不应该再被使用或者应该使用其它函数代替。

用法示例:
```js
  var util = require('util');
  var abc = util.deprecate(function() {
    console.log('adc');
  }, 'Method abc() has deprecated, please use def() instead!');
```

