title: nodejs使用shebang
date: 2016-09-30 10:19:20
tags: nodejs
---
# 基本含义
`shebang`也可以称之为`hashbang`, 代指`#!`。一般出现在脚本的第一行，用于在unix系统或类unix系统中指定默认的脚本文件的解释程序。
其语法格式是：

>#!interpreter [optional-arg] // 解释程序的绝对路径和可选参数

1. 如果脚本文件中没有`#!`这一行，执行时会以系统默认的解释程序允许
2. 如果`#!`之后的解释程序是一个可执行文件，那么执行这个脚本时，它就会把文件名及其参数一起作为参数传给那个解释程序去执行。

<!-- more -->

# 示例
在我的电脑中通过`nvm`安装了两个node版本。一个是4.4.5版本，一个是6.2.0版本。

1. 新建一个脚本文件--`testShebang`，通过`shebang`指定其默认node版本为4.4.5
```
#! /Users/baidu/.nvm/versions/node/v4.4.5/bin/node
// 打印node的版本
console.log(process.version)
```

2. 给`testShebang`添加可执行权限
```
$ chmod 755 testShebang
```

3. 切换终端的node版本为6.2.0。
```
$ node -v
v6.2.0
```

4. 指定node执行该文件
```
$ node testShebang
v6.2.0
```

5. 直接执行
```
$ ./testShebang
v4.4.5
```

# 参考
1. [wikipedia: Shebang (Unix)](https://en.wikipedia.org/wiki/Shebang_(Unix))
2. [释伴：Linux 上的 Shebang 符号(#!)](https://linux.cn/article-3664-1.html)
