---
title: react性能优化之代码分割
date: "2021-02-20 21:45"
description: react性能优化之代码分割动态导入
---

![react.jpeg](./image.jpeg)

> 引言

衡量网站的性能的指标有很多，其中有项重要的指标就是网站的首屏时间，而代码分割却是优化首屏时间最通用的方法

> 三种通用的代码拆分方法（基于 webpack）

1. 入口点：使用[`entry`](https://webpack.js.org/configuration/entry-context)配置手动拆分代码
1. 防止重复：使用[`SplitChunksPlugin`](https://webpack.js.org/plugins/split-chunks-plugin/)重复数据删除和拆分块
1. 动态导入：通过模块内的内联函数调用拆分代码

<br />
<a name="J0hBy"></a>

#### 动态导入（**react-loadable**）有以下概念

> 1. 动态导入的组件自动进行性能优异的代码分割
> 1. **所支持的是组件的动态导入，而不是路由的动态导入**
> 1. 基本思想是组件的按需加载
> 1. 注意：要在 react 中使用 import()，需要安装@babel/plugin-syntax-dynamic-import 插件，并在 babel 配置中引入
> 1. 注意：import 的路径是相对路径，不能添加绝对路径
> 1. 注意：要使用魔法注释需要配置（webpack 的 SplitChunkPlugin 插件，webpack 默认已配置，开箱即用）
> 1. react 中的动态导入还有：react 原生 React.lazy（v16.6），原生 Suspense，库 Loadable Component

<a name="xRYgO"></a>

#### 配合 react-router-dom 实现 react 的路由组件按需加载

```jsx
import Loadable from "react-loadable"
```

```jsx
// 懒加载路由
const AsyncComponent = component =>
  Loadable({
    loader: lazyLoad[component], // 加载promise来加载组件
    loading: MyLoadingComponent, // 当加载进行中或者错误时加载的组件
    delay: 500, // 加载loading组件之前的延迟，防止闪屏
  })
```

```jsx
const lazyLoad = {
  home: () =>
    // webpackChunkName（代码分割后的代码块名）
  	// 添加webpackPrefetch：（未来可能需要的资源）
  	//   这将导致将<link rel="prefetch" href="home.js">其附加在页面的开头
    //   这将指示浏览器在空闲时间预取home.js文件
    // 不配置预加载和预取提取则按需加载
    import(/* webpackChunkName: "home" */ /* webpackPrefetch: true */ './home'),
  detail: () =>
  	// webpackPreload（当前导航期间可能需要的资源）
    import(/* webpackChunkName: "detail" /* webpackPreload: true */  */ './detail')
}
```

```jsx
<Switch>
  <Route path="/app/home" component={AsyncComponent("home")} />
  <Route path="/app/detail" component={AsyncComponent("detail")} />
</Switch>
```

> 官网

- [webpack 代码分割官网](https://webpack.js.org/guides/code-splitting/)
- [react-loadable 仓库](https://github.com/jamiebuilds/react-loadable#preloading)
- [import()文档](https://webpack.js.org/api/module-methods/#import-1)
- [react 的 lazy 代码分割](https://zh-hans.reactjs.org/docs/code-splitting.html)
