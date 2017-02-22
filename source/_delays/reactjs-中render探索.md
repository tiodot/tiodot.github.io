title: reactjs 中render探索
date: 2016-10-27 10:09:23
tags: React
---
最近在学习Reactjs，对其生命周期比较感兴趣。用React写项目时，必须得将React组件绑定到页面元素上，一般做法是:

```
ReactDom.render(<App />, document.getElementById('#app'));
```
这段代码是不是很熟悉，但这里面究竟发生了些什么了？

<!-- more -->

首先使用`npm`下载React、ReactDom, 在`react/lib`目录下有React相关的所有源码，这里为什么不用React的github源码研究了？主要看了一下github上的源码，目录分的太多，而且源码之间的`require`都没有写具体路径，找相关文件比较费劲，而React的`npm`模块其实就是github源码build之后代码，变动不大，而且所有代码都再同一个目录下了。有代码之后，就可以开始探索探索了。

## 一、找到`react-dom`模块
其主要代码为:
```
module.exports = require('react/lib/ReactDOM');
```
啥也没干，只是引用和导出了`react/lib/ReactDOM`

## 二、找到`react`模块，查看`lib/ReactDOM.js`
其导出的api有：
```
var ReactMount = require('./ReactMount');

var ReactDOM = {
  findDOMNode: findDOMNode,
  render: ReactMount.render,
  unmountComponentAtNode: ReactMount.unmountComponentAtNode,
  version: ReactVersion,
  ....
};
```
这里还是没有干啥，只是简单的组合包装了一下其他模块提供的方法，然后导出。

## 三、查看`lib/ReactMount.js`

### 1. `ReactMount.render`方法
查找`render`方法：
```
  /**
   * Renders a React component into the DOM in the supplied `container`.
   * See https://facebook.github.io/react/docs/top-level-api.html#reactdom.render
   *
   * If the React component was previously rendered into `container`, this will
   * perform an update on it and only mutate the DOM as necessary to reflect the
   * latest React component.
   *
   * @param {ReactElement} nextElement Component element to render.
   * @param {DOMElement} container DOM element to render into.
   * @param {?function} callback function triggered on completion
   * @return {ReactComponent} Component instance rendered in `container`.
   */
  // render真身
  render: function (nextElement, container, callback) {
    return ReactMount._renderSubtreeIntoContainer(null, nextElement, container, callback);
  },
```
终于找到这货的真身了。这货主要是渲染一个组件到页面中，如果已经渲染则更新组件。提供了三个参数：
- nextElement: 需要渲染的组件,
- container: 页面元素(就是这个 document.getElementById('#app'))
- callback: 完成之后的回调

具体实现继续往下看`ReactMount._renderSubtreeIntoContainer`又干了些啥。

### 2. `ReactMount._renderSubtreeIntoContainer`方法

研究`ReactMount._renderSubtreeIntoContainer`, 因为这里只关注首次ReactDom.render发生了些什么，
可以精简一下源码:
```
_renderSubtreeIntoContainer: function (parentComponent, nextElement, container, callback) {
    var nextWrappedElement = ReactElement(TopLevelWrapper, null, null, null, null, null, nextElement);
    var nextContext = {};

    // 返回一个documentElement
    var reactRootElement = getReactRootElementInContainer(container);
    // 如果DOM元素已经被渲染过组件了，会有一个data-reactid 如果没有则为空
    var containerHasReactMarkup = reactRootElement && !!internalGetID(reactRootElement);
    // 标识DOM元素渲染组件是不是React根元素（简单的可以认为是不是 App 组件）
    var containerHasNonRootReactChild = hasNonRootReactChild(container);

    // 首次渲染时，shouldReuseMarkup为false，因为containerHasReactMarkup为false
    var shouldReuseMarkup = containerHasReactMarkup && !containerHasNonRootReactChild;
    var component = ReactMount._renderNewRootComponent(nextWrappedElement,
        container, shouldReuseMarkup, nextContext)._renderedComponent.getPublicInstance();
    if (callback) {
      callback.call(component);
    }
    return component;
}
```
这里又引入一下新的新的模块像 `ReactElement`(其引用的是`require('./ReactElement')`), 和一些其他全局方法。

`TopLevelWrapper`生成一个唯一的id来标识组件，便于组件更新:
```
var topLevelRootCounter = 1;
var TopLevelWrapper = function () {
  this.rootID = topLevelRootCounter++;
};
TopLevelWrapper.prototype.isReactComponent = {};
TopLevelWrapper.prototype.render = function () {
  // this.props is actually a ReactElement,
  // 这里对应其实就是 _renderSubtreeIntoContainer 中的 nextElement
  return this.props;
};
```

`ReactElement`做的就是返回一个`element`, 其主要代码就是这些：
```
var ReactElement = function (type, key, ref, self, source, owner, props) {
  var element = {
    // This tag allow us to uniquely identify this as a React Element
    $$typeof: REACT_ELEMENT_TYPE,

    // Built-in properties that belong on the element
    // type保存就是一个 TopLevelWrapper 构造函数
    type: type,
    key: key,
    ref: ref,
    // 这里保存的是 nextElement
    props: props,

    // Record the component responsible for creating this element.
    _owner: owner
  };
  return element;
};
```

### 3.`ReactMount._renderNewRootComponent`方法：
附上源码：
```
_renderNewRootComponent: function (nextElement, container, shouldReuseMarkup, context) {
    // 这个nextElement已经是被ReactElement包装过一层的element的了
    // 返回一个将被挂载到DOM元素的ReactNode实例
    var componentInstance = instantiateReactComponent(nextElement, false);

    // 挂载ReactNode到Dom
    batchedMountComponentIntoNode(componentInstance, container, shouldReuseMarkup, context);

    return componentInstance;
  },
```

该方法关键在于`batchedMountComponentIntoNode`这个方法，该方法为：
```
function batchedMountComponentIntoNode(componentInstance, container, shouldReuseMarkup, context) {
  // 本来这里有个transaction的，需要传递的，用null代替一下，重点不是这个。
  mountComponentIntoNode(componentInstance, container, null, shouldReuseMarkup, context);
}
```
调用具体的挂载ReactNode到Dom元素方法`mountComponentIntoNode`:
```
function mountComponentIntoNode(wrapperInstance, container, transaction, shouldReuseMarkup, context) {

  // 生成一段html，里面逻辑较多，略过。
  var markup = ReactReconciler.mountComponent(wrapperInstance, transaction, null, ReactDOMContainerInfo(wrapperInstance, container), context, 0 /* parentDebugID */
  );

  ReactMount._mountImageIntoNode(markup, container, wrapperInstance, shouldReuseMarkup, transaction);
}
```

### 4.`ReactMount._mountImageIntoNode`方法
结局已经很近了，是否看看还有什么花招：
```
_mountImageIntoNode: function (markup, container, instance, shouldReuseMarkup, transaction) {
    // 关键就是设置html
    setInnerHTML(container, markup);
}
```

然后再看看`setInnerHTML`这个方法都需要干些啥:

```
var setInnerHTML = function (node, html) {
    node.innerHTML = html;
};
```
聪明的你，看到开头就应该猜中需要使用`innerHTML`来设置html吧，但是有没有觉得还是没明白ReactDom.render到底都干了些啥。我也还不是不太懂，这里只是探究了一下render的流程。关键点如何转换一个React Component为html标签。待研究。。。

## 四、总结
ReactDom.render执行过程大致为：

Component -> ReactElement -> ReactNode -> mountComponent -> html markup -> set innerHTML
