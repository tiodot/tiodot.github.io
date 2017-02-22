title: Angular 小技巧
date: 2016-09-27 09:35:35
tags: Angular
---

# 1.`textarea`的`placeholder`换行

问题：placeholder中的文本过长，或者需要格式化展示文本时，需要使用多行展示。
解决方案：使用angular中的数据绑定就可以轻松实现
```
<textarea rows="6" cols="45" placeholder="{{'第一行\n第二行\n第三行\n第四行'}}"></textarea>
```

解决方案来源：[Can you have multiline HTML5 placeholder text in a textarea?](http://stackoverflow.com/a/38128759)
