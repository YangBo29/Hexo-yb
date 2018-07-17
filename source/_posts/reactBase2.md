title: react 基础篇（二）
date: 2017-06-18
tags: react
toc: true
categories: 前端框架
---

### webpack 的基本配置

想要实现项目的自动化管理，还是需要依赖自动化构建工具，这篇文章主要讲述的是webpack的基本配置

```javascript
 $ npm install webpack webpack-dev-server --save-dev // 或者使用yarn
 $ npm install style-loader css-loader url-loader file-loader less-loader html-webpack-plugin --save-dev
```

上面安装的的是一个项目基本必须要使用的一些依赖包
安装完成之后我们来看一下webpack该如何配置

```javascript
// 这个插件是为了webpack打包后的代码可以插入到我们html中的
let HtmlWebpackPlugin = require("html-webpack-plugin");

// 我们先配置一个文件配置
module.exports = {
  entry: "./src/index.js", // 入口文件
  output: { // 出口
    filename: "build.js",  // 文件名
    path: require('path').resolve('/dist') // 文件路径，这个需要填写绝对路径，所以我们引用path插件，来将一个相对路径转换成绝对路径
  },
  module: {
    rules: [ // 配置文件解析规则，
      {test: /\.js$/, use: "babel-loader", exclude: /node_modules/},
      {test: /\.css$/, use: ["style-loader", "css-loader"]},
      {test: /\.less$/, use: ["style-loader", "css-loader", "less-loader"]},
      {test: /\.(jpg|png|gif|jpeg)$/, use: "url-loader"},
    ]
  },
  devtool: 'source-map', /*源码映射*/
  plugins: [ // 插件
    new HtmlWebpackPlugin({
      template: "./index.html"
    })
  ]
};
```

多个文件的配置方法

```
module.exports = {
  entry: {
    app: './src/app.js',
    search: './src/search.js'
  },
  output: {
    filename: '[name].js',
    path: __dirname + '/dist'
  }
  ......
}
```

[webpack](http://www.css88.com/doc/webpack/)还有更多的配置参数，我们先配置一个简单的

当然我们想要将环境跑起来，还需要在package.json文件中的scripts中配置一些启动环境的指令

```javascript
// 截取部分代码
"scripts": {
    "dev": "webpack-dev-server --config webpack.config.js",
    "start": "npm run dev",
    "build": "webpack --config webpack.config.js" // 打包时使用的代码
  },
```

配置完成后，我们就可以执行 start 来开启一个本地端口来运行我们的代码了

是不是很简单呢，快来写一写react代码来感受一下吧
