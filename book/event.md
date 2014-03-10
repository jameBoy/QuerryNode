# EventEmitter
Node.js是一个事件驱动的平台，事件在Node.js中是一个非常重要的概念。

------

### API
我们这里先来看看Node.js中的事件API。   

1. addListener(event, listener)  
    这个和下面的**on**方法是一样的，同样的功能，只是名字不同而已。   
2. on(event, listener)   
    为某个事件添加监听器，它会触发**`newListener`**事件。  
    @params {String} event 必需， 事件名。  
    @params {Function} listener 必需, 要添加的监听函数。  
    @return this; 返回一个调用自身的**emitter**对象，这样可以链式地操作。   
3. once(event, listener)   
    这个方法的与第二个方法功能类似，但是它只是会在事件触发时候执行一次。当相同的事件再次触发时，它将不会再调用。   
    @params {String} event 必需， 事件名。   
    @params {Function} listener 必需, 要添加的监听函数。   
    @return this; 同**on**方法的返回值。
4. removeListener(event, listener)   
   这个方法是用来移除某个事件上的特定监听器。当这个方法执行时，会触发**`removeListener`**事件。   
   @params {String} event 必需， 事件名。   
   @params {Function} listener 必需, 要移除的监听函数。   
   @return this; 同上。   
5. removeAllListeners([event])
   这个方法用来移除某个事件上所有的监听器。如果没有给予参数名，则移除所有事件的监听器。   
   @params {String} event 非必需， 事件名。   
   @return this; 同上。   
6. setMaxListeners(n)   
   这个方法用来设置一个事件上最大监听器数量。   
   @params {Number} n 必需   
   @return this; 同上。   
7. listeners(event)   
   @params {String} event 必需，事件名   
   @return {Array} 返回事件上的所有监听器。   
8. emit(event, [args1], [args2], [args3])   
   触发事件。   
   @params {String} event 必需，事件名   
   @params {*} arguments 非必需， 参数   
   @return {Boolean} 如果有监听器，则为真。如果没有，则为假。   
9. EventEmitter.ListenerCount(emitter, event)   
   @params {EventEmitter} emitter 非必需， EventEmitter实例。   
   @params {String} event 非必需， 事件名。   
   @return {Number} 如果没有参数或只有一个参数，则返回0； 否则返回事件对应的监听器数量。   
10. 两个事件 `newListener` 和 `removeListener`   

------

### 原理
关于Node.js中事件监听器的原理，其简单模型可参看[seajs](https://github.com/seajs/seajs/blob/master/src/util-events.js)的事件处理。   

------

### Tips
如果一个继承了**EventEmitter**的类的对象，使用` emit('error') `触发了一个错误事件，但没有监听者，将会抛出一个异常。这样会产生一个**uncaughtException**异常冒泡到[process](./process.md)对象。   
   
   



