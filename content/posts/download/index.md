---
title: 通过a标签同源和跨域下载服务器文件
path: /download
tags: ["Download"]
featuredImage: "./download.png"
excerpt: 通过a标签同源和跨域下载服务器文件.
created: 2021-02-20 21:45
updated: 2021-04-11
---

## 同源下载(推荐)

- 优点是：通过浏览器的默认行为下载，不会阻塞
- 缺点是：必须是同源下载，不过现在都是 webpack 和 nignx 代理，所以推荐这种下载方式
- 简单说说：如果响应头添加 `Content-Disposition`，可不使用 `download`，两者都可以阻止浏览器默认打开文件，而不是下载文件的行为，但是前者优先级比后者高

```javascript
/**
 * @description: 从远程url下载文件(同源)
 * @param {string} url -远程的url和参数
 * @param {string} filename -下载到本地的文件名
 */
export const downLoadFile = (url: string, filename: string) => {
  const origin = `/api/${url}`
  const aLink = document.createElement("a")
  aLink.style.display = "none"
  aLink.href = origin
  aLink.download = filename
  aLink.target = "_black"
  aLink.type = "application/octet-stream" // 让浏览器知道是二进制
  document.body.appendChild(aLink) // 如果不挂载到body上，火狐浏览器会失效
  aLink.click()
  document.body.removeChild(aLink)
}
```

## 跨域下载

- 缺点是：要等 blob 封装完成才能下载，如果想实现前端流式下载，可参考[fetch 方法](https://segmentfault.com/a/1190000021367378)
- 优点是：可以携带 Cookie 和请求体 实现动态下载

```javascript
/**
 * @description: 获取 blob
 * @param {string} url -目标文件地址
 */
function getBlob(url: string) {
  return new Promise(resolve => {
    const xhr = new XMLHttpRequest()
    xhr.open("GET", url, true)
    xhr.responseType = "blob"
    xhr.onload = () => {
      if (xhr.status === 200) {
        resolve(xhr.response)
      }
    }
    xhr.send()
  })
}
```

```javascript
/**
 * @description: 下载blob工具函数
 * @param {string} blob -文件流
 * @param {string} filename -下载的文件名
 */
function saveAs(blob: Blob, filename: string) {
  if (window.navigator.msSaveOrOpenBlob) {
    navigator.msSaveBlob(blob, filename)
  } else {
    const anchor = document.createElement("a")
    const body = document.querySelector("body")
    anchor.href = window.URL.createObjectURL(blob)
    anchor.download = filename

    anchor.style.display = "none"
    body.appendChild(anchor)

    anchor.click()
    body.removeChild(anchor)

    window.URL.revokeObjectURL(anchor.href)
  }
}
```

```javascript
/**
 * @description: 用户调用的下载函数
 * @param {string} url -目标文件地址（跨域）
 * @param {string} newFilename -想要保存的文件名称
 */
export async function download(url: string, newFilename: string) {
  const blob: Blob = await getBlob(url)
  saveAs(blob, newFilename)
}
```

## 参考

- [一个很全面的实现前端文件下载的博客](https://juejin.im/post/6844903763359039501)
- [前端下载文件的 5 中方法对比](https://juejin.im/post/6844904069958467592)
- [前端 zip 压缩文件/文件夹并下载到本地](https://www.npmjs.com/package/jszip)
