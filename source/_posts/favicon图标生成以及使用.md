---
title: favicon图标生成以及使用
date: 2023-01-10 10:58:36
tags: HTML
---

![](/img/2023-01-09-20-29-47.png)

最近搞一个 chrome 插件，涉及到网站 favicon 图标替换，于是乎看了解下`.ico`格式的图片文件。

<!-- more -->

## favicon 使用概述

favicon 图标主要是用于浏览器标签栏，同时在书签栏也会显示，如上截图所示。favicon 图标可以通过 `link`标签指定，如果页面中没有该标签，浏览器会使用当前访问域名自动拼接一个 favicon 链接然后尝试获取。

`link`标签形式：

```
<link rel="shortcut icon" type="image/x-icon" href="//res.wx.qq.com/a/wx_fed/assets/res/NTI4MWU5.ico">
```

如果页面包含这个，会自动访问下面这个链接获取对应的图标：

```
https://{domain}/favicon.ico
```

如果访问的链接不是一个图标，浏览器会默认的一个 favicon 图标
![](/img/2023-01-09-20-40-18.png)

当然也可以使用 `png`, `svg` 格式的图标

使用`png`图标，可以设置多个并指定不同的大小，一般默认的是 32x32 尺寸，尺寸都需要是正方形，也就是长度和亮度需要是一致的：

```
<link rel="icon" type="image/png" href="/favicon-32.png" sizes="32x32">
<link rel="icon" type="image/png" href="/favicon-64.png" sizes="64x64">
...
```

使用`svg`也是类似的，因为 svg 是矢量图，故可以不用指定大小：

```
<link rel="icon" href="/favicon.svg"/>

```

设置也可以使用 base64：

```
<link rel="icon" href="data:image/x-icon;base64,AAABAA...8AAA==" />
```

或者内联的 svg 图标：

```
<link rel="icon" href="data:image/svg+xml;charset=utf-8,<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" width="32" height="32">
      <rect width="100%" x="0" y="12px" height="20px" fill="red" />
      <text x="3px" y="28px" fill="black" font-weight="bold">ABC</text>
      </svg>" />

```

如果是浏览器直接打开 html 文件，可以考虑使用内联这种方式。

## .ico 文件简介

`.ico`格式图标文件和 PNG 的图标有些不一样，`.ico`文件可以包含一组图标，最初的标准是 16x16，32x32，48x48 等，目前现代浏览器大多数都是 32x32 尺寸的图片了。

为了支持多个图片集合，`.ico`文件通过头部信息标识图标类型&包含的图片数量，每一个图片数据前面 16 Bytes 会指定图片的一些信息，具体如下所示（来自： https://en.wikipedia.org/wiki/ICO_(file_format)）：
![](/img/2023-01-10-10-02-02.png)

以一个 GitHub 的 favicon 图标为例：
![](/img/2023-01-10-10-16-58.png)

对应的二进制文件内容格式解释一下：

```
00 00 01 00 02 00 // .ico图标的头信息，主要是是type和包含的数量
10 10 00 00  // 第一个张图片的大小
01 00 20 00  // (待研究啥作用）
28 05 00 00  // 第一张图片数据的大小，采用小字节序，计算大小时可以简单认为需要倒转 00 00 05 28 => 1320 (js 可以使用 parseInt('0528', 16)获取对应的10进制的数)
26 00 00 00 // 图片的偏移量 38 （如果只有一张图片应该是22，两张38，三张，6 + 16*3 = 54）
20 20 00 00 // 第二张图片的大小为 32 * 32
01 00 20 00  // 颜色，
28 14 00 00 // 图片大小为 5160 Bytes = 5KB左右
4e 05 00 00 // 图片偏移量 1358 （第一张图片大小+偏移量）
28 00 00 00 10 00 00 00 20 00 00 00.... // 第一张图片的内容数据
```

对应前端而言，可以通过一些统计生成和解析`.ico`文件：

1. 解析 https://github.com/LinusU/decode-ico
2. 生成 https://github.com/datvm/PNG2ICOjs

但目前发现生成的 `.con`包含的图片都是 bpm 格式文件，而不是 png 的，后续有问题在研究研究如何在 `.ico` 包含 png 图片。

## favicon 图标实践

最好在根域名目录下放一个 `favicon.ico` 文件，如果页面没有指定 favicon 图标，浏览器会默认加载该文件。如果页面添加 favicon 图标，最好还是通过 png 形式指定，类似：

```
// 来源：https://www.zhangxinxu.com/wordpress/2019/06/html-favicon-size-ico-generator/
<!-- place this in your <head></head> -->
<link rel="shortcut icon" href="/favicon.ico">
<link rel="icon" sizes="16x16 32x32 64x64" href="/favicon.ico">
<link rel="icon" type="image/png" sizes="196x196" href="/favicon-192.png">
<link rel="icon" type="image/png" sizes="160x160" href="/favicon-160.png">
<link rel="icon" type="image/png" sizes="96x96" href="/favicon-96.png">
<link rel="icon" type="image/png" sizes="64x64" href="/favicon-64.png">
<link rel="icon" type="image/png" sizes="32x32" href="/favicon-32.png">
<link rel="icon" type="image/png" sizes="16x16" href="/favicon-16.png">
<link rel="apple-touch-icon" href="/favicon-57.png">
<link rel="apple-touch-icon" sizes="114x114" href="/favicon-114.png">
<link rel="apple-touch-icon" sizes="72x72" href="/favicon-72.png">
<link rel="apple-touch-icon" sizes="144x144" href="/favicon-144.png">
<link rel="apple-touch-icon" sizes="60x60" href="/favicon-60.png">
<link rel="apple-touch-icon" sizes="120x120" href="/favicon-120.png">
<link rel="apple-touch-icon" sizes="76x76" href="/favicon-76.png">
<link rel="apple-touch-icon" sizes="152x152" href="/favicon-152.png">
<link rel="apple-touch-icon" sizes="180x180" href="/favicon-180.png">
```

可以通过 https://realfavicongenerator.net/ 生成 `.ico`文件文件，网站不广告，兼容多种平台。

参考文章：

1. https://www.zhangxinxu.com/wordpress/2019/06/html-favicon-size-ico-generator/
2. https://austingil.com/svg-favicons/
3. https://en.wikipedia.org/wiki/ICO_(file_format)
