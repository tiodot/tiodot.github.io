title: WebUploader使用
date: 2015-08-14 20:44:04
tags: JS
---
# 背景
最近需要封装一个图片上传组件，需要支持单文件，多文件，LOGO上传等功能，
其中LOGO上传需要检测上传图片的尺寸，并且需要支持图片裁切。改组件需要提供统一的UI样式，并有统一的错误提示。
该组件基于Webuploader封装。

<!-- more -->

# 遇坑&填坑
-  由于需要兼容IE9，故需要混合使用flash和H5上传方式，使用flash上传时，给上传按钮绑定 *click* 事件时，点击按钮不会触发事件 => 绑定*mouseup*事件。

-  使用flash上传，如果在上传完成之前隐藏上传按钮，会中断上传。而使用官网提供的解决方案，即给上传按钮添加以下class：
    ```
    .webuploader-element-invisible {
        position: absolute !important;
        clip: rect(1px 1px 1px 1px); /* IE6, IE7 */
        clip: rect(1px,1px,1px,1px);
    }
    ```
    发现IE9不好使 => 将按钮的`position`设置为absolute，然后设置一个较大的`left`属性，同时将父元素的`overflow`设置为hidden、`position`设置为relative。

-  由于可以一个上传实例绑定多个上传按钮，当上传时，需要区分是重新上传或直接上传新图片 => `file`对象的`source`属性中有个`_refer`表示该文件是哪个dom上传的。

-  LOGO上传需要裁切 => 新注册一个裁切的方法，[Webuploader裁切示例](https://github.com/fex-team/webuploader/tree/master/examples/cropper)

-  虽然WebUploader提供了文件队列管理，但不满足实际要求 => 自己再造轮子, 源码中检测文件数量的代码

    ```
        var uploader = this,
            opts = uploader.options,
            count = uploader.count,
            max = parseInt( opts.fileNumLimit, 10 ),
            flag = true;

        if ( !max ) {
            return;
        }

        uploader.on( 'beforeFileQueued', function( file ) {

            if ( count >= max && flag ) {
                flag = false;
                this.trigger( 'error', 'Q_EXCEED_NUM_LIMIT', max, file );
                setTimeout(function() {
                    flag = true;
                }, 1 );
            }

            return count >= max ? false : true;
        });

        uploader.on( 'fileQueued', function() {
            count++;
        });

        uploader.on( 'fileDequeued', function() {
            count--;
        });

        uploader.on( 'reset', function() {
            count = 0;
        });
    ```

# 参考
1. [WebUploader的官网](http://fex.baidu.com/webuploader)
