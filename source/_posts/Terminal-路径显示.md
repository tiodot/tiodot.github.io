title: Terminal 路径显示
date: 2015-12-01 14:13:37
tags: CentOS
---

查看提示符显示方式：
> echo $PS1

修改`$PS1`，打开文件 `~/.bash_profile`，增加一条规则，如果有则编辑即可：
> export PS1="\u \w$"

如果需要设置路径颜色，可以修改为：
> export PS1="[\e[33;1m\u@\h: \e[31m\W\e[0m]\$"

保存修改并退出，应用更新：
> source ~/.bash_profile

<!-- more -->

附录：
`$PS1`符号对应的含义，
```
\a    ASCII 响铃字符（也可以键入 \007）  
\d    "Wed Sep 06" 格式的日期  
\e    ASCII 转义字符（也可以键入 \033）
\h    主机名的第一部分（如 "mybox"）
\H    主机的全称（如 "mybox.mydomain.com"）
\j    在此 shell 中通过按 ^Z 挂起的进程数
\l    此 shell 的终端设备名（如 "ttyp4"）
\n    换行符
\r    回车符
\s    shell 的名称（如 "bash"）
\t    24 小时制时间（如 "23:01:01"）
\T    12 小时制时间（如 "11:01:01"）
\@    带有 am/pm 的 12 小时制时间
\u    用户名
\v    bash 的版本（如 2.04）
\V    Bash 版本（包括补丁级别） ?/td>
\w    当前工作目录（如 "/home/drobbins"）
\W    当前工作目录的“基名 (basename)”（如 "drobbins"）
\!    当前命令在历史缓冲区中的位置
\#    命令编号（只要您键入内容，它就会在每次提示时累加）
\$    如果您不是超级用户 (root)，则插入一个 "$"；如果您是超级用户，则显示一个 "#"
\xxx  插入一个用三位数 xxx（用零代替未使用的数字，如 "\007"）表示的 ASCII 字符
\\    反斜杠
\[    这个序列应该出现在不移动光标的字符序列（如颜色转义序列）之前。它使 bash 能够正确计算自动换行。
\]    这个序列应该出现在非打印字符序列之后。
```

颜色的表示方法，
```
'\e[0m'      no color
'\e[1;37m'   white
'\e[0;30m'   black
'\e[0;34m'   blue
'\e[1;34m'   light blue
'\e[0;32m'   green
'\e[1;32m'   light green
'\e[0;36m'   cyan
'\e[1;36m'   light cyan
'\e[0;31m'   red
'\e[1;31m'   light red
'\e[0;35m'   purple
'\e[1;35m'   light purple
'\e[0;33m'   brown
'\e[1;33m'   yellow
'\e[0;30m'   gray
'\e[0;37m'   light gray
```
