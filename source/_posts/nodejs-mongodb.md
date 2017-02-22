title: nodejs mongodb
date: 2016-11-29 20:01:09
tags: nodejs
---
记录一下nodejs配置mongodb的过程：

1. mac上安装mongodb:
  ```
    brew install mongodb
  ```

2. 创建一个存在数据目录
  ```
    mkdir -p ~/data/db
  ```

3. 在用户根目录运行mongodb，指定数据存在路径为刚刚新建的目录
  ```
    mongod --dbpath=./data/db
  ```
  <!-- more -->

4. 安装nodejs驱动程序mongodb和mongoose
  ```
    npm install mongoose mongodb
  ```

5. 连接mongodb：
  ```
    const mongoose = require('mongoose');

    mongoose.connect('mongodb://localhost/todo');

    mongoose.connection.on('error', err => {
        console.error(err);
    }).on('open', () => {
        console.log('connected');
    });
  ```

其他：

mongodb可视化软件：[mongobooster](https://mongobooster.com/) 没有找到破解版，而且价格略贵
