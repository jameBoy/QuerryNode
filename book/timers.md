# Timers
所有定时器都是全局的，不需要使用`require`即可使用。Node中的定时器与浏览器端的大致相同，可以像浏览器端使用定时器一样在Node环境中使用。

------

## API

### setTimeout
**setTimeout(callback, delay, [arg], [...])**这个函数会创建一个在某段时间后执行一次的定时器。   

  * 第一个参数是时间到了之后要执行的代码
  * 第二个参数是延时时间
  * 可选地传入一些参数，作为回调函数的参数
  * 返回: 这个函数返回一个**Timeout**对象，它可以作为**clearTimeout**的参数，用于清除此定时器

用法示例:   
```javascript
  setTimeout(function() {
    console.log('After 500ms!');
  }, 500);
```

定时器并不一定会在指定时段时间后运行，因为Node.js的执行环境是单进程单线程的，所以，可能需要等待一些其它类似于IO事件的执行完成。

### clearTimeout
**clearTimeout(timeoutObject)**这个函数用于清除某个定时器。   
用法示例:   
```javascript
  var tr = setTimeout(function() {
    console.log('After 1000ms!');  // 这里将不会被执行
  }, 1000);
  clearTimeout(tr);    // clear immediately.
```

### setInterval
**setInterval(callback, delay, [arg], [...])**使用方法与**setTimeout**基本类似，只是其不是只执行一次，而是循环地在间隔时间后执行。    
用法示例:   
```javascript
  setInterval(function() {
    console.log('Antoher 500ms!');
  }, 500);
```

### clearInterval
**clearInterval(intervalObject)**用于清除循环定时器。   
用法示例:   
```javascript
  var tr = setInterval(function() {
    console.log('Another 500ms!');  // 这里将不会被执行
  }, 500);
  clearInterval(tr);
```
### unref
**unref()**方法用于使当事件循环队列中仅存在定时器时，程序的运行状态不受其影响。   
用法示例:   
```javascript
  var tr = setTimeout(function() {
    console.log('Unref, not exec here.');
  }, 1000);
  tr.unref();    // 程序执行到这里即退出，不会再执行定时器
```

但是，当事件队列里有其它待执行的项目时，定时器仍然与普通的一样正常执行。如:   
```javascript
  var tr = setTimeout(function() {
    console.log('After 500ms!');   // 这里仍是会执行
  }, 500);
  tr.unref();
  setTimeout(function() {
    console.log('After 1000ms!');
  }, 1000);
```   
!!!注意，**unref**方法内部会创建一个定时器并唤醒事件循环，所以使用此方法较多时可能会影响性能。

### ref
**ref()**方法用于**unref**方法对定时器的影响。


### setImmediate
**setImmediate(callback, [arg], [...])**这个方法用法与**setTimeout**相似，只是它没有延时，而且它们的调用时机不同。   
用法示例:   
```javascript
  setImmediate(function() {
    console.log('Immediately call this.');
  });
```

### clearImmediate
**clearImmediate(immediateObject)**这个方法用于清除立即定时器。


------

## Tips

### 执行顺序
Node中事件循环的执行顺序为:   
```
  process.nextTick > I/O > setImmediate > setTimeout(setInterval)
```

