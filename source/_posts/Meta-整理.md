title: Meta 整理
date: 2016-05-17 12:28:47
tags: HTML
---

# 基本属性值

meta的意义取决其属性：

1. name： 文档级别的元数据，作用于整个页面。
2. http-equiv： 程序指令，一般用于告诉服务器页面的一些特殊要求。
3. charset： 设置页面的字符
4. itemprop: 自定义的元数据

如果设置了 `name` 或者 `http-equiv`，可以用`content`指定相关的内容

<!-- more -->

# 一 name
1. author: 定义页面的作者
    ```
    <meta name="author" content="author name" />
    ```
2. description: 页面简短精确的描述，有些浏览器会以此作为收藏时的描述，不超过150字
    ```
    <meta name="description" content="descriptions" />
    ```
3. keywords：描述页面的关键字, 多个关键字用 逗号分割
    ```
    <meta name="keywords" content="page keyword" />
    ```
4. viewport: 窗口视图大小设置，适用于移动端开发。

   content可以包含的属性：
   - width: 设置视图大小，数值/device-width
   - height： 设置视图高度，数值/device-height
   - initial-scale：初始缩放，值在0.0~10.0间
   - maximum-scale: 最大缩放比例，值在0.0~10.0间
   - minimum-scale: 最小缩放比例，值在0.0~10.0间
   - user-scalable: 布尔值，是否允许缩放页面

   ```
   <meta name="viewport" content="width=device-width, initial-scale=1, minimum-scale=1, maximum-scale=1">
   ```
5. format-detection: 格式检测
   ```
   <meta  name="format-detection" content="telephone=no" />
   <meta  name="format-detection" content="email=no" />
   ```

# 二 http-equiv
1. refresh: 页面重新加载或者重定向
    - 如果content只有一个数值，表示隔多少秒重新加载页面
    - 如果content格式是`3;url=http://www.baidu.com`表示隔3s重定向到百度

    ```
    <meta http-equiv="refresh" content="4" />
    <meta http-equiv="refresh" content="3;url=http://www.baidu.com" />
    ```

2. default-style: 页面默认样式，content是css的链接，同`<link>`的`href`保持一致

# 三 其他一下meta头

```
<!--隐藏状态栏/设置状态栏颜色, content的值为default | black | black-translucent-->
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent" />
<meta name="apple-mobile-web-app-title" content="标题">

<!-- 针对手持设备优化，主要是针对一些老的不识别viewport的浏览器，比如黑莓 -->
<meta name="HandheldFriendly" content="true">
<!-- 微软的老式浏览器 -->
<meta name="MobileOptimized" content="320">
<!-- uc强制竖屏 -->
<meta name="screen-orientation" content="portrait">
<!-- QQ强制竖屏 -->
<meta name="x5-orientation" content="portrait">
<!-- UC强制全屏 -->
<meta name="full-screen" content="yes">
<!-- QQ强制全屏 -->
<meta name="x5-fullscreen" content="true">
<!-- UC应用模式 -->
<meta name="browsermode" content="application">
<!-- QQ应用模式 -->
<meta name="x5-page-mode" content="app">
<!-- windows phone 点击无高光 -->
<meta name="msapplication-tap-highlight" content="no">

<meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1" />
<!-- 关于X-UA-Compatible -->
<meta http-equiv="X-UA-Compatible" content="IE=6" ><!-- 使用IE6 -->
<meta http-equiv="X-UA-Compatible" content="IE=7" ><!-- 使用IE7 -->
<meta http-equiv="X-UA-Compatible" content="IE=8" ><!-- 使用IE8 -->

<!-- 转码申明：用百度打开网页可能会对其进行转码（比如贴广告），避免转码可添加如下meta -->
<meta http-equiv="Cache-Control" content="no-siteapp" />
```

# 四 参考
1. [常用meta整理](http://get.ftqq.com/6483.get)
2. [MDN <meta>](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/meta#attr-content)
