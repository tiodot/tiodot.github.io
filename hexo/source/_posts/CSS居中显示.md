title: CSS居中显示
date: 2015-08-24 21:18:19
tags: CSS
---
使用CSS3实现垂直左右居中显示
```
.container {
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    width: 300px;
    height: 200px;
    background: #FDFDFD;
}
```
对应的html为
```
    <div class="container"></div>
```
最主要是随着浏览器窗口变化，左右上下边距也会自动调整，因而再也不需要通过js来监听windonw的resize事件了。

当然还有一种更加简单粗暴的方法，css代码如下
```
.container {
    position: fixed;
    width: 200px;
    height: 200px;
    background: orange;
    left: 0;
    right: 0;
    top: 0;
    bottom: 0;
    margin: auto;
}
```
这种方法利用的原理是
>'margin-left' + 'border-left-width' + 'padding-left' + 'width' + 'padding-right' + 'border-right-width' + 'margin-right' = width of containing block

详细请参考[w3c标准](http://www.w3.org/TR/CSS2/visudet.html#Computing_widths_and_margins)

