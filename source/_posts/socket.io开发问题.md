---
title: 使用socket.io进行websocket通信无法连接
tag:
- nodejs
- socket.io
- websocket
categories:
- 工作
---

## socket.io连接后一直polling
客户端启动后一直发送这个连接**http://127.0.0.1:8080/socket.io/?EIO=3&transport=polling&t=1496800827548-2891**
![](/images/socketio_pooling.png)
<!-- more -->
并不想使用polling,我想用websocket通信，在连接时，后面带上参数，指明websocket方式优先
```
 window.socket = io.connect('ws://127.0.0.1:8080',{transports:['websocket','polling']});
```
## 使用socket.io进行websocket通信，返回400
![](/images/socketio_400.png)

由于请求是发送到Nginx反向代理服务器，nginx需做相应配置，支持客户端和服务端保持连接,例如：
```
location /wsapp/ {
    proxy_pass http://wsbackend;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
}
```

## 参考
- [WebSocket proxying](http://nginx.org/en/docs/http/websocket.html)
- [NGINX as a WebSocket Proxy](https://www.nginx.com/blog/websocket-nginx/)
- [Hypertext Transfer Protocol -- HTTP/1.1](https://tools.ietf.org/html/rfc2616#section-13.5.1)
