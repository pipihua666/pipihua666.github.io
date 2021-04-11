---
title: wireshark过滤器表达式
date: "2021-02-20 21:45"
description: wireshark过滤器表达式
---

#### 过滤器表达式

1. 协议过滤

```
tcp,只显示 tcp 协议
```

2. IP 地址过滤

```
 ip.src == 192.168.1.1 源地址
 ip.dst == 192.168.1.2 目的地址
```

3. 端口过滤

```
 tcp.port == 80，只显示 tcp 端口为 80
 tcp.srcport == 80，只显示 tcp 协议源端口为 80
```

4. HTTP 模式过滤

```
 http.request.method == 'GET',只显示 HTTP GET 方法
```

5. 逻辑运算

```
 ip.src == 192.168.1.1 or ip.src == 193.168.1.2
```
