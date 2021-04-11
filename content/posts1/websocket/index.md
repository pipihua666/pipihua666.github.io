---
title: react使用websocket通信
date: "2021-02-20 21:45"
description: react使用websocket通信
---

#### 浏览器自带的 api：（如果是 https 则采用 wss）

```javascript
// 这里的转发标识为/ws
const wsPath = `ws://${window.location.host}/ws/system`
const socket = new WebSocket(wsPath)
socket.addEventListener("open", () => {
  // send可以传递字符串或者二进制
  socket.send(JSON.stringify({ page: 1, size: 10 }))
})
socket.addEventListener("message", this.handleMessage)
socket.addEventListener("close", () => {
  socket.removeEventListener("message", this.handleMessage)
  socket.close()
})
```

<a name="0BRMq"></a>

#### 开发环境配置：

```javascript
devServer: {
      proxy: {
        // 普通http请求
        '/api': {
            target: 'http://localhost:8099/',
            changeOrigin: true,
            secure: false
        },
      // websocket请求配置
        '/ws': {
          target: 'ws://localhost:8099/',// 可以写http或者ws开头
          ws: true, // 代理使用websokcet协议
          secure: false,
          changeOrigin: true
        }
}
```

<br />

<a name="UDtcL"></a>

#### 生产环境配置：

```nginx
// 这里是websocket相关的配置
location /api/ws {
            proxy_pass http://192.168.233.238:8090;
            proxy_next_upstream error timeout invalid_header http_500 http_502 http_503;
            proxy_set_header Host $host;
            proxy_http_version 1.1;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto https;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "upgrade";
            proxy_set_header Origin "";
            proxy_redirect off;
        }
```

<br />

#### 我的 nginx 配置：

```nginx
location ~ /ws/ {
  rewrite ^/ws/(.*) /api/v1/$1 break;
  proxy_pass http://ginapp-service:8686;
  proxy_http_version 1.1;
  proxy_set_header Upgrade $http_upgrade;
  proxy_set_header Connection "upgrade";
}
```

扩展：<br />
nodejs 后端非常好用的 websocket 通信包：[https://socket.io/](https://socket.io/)<br />基于 socket.io 衍生出的前端 websocket 通信工具：[https://socket.io/docs/client-api/](https://socket.io/docs/client-api/)<br />注意：socket.io.client 必须与 socker.io 配合使用<br />
<br />
