title: ES6之Destructuring
date: 2017-02-21 11:08:01
tags: JS
---
记录不同形式Destructuring导致的不同结果：

```
function move1({x=0, y=0} = {}) {
    console.log(x, y)
}

function move2({x, y} = {x: 0, y: 0}) {
    console.log(x, y)
}
```

<!-- more -->

分别测试以下几种情况：
```
// 测试输入为空
move1();   // 0 0
move2(); // 0 0

// 测试输入{z: 3}
move1({z:3});   // 0 0
move2({z:3}); // undefined undefined

// 测试输入 {x:1, y: 2}
move1({x:1, y:2})     // 1, 2
move2({x:1, y:2})   // 1, 2
```

对于`move1`函数：

1. 如果输入为空 `''`  ==>  `{x = 0, y = 0} = {}`  ==> `x = 0, y = 0`;
2. 输入为 `{z: 3}]`  ==>  `{x = 0, y = 0} = {z: 3}`  ==> `x = 0, y = 0`;
3. 输入为 `{x:1, y: 2}` ==> `{x = 0, y = 0} = {x: 1, y: 2}` ==> `x = 1, y = 2`;

对于`move2`函数：

1. 如果输入为空 `''`  ==>  `{x, y} = {x: 0, y: 0}`  ==> `x = 0, y = 0`;
2. 输入为 `{z: 3}]`  ==>  `{x, y} = {z: 3}`  ==> `x = undefined, y = undefined`;
3. 输入为 `{x:1, y: 2}` ==> `{x, y} = {x: 1, y: 2}` ==> `x = 1, y = 2`;

主要区别就是有输入时，如果没有同名属性 `{x, y} = {x: 0, y: 0}`这种写法取不到默认值。

ps. 在ES6中如果解构对象时：

```
let {a, ...b} = {a: 1, c: 2, d:3}  // Uncaught SyntaxError: Unexpected token ...
```
但是在React中使用ES6这种写法却很常见，原因是babel([babel在线编译](http://babeljs.io/repl))增强解构功能，会将这种写法编译为：
```
function _objectWithoutProperties(obj, keys) {
    var target = {};
    for (var i in obj) {
        if (keys.indexOf(i) >= 0) continue;
        if (!Object.prototype.hasOwnProperty.call(obj, i)) continue;
        target[i] = obj[i];
    }
    return target;
}

var _a$c$d = { a: 1, c: 2, d: 3 },
    a = _a$c$d.a,
    b = _objectWithoutProperties(_a$c$d, ["a"]);
```

扩展阅读：

1. [Exploring ES6 Destructuring](http://exploringjs.com/es6/ch_destructuring.html)
