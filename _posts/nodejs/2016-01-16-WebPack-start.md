---
layout: post
title: Webpack上手
categories: nodejs
---

### info:
- by :panshang time: 2016-1-16
## 创建一个WebPack
- 可以通过browserify与gulp来创建一个React JS项目,虽然可能Reacte JS不是你所专注的,但你可以通过这篇文章获得更多的知识

- 用browserify和gulp创建一个React JS 项目时,你需要很多的依赖集, 并且你需要写很多的代码, 你可能很感激gulp给你带来的自由性, 但是它所捆绑的需求使得整体看起来和复杂. 这里, 在使用React JS时,需要知道一些核心概念.
 - SourceMaping
 - 用Common JS 形式来写脚本
 - 转变JSX (Coffe, TypeScript 等)
 - 分离依赖,快速重构
 - 处理样式如 css, sass,less 等
 - 处理图像和字体
 - 使你的代码做好准备
- 根据你的工程, 你可能有更多的或者更少的需求, webpack被模块化的不仅仅是js, 开发便捷, 能替代部分gulp操作, 如打包, 压缩混淆, 图片转base64 ,扩展性强, 插件机制完善, 特别要说的是支持 React 热插拔的功能. 这就是我所说的核心概念. 下面我所要讲的就是如何使用 WebPack 快速地搭建工作站来处理这些核心概念.

# 开始
## 安装并且建立webpack , webpack-server
- 首先需要做的就是同时安装 webpack, webpack-dev-server ,使用npm来安装,:
 - npm使用:
 
```
1.npm install webpack
2.npm install webpack-dev-server
```
 如图: img1,2
 这使得我们可以直接通过终端命令使用webpack, 我们将使用webpack配置代码, 使用webpack-dev-server 来运行项目.
- 创建一个workflow 项目
 - 选择一个空文件夹命名为工作项目名
 - 在这个文件夹下创建一个entry.js的文件
 - 文件内容为: 
 
```
document.write("Webpack test by shinepans");
```
 - 再创建一个 index.html文件,内容为:
 
```
<html>
<head>
    <meta charset = "utd-8">
</head>
<body>
    <script type="text/javascript" src="bundle.js" charset="utf-8"></script>
</body>
</html>
```
 - 在根目录下执行:

```
webpack ./entry.js bundle.js
``` 
 - 如图结果: img3
 - 用浏览器打开发现会输出js文件
- 再添加一个文件
 - 取名为 content.js, 内容为:
```
module.exports = "It works from content.js !";
```
 - 更新刚才添加的 entry.js 添加一行:
 
```
document.write(require("./content.js"));
```
 - 再执行命令:
 
```
webpack ./entry.js bundle.js
```
 - 刷新浏览器, 文字变化
 
 # 第一个LOADER
 - 当我们给应用添加css文件, webpack原生仅支持js, 所以我们需要css-loader 和style-loader 来应用样式
  - 终端运行:(注意是局部安装, 会安装到node_modules里)
  
```
npm install css-loader style-loader
```
 - 接下来,我们再添加一个文件: 
  - style.css
  
```
body{
    backgroud: yellow;
}
```
 - 然后更新 entry.js 加一行:
 
```
require("!style!css./style.css");
```
 - 接下来我们看看效果:
 img6
 
 - 其实可以用这种办法来写, 免去了繁杂的语句:
 
```
require("./style.css");
```
 - 然后在 终端输入:
 
```
webpack ./entry.js bundle.js --module-bind "css=style!css"
```
 - 这效果将会是一样的

- 配置文件 webpack.config.js
 - 添加 webpack.config.js文件
```
module.exports = {
    entry: "./entry.js"
    output: {
        path: __dirname,
        filename: "bundle.js"
    },
    module:{
        loaders:{
            {test:/\.css$/, loader: "style!css"}
        }
    }
}
```
 - 设置好后, 直接使用 webpack 
- 更友好的输出
 - 随着项目的增长, 编译过程可能会越来越长, 所以我们可以展示进度条和增加配色实现友好输出:
 
```
webpack --progress --colors
```
 
- 监听模式
 - 当我们不希望文件改动后手动执行编辑操作时使用:
 
```
webpack --progress --colors --watch
```
- 开发服务器
 - 提供开发服务器是一项好的服务, 可以替换 python -m SimpleHTTPSerer启用HTTP静态服务器:
 
```
webpack install webpack-dev-server -g
```
- 启动服务器
 - 终端:

```
webpack-dev-server --profress --colores
```
  - 这会将express服务器绑到 8080 端口, 通过访问localhost:8080/webpack-dev-server/bundle，bundle 每次重编译后浏览器页面都会自动更新