title: Socket.io问题分析
date: 2017-02-03 15:59:57
tags: JS
---

## 广播数据
如何向所有的客户端广播数据？

1. 使用io.sockets.emit

  ```
  ...

  var io = require('socket.io')(server);
  io.on('connection', function(socket){
      io.sockets.emit('new', {hello: 'world'});      
  });

  ```

2. 使用socket.broadcast.emit

```
...

var io = require('socket.io')(server);
io.on('connection', function(socket){
    socket.broadcast.emit('new', {hello: 'world'});      
});

```

两种方式区别`io.sockets.emit`向所有连接的客户端广播数据，而`socket.broadcast.emit`则向
除了该`socket`自身之外的其他所有广播数据。其实也就是说
`io.sockets.emit` = `socket.broadcast.emit` + `socket.emit`

因此可以猜测出socket.io内部会保留所有连接的socket。
