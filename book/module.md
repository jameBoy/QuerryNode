# Module
Node有一个简单易用的模块加载系统。在Node中，文件和模块是一一对应的关系，即一个文件就是一个模块。


---

## API 

### module
在每一个模块里，都有一个**module**变量，其表示当前模块。**module**不是一个全局变量，但是在任何一个模块里都可以使用它。


### module.exports  &&  exports
**module.exports**是**module**的一个属性，而**exports**是**module.exports**的一个引用。根据JavaScript的引用原理，当其中一个改变了被引用对象的属性时，也会反映到另外一个引用上去。但是当其中一个引用指向发生改变时，两者就不再指向同一个引用，因而也不会互相影响。   
在使用的过程中，注意配合好使用**exports**和**module.exports**。参见下面[exports = module.exports](#exports_=_module.exports_=)。

### module.require
**module.require(id)**是通过某一模块来请求其它模块的方法。它需要相应模块明确的将自身的**module**变量导出。   
用法示例:   
```javascript
  // a.js
  exports.getModule = function() {
    return module;
  };

  exports.getChildren = function() {
    console.log(module.children);
  };

  // b.js
  var a = require('./a');
  var ma = a.getModule();
  a.getChildren();        // []
  ma.require('./c');
  a.getChildren();        // [ { ... } ]

  // c.js
  console.log('module c is required!');
```   
在上面的示例中，是通过a.js模块的**module**变量来**require**模块c.js，从而执行。

### module.id
### module.filename
**module.id**与**module.filename**返回的都是解析后完整的模块文件路径。但是，有一点不同的是，在入口模块里，**module.id**返回的是` . `这个符号，而不是路径。

### module.loaded
**module.loaded**返回的是一个布尔值，其代表模块已经加载完成与否。

### module.parent
**module.parent**返回的是一个对象，即当前模块的父模块(上一级)的信息。

### module.children
**module.children**返回当前模块使用**require**加载的所有模块信息的一个数组。

------

## Tips

### exports = module.exports =
为什么使用` exports = module.exports = SomeObject `呢？见下例:   
```javascript
  // file: a.js
  var a = {
    name: 'node'
  };
  module.exports = a;
  exports.age = 5;
```   
当在另一个文件里作为了个模块**require**的时候，   
```javascript
  var a = require('./a.js');
  console.log(a);    // { name: 'node' }
```
因为**exports**其实是一个局部变量，它是**module.exports**的一个引用。当将**module.exports**引用指向它处时，**exports**引用和其不再是同一个引用，因而对其的任何改变不会再影响到**require**的结果。
   
### require.main
**require**有一个属性**main**，只有在以` node xxx.js `方式直接执行的JS文件中，才会有` require.main === module `。而且，由于**require.main**属性是一个**Module**对象，所以使用**require.main.filename**可以获取入口文件名。


------

## 总结

### require(X)的加载
在路径Y下加载

  1. 如果**X**是一个核心模块   
    直接返回核心模块
  2. 如果**X**是以` ./ 或者 ../ 或者 / ` 开头
    a. 以文件形式加载` (Y + X) `
    b. 以目录形式加载` (Y + X) `
  3. 加载**node_modules**目录下的模块
  4. 如果没找到，则抛出一个` MODULE_NOT_FOUND `的错误
   
以文件形式加载:   

  1. 如果**X**是一个文件，则将其当作一个普通的JavaScript文本文件加载**X**
  2. 如果**X.js**是一个文件，以普通JavaScript文本文件形式加载**X.js**
  3. 如果**X.json**是一个文件，则以JSON格式形式加载文件**X.json**
  4. 如果**X.node**是一个文件，则以二进制形式加载**X.node**
   
以目录形式加载:   

  1. 如果目录**X/package.json**存在，并且有**main**域，则加载**main**对应的值
  2. 如果**X/index.js**存在，则以JavaScript文本文件形式加载它
  3. 如果**X/index.node**存在，则以二进制形式加载它
   
从**node_modules**目录中加载   
> Node首先会检查执行当前模块的目录下是否有**node_modules**目录，如果有，则在其里面以目录的形式加载相应的模块。
> 如果没有找到，则在前上一级目录中重复上面的步骤。这样直到找到相应模块或者直到根目录也没找到而抛出错误。
使用**node_modules**时，Node会在其它路径都没找到的情况下，额外查找下面几个目录:   

 1. \$HOME/.node_modules
 2. \$HOME/.node_libraries
 3. \$PREFIX/lib/node

之所以会额外查找以上三个目录，是由于历史遗留出于兼容性考虑。但在使用中，最好是将依赖的模块放在本地，这样更可靠的同时加载速度也更快。



