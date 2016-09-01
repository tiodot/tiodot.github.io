title: event那些事
date: 2016-07-25 19:58:42
tags: JS
---

遇到一个问题：

    `A`标签跳转前需要做一下登录判断，如果未登录则不让跳转，使用preventDefault发现阻止不了`A`标签的跳转。

解决方法：

    加上stopImmediatePropagation才行。当然也可以把`A`标签的`href`属性设置为空，通过js来控制跳转，但改动略大些，忽视。

于是正好借此机会整理和收集一下JavaScript中的event事件。

<!-- more -->

## 事件监听方式
js中实现事件监听方式，以`click`事件为例，可以有直接设置元素的`onclick`属性：
    ```
    <!-- 直接设置元素的onclick属性 -->
    <button id="btn1">js绑定事件测试按钮1</button>

    <script>
        document.querySelector('#btn1').onclick = function (e) {
            alert('js绑定事件方式1');
        }
    </script>
    ```
    或者用`addEventListener`监听`click`：
    ```
    <!-- 用addEventListener监听click -->
    <button id="btn2">js绑定事件测试按钮1</button>

    <script>
        document.querySelector('#btn2').addEventListener('click', function (e) {
            alert('js绑定事件方式2');
        });
    </script>
    ```

    提到事件绑定，解绑事件也事件当然也可以有，具体请可以查看[Demo](/event.html)。至于像`attachEvent`这类，这里不多讨论。

    比较推荐的绑定方式是`addEventListener`, 这种方式可以给DOM元素添加不止一个事件处理函数，而且可以控制事件在捕获或冒泡阶段触发。

    PS：addEventListener可以接受三个参数，其语法是`target.addEventListener(type, listener[, useCapture]);`，第三个参数标识是在捕获阶段触发还是在冒泡阶段，默认值为`false`标识在冒泡阶段触发

## 事件捕获或冒泡
这里简要说明一下W3C的事件模型：
```
                 | |  / \
-----------------| |--| |-----------------
| element1       | |  | |                |
|   -------------| |--| |-----------     |
|   |element2    \ /  | |          |     |
|   --------------------------------     |
|        W3C event model                 |
------------------------------------------
```
假设给元素添加一下事件：
```
element1.addEventListener('click',doSomething2,true)
element2.addEventListener('click',doSomething,false)
```
当点击element2时，首先会点击事件开始于捕获阶段，触发element1的事件处理函数doSomething2，然后捕获阶段没有可执行的的事件处理函数了，接着开始进入冒泡阶段，触发element2的事件处理函数doSomething。

其他一些情况请参考[Event order](http://www.quirksmode.org/js/events_order.html)
## 事件阻止
事件阻止有三个方法： preventDefault，stopPropagation，stopImmediatePropagation。

1. preventDefault： 阻止DOM元素的默认行为，例如阻止表单`type="submit"`的button或input的提交表单行为。
2. stopPropagation： 阻止事件的传播（冒泡）。
3. stopImmediatePropagation： 阻止其他事件处理函数执行，并也会阻止事件的传播。
4. 事件处理函数中使用 `return false`：效果等价于 preventDefault + stopPropagation (据说是jQuery中实现的，原生JS中并没有什么用，测试环境是chrome 52)。

具体请查看[Demo](/event.html)

PS: 重点不是本文所写，而是以下参考的文章。

## 参考
1. [EventTarget.addEventListener](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener)
2. [PREVENTDEFAULT VS. STOPPROPAGATION VS. STOPIMMEDIATEPROPAGATION](https://codeplanet.io/preventdefault-vs-stoppropagation-vs-stopimmediatepropagation/)
3. [Remove onclick event from img tag](http://stackoverflow.com/questions/10712219/remove-onclick-event-from-img-tag)
4. [jquery: event.stopImmediatePropagation() vs return false](http://stackoverflow.com/questions/5302903/jquery-event-stopimmediatepropagation-vs-return-false)
5. [jQuery Events: Stop (Mis)Using Return False](http://fuelyourcoding.com/jquery-events-stop-misusing-return-false/)
6. [Event order](http://www.quirksmode.org/js/events_order.html)
