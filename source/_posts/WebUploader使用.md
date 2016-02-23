title: WebUploader使用
date: 2015-08-14 20:44:04
tags:
---
最近需要上传文件，要求使用WebUploader作为上传组件。WebUploader以h5上传，同时以flash为辅，因而可以很好兼容IE系列的浏览器。同时WebUploader的支持动态添加多个上传按钮的特性表示很赞。当然还有很多其他很赞的特性，详细可以参考[WebUploader的官网](http://fex.baidu.com/webuploader)，官网api比较详细，但还是踩了不少的坑，主要是使用flash上传有比较多的问题。

<!-- more -->

-   使用flash上传时，给上传按钮绑定 *click* 事件时，点击按钮不会触发事件，因而只能退而求其次，绑定*mouseup*事件，当然绑定*mousedown*事件也是可以的。鼠标按下是触发*mousedown*， 释放鼠标时触发*mouseup*事件，而当*mousedown*和*mouseup*同时作用于同一个dom元素时就会触发*click*事件。
-   使用flash上传，如果在上传完成之前隐藏上传按钮，会中断上传。而使用官网提供的解决方案，即给上传按钮添加以下class：
```
        .webuploader-element-invisible {
            position: absolute !important;
            clip: rect(1px 1px 1px 1px); /* IE6, IE7 */
            clip: rect(1px,1px,1px,1px);
        }
```
    发现还是不行，试了试直接给上传按钮设置 `visibility` 为 hidden，对于chrome可以正常上传，但对于IE9，还是不行。因而只好将`position`设置为absolute，然后设置一个较大的`left`属性，同时将父元素的`overflow`设置为hidden、`position`设置为relative。这样就可以啦。
-   获取图片的大小和尺寸，WebUploader中的file对象，自带有size属性，因而可以直接获取上传文件的大小。但上传图片时，有时需要判断上传图片的尺寸是否满足要求。大概看了一下源码，发现在给图片生成缩略图时，WebUploader对给每一个file对象附加一个`_info`属性，这个属性包含了图片的宽度和高度，虽然直接属于私有属性，但能用就直接用吧，成大事不要拘泥于小节。file对象还有一个私有属性也很好用——`_refer`，这个属性记录了file是由那个上传按钮上传的，有多个上传按钮时，这个属性就可能减少不少代码量了。

总的来说，WebUploader很不错，毕竟是百度FEX团队发布的，质量还是有保证的，而且反馈问题，也能比较及时的收到回复。