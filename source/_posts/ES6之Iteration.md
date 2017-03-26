title: ES6之Iteration
date: 2017-03-23 11:00:05
tags: JS
---

探索问题：
1. 为什么ES6中数组，字符串等是可迭代的，而对象却是不可迭代？直观感受就是数组和字符串都可以用`for...of`，而对象却不可以。
2. 实现一个最小化的迭代器。

<!-- more -->
JavaScript中可以迭代的两种情景：
1. 程序层级：检测程序的结构
2. 数据层级：检测程序中所有的数据

这两个层级过于抽象，尤其是`程序层级`，可以借助js中的数组来理解：
```
const dt = [1,2,3,4]; // 数据层
dt.func = () => {}; // 程序层

for (let i in dt) console.log(xs[i]); // 1, 2, 3, 4, () => {}

for (let i of dt) console.log(i); // 1, 2, 3, 4
```
像ES5中的`for...in`可以检测对象中的所有属性，针对的大多数就是程序层级。为了解决这个问题，ES6中引入了`for...of`.

> 那既然可以用`for...in`遍历对象，为何就不能用`for...of`了？

如果设计可以迭代对象，也就意味着会混淆上面两个层级，简单的理解就是一个对象不可能仅仅只是像数组一样存储一组数据。如果混合上述两个概念，也就是说可以使用`for...of`遍历对象，意味着只能遍历对象的数据，而不能遍历其他程序层级的属性，相当于把对象当成一个数据层级来处理，这很可能会破坏代码结构。

为了解决对象不可以使用`for...of`遍历的问题，ES6中引入了一个新的概念`Map`，使用`Map`创建的key-value对就可以迭代遍历。

> 当然也可以做一个工具函数，来实现对对象的迭代。

```javascript
function objectEntries(obj) {
    let index = 0;

    const propKeys = Reflect.ownKeys(obj);

    return {
        [Symbol.iterator]() {
            return this;
        },
        next() {
            if (index < propKeys.length) {
                const key = propKeys[index];
                index++;
                return { value: [key, obj[key]] };
            } else {
                return { done: true };
            }
        }
    };
}

const obj = { first: 'Jane', last: 'Doe' };
for (const [key,value] of objectEntries(obj)) {
    console.log(`${key}: ${value}`);
}

// Output:
// first: Jane
// last: Doe
```

PS.可以思考一下为什么不扩充在Object原型链上？

# 扩展阅读
1. [Plain objects are not iterable](http://exploringjs.com/es6/ch_iteration.html#sec_plain-objects-not-iterable)
2. [Why are Objects not Iterable in JavaScript?](http://stackoverflow.com/a/38777177)