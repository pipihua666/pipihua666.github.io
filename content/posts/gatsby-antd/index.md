---
title: Gatsby配置antd
path: /gatsby-antd
tags: ["Gatsby"]
featuredImage: "./sweet.png"
excerpt: 想试试Gatsby配置antd吗？.
created: 2021-02-20 21:45
updated: 2021-04-11
---

## 前言

> 相信国内的 react 用户都是 antd 的忠实爱好者，此教程实践可行

## 首先安装必须的 npm 包

```
npm install --save gatsby-plugin-antd less less-loader babel-plugin-import
```

## 将以下代码添加到 gatsby-node.js 中

- babel-plugin-import 配置 antd 按需加载组件

```js
exports.onCreateBabelConfig = ({ actions }) => {
  actions.setBabelPlugin({
    name: "babel-plugin-import",
    options: {
      libraryName: "antd",
      style: true,
    },
  })
}
```

## 将以下代码添加到 gatsby-config.js 的插件组中

- modifyVars 可自行修改 antd 的样式

```
{
    resolve: "gatsby-plugin-antd",
    options: {
      style: true,
    },
},
{
    resolve: `gatsby-plugin-less`,
    options: {
      modifyVars: {
          "primary-color": "#da3043",
          "font-family": "Arial",
          "layout-body-background": "#66ff79",
      },
      lessOptions: {
          javascriptEnabled: true,
      },
    },
}
```

## 🤔 最后一起康康我的 package.json 吧

```
  "dependencies": {
    "antd": "^4.12.3",
    "babel-plugin-import": "^1.13.3",
    "gatsby": "^2.32.3",
    "gatsby-image": "^2.11.0",
    "gatsby-plugin-antd": "^2.2.0",
    "gatsby-plugin-feed": "^2.13.0",
    "gatsby-plugin-google-analytics": "^2.11.0",
    "gatsby-plugin-less": "^4.7.0",
    "gatsby-plugin-manifest": "^2.12.0",
    "gatsby-plugin-offline": "^3.10.0",
    "gatsby-plugin-react-helmet": "^3.10.0",
    "gatsby-plugin-sharp": "^2.14.1",
    "gatsby-remark-copy-linked-files": "^2.10.0",
    "gatsby-remark-images": "^3.11.0",
    "gatsby-remark-prismjs": "^3.13.0",
    "gatsby-remark-responsive-iframe": "^2.11.0",
    "gatsby-remark-smartypants": "^2.10.0",
    "gatsby-source-filesystem": "^2.11.0",
    "gatsby-transformer-remark": "^2.16.0",
    "gatsby-transformer-sharp": "^2.12.0",
    "less": "^4.1.1",
    "less-loader": "^8.0.0",
    "prismjs": "^1.22.0",
    "react": "^16.12.0",
    "react-dom": "^16.12.0",
    "react-helmet": "^5.2.1",
    "typeface-merriweather": "0.0.72",
    "typeface-montserrat": "0.0.75"
  },
  "devDependencies": {
    "gh-pages": "^3.1.0",
    "prettier": "2.2.1"
  },
```
