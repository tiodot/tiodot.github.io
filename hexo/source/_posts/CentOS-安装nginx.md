title: CentOS 安装nginx
date: 2015-11-10 08:44:23
tags: CentOS
---
### 安装EPEL
    sudo yum install epel-release

### 安装nginx
    sudo yum install nginx

### nginx常用操作
启动

    sudo /etc/init.d/nginx start

关闭

     sudo /etc/init.d/nginx stop
     //或者
     nginx -s stop
     //或者
     nginx -s quit

重启

     sudo /etc/init.d/nginx restart

重新载入（修改配置文件之后）

     sudo /etc/init.d/nginx reload



