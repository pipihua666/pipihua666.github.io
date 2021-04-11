---
title: k8s持久化存储实现前端oem需求
path: /oem
tags: ["Kubernetes"]
featuredImage: "./volumes.jpeg"
excerpt: 前端根据licence显示不同的背景图和文字信息.
created: "2021-02-21 15:00"
updated: 2021-04-11
---

## 技术栈

- react，docker，k8s

## 需求

- 前端实现界面的 licence 控制，根据 licence 显示不同的背景和文字信息，并且不用重新编译前端镜像，达到可以在宿主机上随时都可以定制的需求

## 需求分析

- 创建一个 oem 文件夹，存放不同的 licence 的 index.json 和图片，前端通过 ajax 可以直接请求本地的文件（这里是 index.json） k8s 在宿主机上持久化本地存储的方式来进行需求开发再合适不过。）

## 目前官方支持的 3 种本地存储方案如下：

### emptyDir

1. pod 在节点上运行时初始化的空卷，pod 中的所有容器都可以访问到该卷。
2. pod 从节点上删除时，该卷也会被删除(注意：容器奔溃不会导致 pod 从节点上删除，因此容器奔溃时 emptyDir 是安全的）。

pod 示例：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
    - image: my-app-image
      name: my-app
      volumeMounts:
        - mountPath: /cache   // pod中的emptyDir路径
          name: cache-volume
  volumes:
    - name: cache-volume
      emptyDir: {}
```

### hostPath（适合我的需求）

1. pod 中的文件夹与宿主机上的文件夹相互映射，如果宿主机没有则到 pod 映射，如果 pod 没有则到宿主机映射，如果都没有则默认空文件夹
2. 宿主机上映射的文件夹的修改会自动同步到 pod 中。
3. pod 即使从节点上被删除了（比如：pod 重启），该卷还是会持久化在宿主机上。

pod 示例：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
    - name: my-app-image
      image: my-app-image
      volumeMounts:
        - mountPath: /opt/nginx/oem  // pod路径
          name: oem   // 映射宿主机的volume名称
  volumes:
    - name: oem
      hostPath:
        path: /data/oem    // 宿主机路径
```

但是还有一点问题，就是 pod 每次重启了，会重新创建 pod 中的 oem 文件夹，这里就是/opt/nginx/oem,那么宿主机上的文件夹/data/oem 也会随着 pod 的重启而替换，所以这需要在 pod 重启的容器中添加一个 command 指令<br />

```yaml
spec:
  containers:
    - name: my-app-image
      image: my-app-image
      command:
        [
          "sh",
          "-c",
          "cp -r -n /opt/assets/public/oem /opt/nginx/ && nginx -g 'daemon off;'",
        ]
      volumeMounts:
        - mountPath: /opt/nginx/oem
          name: oem
```

1. /opt/assets/public/oem 是容器中的模版文件夹
2. `cp -r -n` 如果 pod 中存在/opt/nginx/oem 就不复制（保证每次重启 pod 都是用的宿主机上的 oem 文件夹），也就是初始化 pod 的时候会创建该文件夹
3. 镜像的 cp 命令升级：
   `RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g' /etc/apk/repositories \ && apk add --no-cache coreutils`
   （如果 nginx:1.17-alpine 的版本太老，没办法 cp -n）
4. 至此，需求完成。

### Local Persistent Volume

指的就是利用机器上的磁盘来存放业务需要持久化的数据，和远端存储类似，此时数据依然独立于 Pod 的生命周期，即使业务 Pod 被删除，数据也不会丢失。

## Local Persistent Volume 和 hostPath 的区别

1. HostPath:调度程序可能会将引用 HostPath 卷的 pod 移至其他节点，从而导致数据丢失。
2. Local Persistent: 调度程序可确保始终将使用本地持久卷的容器调度到同一节点。

## 参考

- [Kubernetes 本地持久化方案](https://segmentfault.com/a/1190000022860937)
