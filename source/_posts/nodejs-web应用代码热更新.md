title: nodejs web应用代码热更新
date: 2016-09-01 17:23:08
tags: nodejs
---
在用nodejs开发web程序过程中, 每次修改都需要重新启动一下服务, 虽然有pm2,forever等解决方案。
在fex上看见一个比较另类的解决方案——[Node.js Web应用代码热更新的另类思路](http://fex.baidu.com/blog/2015/05/nodejs-hot-swapping/)

在这里简单实现一下来解决开发过程中热启动的问题。

首先需要重新包装一下nodejs提供的`require`方法。在require自己写的代码时,同时需要监听文件变化,当文件发生变化时,重新加载模块。

<!-- more -->

```js
// wrapperRequire.js
const chokidar = require('chokidar');

function cleanCache(modulePath) {
    var module = require.cache[modulePath];
    // remove reference in module.parent
    if (module.parent) {
        module.parent.children.splice(module.parent.children.indexOf(module), 1);
    }
    require.cache[modulePath] = null;
}

function watchFile(obj) {
    let absModulePath = require.resolve(obj.modulePath);
    chokidar.watch(absModulePath).on('all', (event, path) => {
        cleanCache(absModulePath);
        console.log(`file [${path}] --> [${event}]`);
        try {
            obj.name = require(obj.modulePath);
        } catch (ex) {
            console.error('module update failed');
        }
    });
}

module.exports = function (obj) {
    !obj.ignore && watchFile(obj);
    obj.name = require(obj.modulePath);
    return obj;
};
```
由于使用node原生的fs.watch会出现文件变化监听不到的情况（其他一些问题见[chokidar readme](https://github.com/paulmillr/chokidar)）,
所以这里使用`chokidar`来实现对文件的监听。

使用`wrapperRequire.js`
```js
// main.js
const wrapperRequire = require('./require');
let code = wrapperRequire({modulePath: './code', 'name': ''});
setInterval(function () {
    console.log(code.name);
}, 1000);


```
测试文件 `code.js`
```
module.exports = 'hello world';
```

执行`main.js`然后更改`code.js`后也可以展示正确的结果。


总结一下,以上实现的基础是:

1. nodejs可以对require缓存的文件进行清空
2. 监听文件变化,然后重新require文件
3. js函数中传递的Object参数是引用传递

参考:
1.[Node.js Web应用代码热更新的另类思路](http://fex.baidu.com/blog/2015/05/nodejs-hot-swapping/)
2.[chokidar](https://github.com/paulmillr/chokidar)
