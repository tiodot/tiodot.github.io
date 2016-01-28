title: nodejs中的exports和module.exports
date: 2015-08-13 14:02:51
tags: nodejs
---
## exports用法
在a.js中
```
    exports.sayHello = function () {
        console.log('hello, exports....');
    };
```
然后在main.js中使用a.js
```
    var a = require('./a');
    a.sayHello();
```
结果是
```
    hello, exports....
```

如果a.js是
```
    exports = function () {
        console.log('hello, exports....');
    }
```
修改main.js以调用新的a.js
```
    var a = require('./a');
    a();
```
结果是
```
    TypeError: object is not a function
```


## module.exports用法
在b.js中
```
    module.exports = function () {
        console.log('hello, module.exports.....')
    }
```
修改main.js以使用b.js
```
    var b = require('./b');
    b();
```
结果是
```
    hello, module.exports.....
```
改造一下b.js
```
    module.exports.sayHello = function () {
        console.log('hello, module.exports.....')
    }
```
修改main.js以使用b.js
```
    var b = require('./b');
    b.sayHello();
```
结果是
```
    hello, module.exports.....
```
## exports 和 module.exports 并存
在c.js中
```
    exports.sayHello = function () {
        console.log('hello, exports....');
    };

    module.exports = function () {
        console.log('hello, module.exports.....')
    }
```
修改main.js
```
    var c = require('./c');
    c.sayHello();
```
结果是
```
    TypeError: undefined is not a function
```
修改main.js为
```
    var c = require('./c');
    c();
```
结果是
```
    hello, module.exports.....
```
## 结论
- exports是module.exports的一个引用，exports不是真实存在，而module.exports是真实存在的，因而模块导出的是module.exports。
- exports用于模块中收集属性，然后挂载在module.exports下。直接赋值给exports时，exports不再是module.exports的引用，所以访问不到exports对象极其属性了，当然如果将module.exports赋值给exports是可行的。

