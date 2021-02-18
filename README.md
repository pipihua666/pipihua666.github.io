<!-- AUTO-GENERATED-CONTENT:START (STARTER) -->
<p align="center">
  <a href="https://www.gatsbyjs.com">
    <img alt="Gatsby" src="https://www.gatsbyjs.com/Gatsby-Monogram.svg" width="60" />
  </a>
</p>
<h1 align="center">
  使用Gatsby.js+github pages搭建个人博客
</h1>

## 🚀 Gatsby 环境配置

1. **安装 Gatsby 命令行工具.**

   ```shell
   npm install -g gatsby-cli
   ```

1. **根据官方博客的 starter，创建一个新的 Gatsby 站点.**

   ```shell
   gatsby new my-blog-starter https://github.com/gatsbyjs/gatsby-starter-blog
   ```

1. **本地开发环境.**

   ```shell
   cd my-blog-starter/
   gatsby develop
   ```

1. **打开源代码并开始编辑!**

   你的站点运行在 `http://localhost:8000`!

   注意: 此链接的工具将会方便 graphql 数据查询: `http://localhost:8000/___graphql`

   在您选择的代码编辑器中打开 my-blog-starter 目录，然后编辑 src / pages / index.js。 保存您的更改，浏览器将实时更新！

## 🌞 部署到 github pages

需要在 github 上上创建一个名字叫 username.github.io 的工程，比如本人的 github 用户名是 pipihua666，那么创建的工程名字就是`pipihua666.github.io`

1. 安装依赖

   ```shell
   npm install gh-pages --save-dev
   ```

2. 找到 gatsby-config.js,添加 pathPrefix 配置，如果不需要路径，直接配置成 pathPrefix: /就可以。

   ```shell
   module.exports = {
     pathPrefix: `/project-name`,
   }
   ```

3. 在 package.json，配置 github 仓库并添加一个 scripts 配置，将先将静态文件 build 到 public 目录，然后再 push 到 github 工程的 master 分支。
   ```shell
   "scripts": {
       "deploy": "gatsby build --prefix-paths &&  cp -f README.md public && gh-pages -d public master",
     }
    "repository": {
      "type": "git",
      "url": "git+https://github.com/pipihua666/  pipihua666.github.io.git"
   },
   ```
4. 发布并上线

   ```shell
   npm run deploy
   ```

## 🧐 文件夹约定规范

    .
    ├── node_modules
    ├── src
    ├── .gitignore
    ├── .prettierrc
    ├── gatsby-browser.js
    ├── gatsby-config.js
    ├── gatsby-node.js
    ├── gatsby-ssr.js
    ├── LICENSE
    ├── package-lock.json
    ├── package.json
    └── README.md

1.  **`/src`**: 源代码

2.  **`gatsby-browser.js`**: Gatsby 希望在此文件中找到[Gatsby 浏览器 API]（`https://www.gatsbyjs.com/docs/browser-apis/`） 这些允许自定义/扩展影响浏览器的默认 Gatsby 设置。

3.  **`gatsby-config.js`**: 这是 Gatsby 站点的主要配置文件。 您可以在此处指定有关您的网站（元数据）的信息，例如网站标题和说明，您要包括的 Gatsby 插件等。（`https://www.gatsbyjs.com/docs/gatsby-config/`）

4.  **`gatsby-node.js`**: Gatsby 希望在此文件中找到[Gatsby Node API]（`https://www.gatsbyjs.com/docs/node-apis/`） 这些允许自定义/扩展默认的 Gatsby 设置，从而影响网站的构建过程。

5.  **`gatsby-ssr.js`**: Gatsby 希望在此文件中找到[Gatsby 服务器端渲染 API]（`https://www.gatsbyjs.com/docs/ssr-apis/`） 这些允许自定义影响服务器端渲染的默认 Gatsby 设置。
