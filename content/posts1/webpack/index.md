---
title: webpack中五个容易混淆的知识点
date: "2021-02-20 21:45"
description: webpack中五个容易混淆的知识点
---

<a name="fPK71"></a>

#### 1.webpack 中，`module`，`chunk` 和 `bundle` 的区别是什么？

<br />![1604373359405.jpg](https://cdn.nlark.com/yuque/0/2020/jpeg/1683395/1604373377394-bbca646b-517a-4f66-8086-458c666e7468.jpeg#align=left&display=inline&height=372&margin=%5Bobject%20Object%5D&name=1604373359405.jpg&originHeight=372&originWidth=657&size=48412&status=done&style=none&width=657)<br />看这个图就很明白了：<br />

1. 对于一份同逻辑的代码，当我们手写下一个一个的文件，它们无论是 ESM 还是 commonJS 或是 AMD，他们都是 module ；
1. 当我们写的 module 源文件传到 webpack 进行打包时，webpack 会根据文件引用关系生成 chunk 文件，webpack 会对这个 chunk 文件进行一些操作；
1. webpack 处理好 chunk 文件后，最后会输出 bundle 文件，这个 bundle 文件包含了经过加载和编译的最终源文件，所以它可以直接在浏览器中运行。

<br />一般来说一个 chunk 对应一个 bundle，比如上图中的 utils.js -> chunks 1 -> utils.bundle.js；但也有例外，比如说上图中，我就用 MiniCssExtractPlugin 从 chunks 0 中抽离出了 index.bundle.css 文件。<br />`module`，`chunk` 和 `bundle` 其实就是同一份逻辑代码在不同转换场景下的取了三个名字：<br />我们直接写出来的是 module，webpack 处理时是 chunk，最后生成浏览器可以直接运行的 bundle。<br />
<br />

<a name="wcxyI"></a>

#### 2.`filename` 和 `chunkFilename` 的区别

<br />`filename` 指**列在** `entry` 中，打包后输出的文件的名称。<br />`chunkFilename` 指**未列在** `entry` 中，却又需要被打包出来的文件（如懒加载的文件）的名称。<br />

<a name="jwkVB"></a>

#### 3.`webpackPrefetch`、`webpackPreload` 和 `webpackChunkName` 到底是干什么的？

<br />`webpackChunkName` 是为预加载的文件取别名，`webpackPrefetch` 会在浏览器闲置下载文件，`webpackPreload` 会在父 chunk 加载时并行下载文件。<br />
<br />`webpackChunkName`打包出来的 bundle 会有 vendors~这种前缀<br />原因是其实 webpack 懒加载是用内置的一个插件 **SplitChunksPlugin** 实现的，这个插件里面有些**默认配置项**，比如说 `automaticNameDelimiter`，默认的分割符就是 `~`<br />
<br />

<a name="5rfUT"></a>

#### 4.`hash`、`chunkhash`、`contenthash` 有什么不同？

<br />hash 计算与整个项目的构建相关；<br />chunkhash 计算与同一 chunk 内容相关；<br />contenthash 计算与文件内容本身相关。<br />

<a name="zQZjK"></a>

#### 5.`sourse-map` 中 `eval`、`cheap`、`inline` 和 `module` 各是什么意思？

| 参数                                 | 参数解释                                                                                                                                                           |
| :----------------------------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| eval                                 | 打包后的模块都使用 `eval()`执行，行映射可能不准；不产生独立的 map 文件，速度很快(不需要复杂转换和打包)，不能映射到原始代码（没有从 loader 中获取 source map）      |
| cheap                                | map 映射只显示行不显示列（只看得到哪行错了），忽略源自 loader 的 source map,即没有源码的映射                                                                       |
| inline                               | 映射文件以 base64 格式编码，加在 bundle 文件最后，不产生独立的 map 文件                                                                                            |
| module                               | 增加对 loader source map 和第三方模块的映射，即源码的映射                                                                                                          |
| `hidden`<br />`nosources-source-map` | 和`source-map`基本相同，但是不会有`sourceMappingURL`注释，这样你就无法定位到源文件，这样你在开发环境能看到具体的目录结构，基本上就是生产环境能用，开发环境不能用， |

<br />
<br />[原文链接](https://mp.weixin.qq.com/s/H5KeOIKoJ1r3K8-EbKmdLA)<br />
