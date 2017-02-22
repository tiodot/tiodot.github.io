title: 为什么需要使用setState来设置state
date: 2016-11-02 16:57:30
tags: React
---

看[React官网文档](https://facebook.github.io/react/docs/state-and-lifecycle.html)时,提及一段话

> Do Not Modify State Directly
> For example, this will not re-render a component:

> // Wrong
> this.state.comment = 'Hello';

> Instead, use setState():

> // Correct
> this.setState({comment: 'Hello'});

这里只是提及了一下编程过程如何设置state，然而并没有给出解释：
1. 为什么使用`this.state.comment`来设置`state`中的`comment`就不会重新渲染一个组件，而使用`setState`就能？
2. 如果使用`this.state.comment`来设置值，能否手动更新组件？
3. 使用`setState`设置值时，能否不让组件更新？

<!-- more -->

首先我们需要明确的一点React不是一个MVC框架，而主要注重于MVC中的V(view)层。具体可以看官网博客《[Why did we build React?](https://facebook.github.io/react/blog/2013/06/05/why-react.html)》。
可以简单的理解为React做的事情就是根据数据来自动渲染页面，类似于一个前端模板库，但提供使用JSX快速开发，事件处理，组件开发，生命周期等等功能。

简单了解React之后，是时候看看为什么推荐使用`setState`来设置值。为了解决这个疑问，写一个简单的demo，方便调试源码：
```
import React, {Component} from 'react';
import ReactDOM from 'react-dom';

class App extends Component {
    state = {
        comment: 'hello'
    };
    constructor ()  {
        super();
        // 设置一个全局的变量，方便再console中相关的api修改comment的值
        window.app = this;
    }
    render() {
        return (<div>{this.state.comment}</div>)
    }
}
ReactDOM.render(
  <App />,
  document.getElementById('root')
);
```
配合webpack-dev-server可以很方便在浏览器中看见页面。

然后直接在源码中搜索，可以发现在`ReactComponent.js`(在模块开头的注释中会有 @providesModule ReactComponent)中对`setState`方法进行了定义：
```
// 移除warning，主要代码如下
ReactComponent.prototype.setState = function (partialState, callback) {
  this.updater.enqueueSetState(this, partialState);
  if (callback) {
    this.updater.enqueueCallback(this, callback, 'setState');
  }
};
```
接下来就可以开始debug源码了。
中间依次会调用的方法有`ReactUpdates.js`的`enqueueSetState`：
```
function enqueueUpdate(component) {
	  if (!batchingStrategy.isBatchingUpdates) {
	    batchingStrategy.batchedUpdates(enqueueUpdate, component);
	    return;
	  }
      // 初始的 dirtyComponents=[];
	  dirtyComponents.push(component);
	  if (component._updateBatchNumber == null) {
	    component._updateBatchNumber = updateBatchNumber + 1;
	  }
	}
```
这里只是把需要变更的值加入到一个设置`state`的事件队列中，然后会将该组件添加到`dirtyComponents`中，
然后执行一系列优化合并state的值等，最后会执行一个`runBatchedUpdates`方法(ReactUpdate.js)：
```
function runBatchedUpdates(transaction) {
  // 全局初始化为0
  updateBatchNumber++;

  for (var i = 0; i < len; i++) {
    var component = dirtyComponents[i];
    ReactReconciler.performUpdateIfNecessary(component, transaction.reconcileTransaction, updateBatchNumber);
  }
}
```
接下来根据diff算法来决定组件是否应该更新到DOM中。

到这里大致可以明白为何使用`setState`了，`setState`包含对组件的更新操作，未完待续。感觉还是很不明白。。。
