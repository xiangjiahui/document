# Vue学习笔记

## 基础

### 1、前端工程化

#### 代码目录和webpack安装

**代码目录结构**

```html
首先是根目录,自定义名称
root
根目录下一层必须是src
  src 
src里面放置各种其它的文件
    index.html/index.js...

在根目录下执行命令来初始化项目，生成package.json文件
npm init -y
下载第三方依赖包jquery
npm i jquery
下载webpack和webpack-cli  
npm i webpack webpack-cli --save-dev
--save-dev 的含义,表示这两个依赖规划到一下配置里,只在开发环境中用到
"devDependencies": {
    "webpack": "^5.88.2",
    "webpack-cli": "^5.1.4"
  }
```

#### webpack的基本使用



##### 	webpack的初始化配置

​		**在项目中配置webpack**

​		**首先在项目根目录下新建webpack.config.js文件**

​		webpack.config.js

```js
// webpack.config.js 是webpack的配置文件
// 向外导出一个webpack的配置对象
module.exports = {
    // 代表webpack的运行的模式,可选值有两个,development、production
    // development模式打包耗费时间少，但打包后的js文件没有完全进行压缩
    // production模式打包耗费时间较多，但是打包后的js文件是完全压缩过的
    mode: "development"
}
```

​		**webpack配置完成后，在package.json文件中继续配置**

```js
"scripts": {
    "dev": "webpack"
  }
```

​		**配置完成后，在终端执行命令**

```js
npm run dev
// 执行命令以后，在项目根目录下，会生成一个dist文件夹，将里面的main.js文件引入index.html，
// npm run dev 命令执行以后生成的js文件没有兼容性问题
```



##### webpack的其它配置

​	**自定义打包的入口与出口**

```js
const path = require("path");

module.exports = {
    // 指定要处理的入口文件
    entry: path.join(__dirname,"./src/index.js"),
    output: {
        // 指定路径
        path: path.join(__dirname,"dist"),
        // 指定生成的文件名
        filename: "bundle.js"
    }
}
```



##### webpack中插件的使用

###### 	webpack-dev-server

​	这个插件相当于nodejs中的nodemon，类似于热部署，每次修改了代码就不需要再重新打包

​	**先安装插件**

```js
npm i webpack-dev-server --save-dev
```

​	**在package.json文件中修改配置**

```js
"scripts": {
    "dev": "webpack serve"
  },
 // webpack-cli 和 webpack-dev-server 可能会有版本冲突造成热部署不生效，选择以下版本下载
 "devDependencies": {
    "webpack": "^5.42.1",
    "webpack-cli": "^4.7.2",
    "webpack-dev-server": "^3.11.2"
  }
// 配置完成之后，npm run dev 命令启动之后会自动检测代码的更改去重启，就不要每次更改之后都需要去手动重启服务
```

​	**在webpack.config.js文件中也加上一项配置**
```js
module.exports = {
    mode: "development",
    // 指定要处理的入口文件
    entry: path.join(__dirname,"./src/index.js"),
    output: {
        // 指定路径
        path: path.join(__dirname,"dist"),
        // 指定生成的文件名
        filename: "bundle.js"
    },
    // 新加上的配置
    devServer: {
        // 自动打开浏览器
        open: true,
        host: "localhost",
        port: 80,
        static: "./"
    }
}
```

###### 	html-webpack-plugin

​	**这个插件和webpack-dev-server插件配合一起使用**

​	**安装插件**

```js
npm i html-webpack-plugin --save-dev
```

​	**配置html-webpack-plugin插件的使用**

```js
// 导入插件，得到构造函数
const HtmlPlugin = require("html-webpack-plugin");
// 创建Html插件的实例对象
const htmlPlugin = new HtmlPlugin({
    // 指定原文件的路径
    template: "./src/index.html",
    // 指定生成的文件路径
    filename: "./index.html"
});
//使用插件
module.exports = {
    plugins: [htmlPlugin]
}
```



##### webpack中的loader

###### 	概念

​		webpack只能打包处理js后缀的文件，其它非js后缀的文件无法处理，所以需要调用loader加载器才可以正常打包

###### 	作用

​		协助webpack打包处理特定的文件模块，比如css、less、高级的JS语法

​		css-loader 打包处理css文件、less-loader 打包处理less文件、babel-loader 打包处理高级JS语法

###### 	使用loader

​		**安装loader**

```js
npm i style-loader css-loader -D
```

​		**在webpack.config.js文件中添加配置**

```js
module.exports = {
    module: {
        rules: [
            // test 表示匹配的文件类型，use 表示要调用的loader
            { test: /\.css$/,use: ["style-loader","css-loader"] }
        ]
    }
}
```

​		**在index.js中导入css文件**

```js
import "./css/index.css"
```

