# Assert
**assert**模块主要是用于编写应用的单元测试。使用它时，需要` require('assert') `。

------

## API

### assert.fail
**fail(actual, expected, message, operator)**这个方法会抛出一个**AssertionError**类型的错误，如果参数里`message`存在且不为空，则打印打。否则，使用`operator`分割符分割`actual`和`expected`这两个参数值，并打印。   
用法示例:   
```javascript
  var assert = require('assert');
  assert.fail('actual', 'expected', 'actual not equal expected.', '//');
  //assert.fail('actual', 'expected', null, '//');
```

### assert && assert.ok
**assert = assert.ok(value, [message])**方法用于判断`value`是否为真，如果不为真，则调用**assert.fail**方法。如果为真，则什么也不做。
```javascript
  var assert = require('assert');
  assert(false, 'valuse is false.');
  assert.ok(false);
```

### assert.equal
**assert.equal(actual, expected, [message])**与上面的**assert.ok**相似。如下所示:   
```javascript
  var value = (actual == expected);
  assert(value);  // == assert.equal(actual, expected);
```

### assert.notEqual
**assert.notEqual(actual, expected, [message])**相当于` var value = (actual != expected); `。

### assert.deepEqual
**assert.deepEqual(actual, expected, [message])**使用更加严格的比较`actual`和`expected`，如下:   

  1. 如果两者都是基本类型，使用` === `进行比较
  2. 如果两者都是Buffer类型，逐个进行值比较
  3. 如果两者都是Date类型，比较两者毫秒值
  4. 如果两者都是正则类型，按正则类型比较
  5. 如果两者都是对象，使用` == `进行比较
  6. 如果不是上述类型，使用一些更复杂的比较方式

### assert.notDeepEqual
**assert.notDeepEqual(actual, expected, [message])**对上面的**deepEqual**结果取反。

### assert.strictEqual
**assert.strictEqual(actual, expected, [message])**完全使用` === `进行比较。

### assert.notStrictEqual
**assert.notStrictEqual(actual, expected, [message])**完全使用` !== `进行比较。

### assert.throws
**assert.throws(block, [error], [message])**这个方法期望`block`抛出一个错误，可以选择错误类型、自定义错误信息。   
用法示例:   
```javascript
  var assert = require('assert');
  assert.throws(function() {
    throw new Error('error');
  });
```

### assert.doesNotThrow
**assert.doesNotThrow(block, [message])**这个方法期望`block`执行时不抛出一个错误。


### assert.ifError
**assert.ifError(value)**当`value`为真时，抛出它。

