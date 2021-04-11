---
title: nginx 部署 react 项目
date: "2021-02-20 21:45"
description: nginx部署react单页面应用并实现静态文件下载
---

我用的是 create-react-app 在 nginx 上部署的前端项目，并使用 nginx 实现了下载文件的功能
<br>
<a name="33FUW"></a>

#### nginx 的根目录是/usr/share

```nginx
location /pkg/ {
  if (!-f $request_filename) {
    return 404 '文件不存在';
  }
}

location /upgrade {
  try_files $uri $uri/ /upgrade/index.html;
}
```

<a name="k0wDy"></a>

#### 可以看到，下载升级包的页面路径是/upgrade，项目部署上线结果白屏了，找了找原因

1. react 项目配置页面路由的 basename(前端路由的/等于 nginx 的根目录/use/share)

```jsx
function App() {
  return (
    <Router basename="/upgrade">
      <Switch>
        <Route exact path="/">
          <Main />
        </Route>
        <Route exact path="/404">
          <NotFoundPage />
        </Route>
        <Route>
          <NotFoundPage />
        </Route>
      </Switch>
    </Router>
  )
}
```

2. pakage.json 中配置

```json
  "homepage": "/upgrade",
```

如果是 webpack 而不是脚手架 create-react-app,则配置 publicPath,目的是让 html 中引用的 js,css 文件的路径是以/upgrade 开头的，那么路由就可以找到他们了，homepage 只会影响项目中引用的静态资源的路径

```html
<script src="/upgrade/static/js/2.c8c300b5.chunk.js"></script>
<script src="/upgrade/static/js/main.529d8b95.chunk.js"></script>
```

<a name="ztjd1"></a>

#### 前端实现下载文件，如果文件不存在则使用 react-router 跳转到 404 页面

```jsx
// head请求文件是否存在，如果存在才创建a标签触发浏览器的下载行为
axios.head(path, {
  validateStatus: status => {
    if (status >= 200 && status < 300) {
      const a = document.createElement("a")
      a.href = path
      a.style.display = "none"
      a.rel = "noopener noreferrer"
      a.target = "_blank"
      a.download = `${key}.pkg`
      a.type = "application/octet-stream"
      document.body.appendChild(a)
      a.click()
      document.body.removeChild(a)
    } else if (status === 404) {
      props.history.push("/404")
    }
    return false
  },
})
```

<a name="YM84e"></a>

#### 为什么使用 head 先请求一次？

**HTTP `HEAD` 方法** 请求资源的头部信息, 并且这些头部与 HTTP [`GET`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/GET) 方法请求时返回的一致. 该请求方法的一个使用场景是在下载一个大文件前先获取其大小再决定是否要下载, 以此可以节约带宽资源.

<a name="hzhvb"></a>

### 本文中参考的博客

- [HashRouter 和 BrowserRouter 的区别](https://blog.csdn.net/jingtian678/article/details/90239919)
- [React-Router browserHistory 浏览器刷新出现页面 404 解决方案](https://www.thinktxt.com/react/2017/02/26/react-router-browserHistory-refresh-404-solution.html)
- [create-react-app 一些常用的自定义配置](https://segmentfault.com/a/1190000023327242)
