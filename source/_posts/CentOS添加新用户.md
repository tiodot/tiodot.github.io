title: CentOS添加新用户
date: 2015-11-09 10:16:42
tags: CentOS
---

### 添加新用户
    adduser username

### 修改新用户密码
    passwd username

<!-- more -->

### 添加sudo权限
修改权限相关的配置文件，进入`/etc`

    vi sudoers

找到以下内容

     ## Allow root to run any commands anywhere
    root ALL=(ALL) ALL

新增一行，增加一下内容

     username ALL=(ALL) ALL

按下`esc`退出编辑模式，执行以下命令，保存退出
     
     :wq

### 切换到新用户
    su username

