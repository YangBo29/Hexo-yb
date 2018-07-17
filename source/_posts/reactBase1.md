title: react 基础篇（一）
date: 2017-06-11
tags: react
toc: true
categories: 前端框架
---

## react 安装

对于入门的人来说，是从无到有的过程，首先我们要先了解一下如何使用框架

1、首先要安装node.js 在[Node](http://nodejs.cn/)下载，node的版本会影响你可用的功能和一些语法支持，所以尽量使用高版本的node

2、node会自带npm包管理器，[Npm](https://www.npmjs.com/)，使用npm的指令来安装react项目中需要用到的组件，插件，翻译官(babel)等,如果下载缓慢的话可以采用taobao镜像，也可以使用yarn来安装，速度会快一些。

```
$ npm install -g cnpm --registry=https://registry.npm.taobao.org
```

3、创建一个新的文件夹，在这个文件夹中调用命令窗口（Shift+右键）调用cmd命令窗口
输入指令
``` javascript
 $ npm init  // 生成一个package.json文件 ，一步步配置文件信息
 $ npm init -y // 自动提取文件信息生成一个默认的package.json文件
```
如果想使用yarn 首先要通过

```
$ npm install yarn -g  //来安装
```

之后就可以使用yarn的语法了

4、安装react：

```
$ npm install react react-dom
//或者
$ yarn add react react-dom
```

在react项目中还需要很多辅助插件：

babel 这个翻译官 是在框架项目中必备的，它可以将项目中的es2015(es6)-es2017(es8)的语法转换成现在所有浏览器支持程度最高的es5语法；【注意】：像Set、Map这种是不会被翻译的。

babel 的核心包 babel-core

5、安装babel
```
$ npm babel-core babel-preset-stage-0 babel-preset-react babel-loader babel-preset-env --save-dev

// --save-dev 是将安装这些依赖包安装在开发环境中
```

在现在流行的框架开发中，有开发环境和生产环境，所谓的开发环境就是项目在开发过程中所依赖的环境；生产环境就是上线之后，项目面对用户的真实环境

6、配置文件

我们还需要在根目录下创建一个 .babelrc 文件（没有文件名称）在里面配置一些东西

```
// .babelrc 文件内

[
  "presets":["react","env","stage-0"]
]
```

`env是可以解析es6及以前的语法，但是在用的后发现，他无法解析es6以上的一些草案语法，所以为了可以使用更高级的语法，在后面加上stage-0 保险起见；`

完成以上操作其实你就已经开启了你的react之旅了
