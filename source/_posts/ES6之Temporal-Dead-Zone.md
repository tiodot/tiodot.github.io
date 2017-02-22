title: ES6之Temporal Dead Zone
date: 2017-02-20 11:26:08
tags: JS
---
在使用ES6时，一般都是推荐使用`let, const`来定义变量，相对`var`而言：

|                | Hoisting            |  Scope      |  Creates global properties |
| :------------- | :-------------------| :---------- | :--------------------------|
| var            | Declaration         | Function    |  Yes                       |
| let            | Temporal dead zone  | Block       |  No                        |
| const          | Temporal dead zone  | Block       |  No                        |


`Temporal dead zone`这个与`var`的`Declaration`有什么不一样的地方？

<!-- more -->

先看一个例子：
```
// var定义的变量提升
console.log(tmpVar); // undefined
if (true) {
    var tmpVar = 12; // var定义的变量不是块级作用域
}
console.log(tmpVar); // 12

// let定义的变量提升
let tmp = true;
if (true) { // 新的作用域 TDZ 开始
    console.log(tmp);  // ReferenceError

    let tmp; // TDZ 结束, `tmp` 被初始化为 undefined
    console.log(tmp); // undefined

    tmp = 123;
    console.log(tmp); // 123
}
console.log(tmp); // true

```

可以查看其实`TDZ`类似于一个视觉的盲区，在作用域中应该意识到变量的存在，但却看不见，更用不了。

之所以需要`TDZ`：
1. 捕获错误： 变量提升本就比较奇怪，也不太符合变量需要定义才能使用的逻辑。
2. `const`语义要求： `const`定义一个不可以变更的常量，只能在定义时赋值，如果存在变量提升和语义不符合。为了保持一致，所以`let`也有`TDZ`。

# 相关资料
1. [9.4 The temporal dead zone](http://exploringjs.com/es6/ch_variables.html)
