title: CSS3图标收集和整理
date: 2015-10-22 21:17:36
tags: CSS
---
## CSS3实现后退按钮，从此告别后退图标
用CSS3实现的原理是利用伪元素生成一个只有两条边的正方形，然后通过旋转一定角度实现各个方向的箭头图形。
### 90度角的箭头图形实现
```
.back1 {
    position: relative;
}
.back1:before {
    content: "";
    display: inline-block;
    width: 12px;
    height: 12px;
    left: 0;
    border-top: 2px solid #f00;
    border-left: 2px solid #f00;
    -webkit-transform: rotate(-45deg);
            transform: rotate(-45deg);
}
```

### 任意角度的箭头图形
利用伪元素before和after，根据需要可以将旋转角度适当调整，从而实现任意角度的箭头图形
```
.back2 {
    position: relative;
}
.back2:before {
    content: "";
    position: absolute;
    display: inline-block;
    width: 12px;
    height: 12px;
    left: 0;
    border-top: 2px solid #0f0;
    -webkit-transform: translate(0, -20%) rotate(-60deg);
            transform: translate(0, -20%) rotate(-60deg);
}
.back2:after {
    content: "";
    position: absolute;
    display: inline-block;
    width: 12px;
    height: 12px;
    border-left: 2px solid #0f0;
    -webkit-transform: translate(0, 20%) rotate(-30deg);
            transform: translate(0, 20%) rotate(-30deg);
}
```
当然另一种实现方式还是给border设置不同的值来实现角度的不一样。

ps: 上述的[Demo地址](/back.html)
