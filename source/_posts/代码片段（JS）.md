title: 代码片段（JS）
date: 2015-12-11 17:31:08
tags: snippet
---

#获取随机字符
###获取26个字母与10个数字之间的随机字符方法
```
var randstr = Math.random().toString(36).slice(2);
var len = randstr.length;
var r = randstr.charAt(~~(len*Math.random()))
```

###获取某一个区间中的随机字符，利用unicode实现
```
String.fromCharCode(0x2200 + (0x220f - 0x2200 + 1) * Math.random());
```

PS: 数字的unicode范围为 0030--0039；英文小写字母的范围为，0061--007a