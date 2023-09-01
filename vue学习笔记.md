# Vue学习笔记

# webpack

## 前端工程化

### 代码目录和webpack安装

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
--save-dev 的含义,表示这两个依赖规划到以下配置里,只在开发环境中用到
"devDependencies": {
    "webpack": "^5.88.2",
    "webpack-cli": "^5.1.4"
  }
```

### webpack的基本使用

#### 	webpack的初始化配置

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



#### webpack的其它配置

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



#### webpack中插件的使用

##### 	webpack-dev-server

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

##### 	html-webpack-plugin

​	**这个插件和webpack-dev-server插件配合一起使用**

​		使用了这个插件之后，就可以实现打开浏览器就访问到了index.html，而不用在去访问src目录才能访问到index.html

​		原理是使用这个插件将index.html文件复制一份到项目的根目录下

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

##### 	使用loader

###### 		**处理css的loader**

​		**安装loader**

```js
npm i style-loader css-loader less-loader less -D
```

​		**在webpack.config.js文件中添加配置**

```js
module.exports = {
    // 所有第三方文件模块的匹配规则
    module: {
        // 文件后缀名的匹配规则
        rules: [
            // test 表示匹配的文件类型，use 表示要调用的loader
            { test: /\.css$/,use: ["style-loader","css-loader"] },
            { test: /\.less$/,use: ["style-loader","css-loader","less-loader"] }
        ]
    }
}
```

​		**在index.js中导入css文件**

```js
import "./css/index.css"
```

​	

###### **加载图片的loader**

​	**安装loader**

```js
npm i url-loader file-loader
```

​	**在webpack.config.js文件中添加配置**

```js
module.exports = {
    module: {
        rules: [
            // limit用来指定图片的大小，单位是字节(byte)
            // 只有<= limit大小的图片，才会被转为base64格式的图片
            {test: /\.jpg|png|gif|ico$/, use: "url-loader?limit=22229"}
        ]
    }
}
```

​	**在index.js中使用**

```js
// 先导入图片
import logo from "./images/logo.ico"
// 给图片赋予src属性
$(".box").prop("src",logo);
```



###### **处理高级js的loader**

**安装loader**

```js
npm i babel-loader @babel/core @babel/plugin-proposal-decorators -D
```

**在webpack.config.js文件中添加配置**

```js
module.exports = {
    module: {
        rules: [
            // 必须使用 exclude 指定排除项,因为 node_modules 目录下的第三方包不需要被打包
            {test: /\.js$/, use: "babel-loader",exclude: /node_modules/}
        ]
    }
}
```

**在根目录下新建babel.config.js文件**

```js
module.exports = {
    // 声明 babel 可以的插件
    plugins: [["@babel/plugin-proposal-decorators", {legacy: true}]]
}
```



### 打包发布

​	**配置package.json文件**

```js
"scripts": {
    "dev": "webpack serve", // 开发环境中运行dev命令
    "build": "webpack --mode production" // 项目发布时，运行build命令
  }
```

​	**clean-webpack-plugin插件**

​	每次打包或者开发环境运行时，回自动删除dist目录，不用手动删除

```js
// 先安装
npm i clean-webpack-plugin

// 在webpack.config.js文件中使用插件
const {CleanWebpackPlugin} = require("clean-webpack-plugin");
plugins: [htmlPlugin,new CleanWebpackPlugin()]
```



#### 详细webpack.config.js配置文件

```js
const path = require("path");

const HtmlPlugin = require("html-webpack-plugin");

// 创建Html插件的实例对象
const htmlPlugin = new HtmlPlugin({
    // 指定原文件的路径
    template: "./src/index.html",
    // 指定生成的文件路径
    filename: "./index.html"
});

const {CleanWebpackPlugin} = require("clean-webpack-plugin");

module.exports = {
    // 开发阶段配置这个选项能保证报错的信息根原代码的行数一致,方便调试,并且安全，只暴露行数，不暴露源码
    devtool: "nosources-source-map",
    mode: "development",
    // 指定要处理的入口文件
    entry: path.join(__dirname, "./src/index.js"),
    output: {
        // 指定路径
        path: path.join(__dirname, "dist"),
        // 指定生成的文件名,指定打包时,js存放路径
        filename: "js/bundle.js"
    },
    devServer: {
        open: false,
        host: "localhost",
        port: 80,
        static: "./"
    },
    plugins: [htmlPlugin,new CleanWebpackPlugin()],
    module: {
        rules: [
            // test 表示匹配的文件类型，use 表示要调用的loader
            {test: /\.css$/, use: ["style-loader", "css-loader"]},
            // 加上outputPath=images参数,指定打包时图片存放路径 "url-loader?limit=22229&outputPath=images"
            // limit的值太大，打包时不会生成images文件夹
            {test: /\.jpg|png|gif|ico$/, use: "url-loader?limit=47&outputPath=images"},
            // 必须使用 exclude 指定排除项,因为 node_modules 目录下的第三方包不需要被打包
            {test: /\.js$/, use: "babel-loader",exclude: /node_modules/}
        ]
    },
    // 配置这个是为了在使用import导入的时候, 用@来导入, @代表 src,这样导入的时候就是从外往里找,结构清晰
    // import logo from "@/images/logo.ico"
    resolve: {
        alias: {
            "@": path.join(__dirname,"./src/")
        }
    }
}
```





# Vue

## 概念

**什么是vue**

1. 构建用户界面
2. 是一个前端的框架

**vue的特性**

1. 数据驱动视图

   >数据驱动视图时单向的，永远是数据的变化回驱动视图自动更新

   - 数据的变化会驱动视图自动更新
   - 优点：编写代码时只需要把数据维护好，页面结构会被vue自动渲染出来

2. 双向数据绑定
   >在页面中，form表单负责<font color='red'>采集数据</font>，Ajax负责<font color='red'>提交数据</font>

   - js数据的变化，会被自动渲染到页面上
   - 页面上表单采集的数据发生变化的时候，会被**vue**自动获取并更新到js数据对象中

> 数据驱动视图和双向数据绑定的底层原理都是MVVM，即Model-数据源、View-DOM结构、ViewModel-vue实例



## 基础用法

### 入门

1. 先引入vue.js文件
   ```js
   <script src="../../lib/vue.js"></script>
   ```

2. 创建vue实例对象
   ```js
   // 创建vue实例对象
       const vm = new Vue({
           // el属性固定写法,表示当前vue实例要控制页面上的哪个区域,接收的值是一个css选择器
           el: "#app",
           data: {
               username: "xjh",
               user: {
                   username: "xiangjiahui"
               }
           }
       });
   ```

3. html中使用
   ```html
   <!-- 表示这个区域是vue接管的 -->
   <!-- 页面访问数据的时候可以不用加$data,如果是最外层的数据,那么直接用key就可以获取值,如果再下层还有对象,就用obj.key获取-->
   <div id="app">
       {{ username }}<br>
       {{ $data.username }}<br>
       {{ $data.user.username }}<br>
       {{ user.username }}<br>
       {{ user }}<br>
   </div>	
   ```



### 指令

#### 内容渲染指令

> 内容渲染指令是vue提供的模板语法，能够使用指令将数据填充到DOM元素的内部，有三种语法

##### **v-text**

> v-text指令可以将data数据填充到标签内部，但是会覆盖标签内部原有的值，所以此指令不能实现数据拼接的效果，只能整个覆盖

**用法**

```html
<div id="app">
    <!-- v-text指令可以将数据data的数据填充到p标签内部，如果标签内部本来有值，那么会被v-text渲染的值覆盖 -->
    <p v-text="username"></p>
    <p v-text="user.username">p标签</p>
</div>

<script>
    const vm = new Vue({
        el: "#app",
        data: {
            username: "xjh",
            user: {
                username: "xiangjiahui"
            }
        }
    });
</script>
```



##### 插值表达式{{  }}

> {{  }} 这个语法叫插值表达式，专门用来解决v-text语法会覆盖值的问题

**用法**

```html
<div id="app">
    <p>姓名: {{ user.username }}</p>
    <p>性别: {{ user.gender }}</p>
</div>
<script>
    const vm = new Vue({
        el: "#app",
        data: {
            user: {
                username: "xiangjiahui",
                gender: "男"
            }
        }
    });
</script>
```



##### v-html

> v-html语法可以将带有html标签的数据也渲染成真正的html元素到标签的内部

**用法**

```html
<div id="app">
    <p v-html="message"></p>
</div>
<script>
    // 创建vue实例对象
    const vm = new Vue({
        el: "#app",
        data: {
            message: "<h4 style='color: red;'>vue.js使用</h4>"
        }
    });
</script>
```



#### 属性绑定指令

##### v-bind:

> v-bind: 属性绑定指令只能用于操作html元素的属性，可以简写为 :

**用法**

```html
<div id="app">
    <input type="text"  v-bind:placeholder="tips">
    <br>
    <img v-bind:src="photo" alt="" style="width: 50px;">
    <!-- 简写形式 : -->
    <img :src="photo" alt="" style="width: 50px;">
</div>
<script>
    const vm = new Vue({
        el: "#app",
        data: {
            tips: "请输入内容",
            photo: "https://cn.vuejs.org/logo.svg"
        }
    });
</script>
```

**使用JS语法**

> 在插值语法和v-bind: 语法中，可以编写js语句

```html
<div id="app">
    <input type="text"  v-bind:placeholder="tips">
    <br>
    <img v-bind:src="photo" alt="" style="width: 50px;">
    <img :src="photo" alt="" style="width: 50px;">

    <!--还可以再插值语法和bind中使用js语法-->
    <div>1 + 2 = {{ 1 + 2 }}</div>
    <div>tips反转: {{ tips.split("").reverse().join("") }}</div>
    <div :data-id="'data-id-' + id" :id="id"></div>
</div>
<script>
    const vm = new Vue({
        el: "#app",
        data: {
            tips: "请输入内容",
            photo: "https://cn.vuejs.org/logo.svg",
            id: 1
        }
    });
</script>
```



#### 事件绑定指令

##### v-on:

> v-on: 给html元素绑定事件，用法是v-on:事件类型，例如v-on:click。v-on:也可以简写为@

**用法**

```html
<div id="app">
    <p>{{ count }}</p>
    <!-- 方法也可以直接写名称add，如果要传参数，那么就需要加上括号传递参数，还可以传递event，参数没有前后顺序 -->
    <button v-on:click="add(5,$event)">增加</button>
    <button v-on:mouseenter="">鼠标经过</button>
    <!-- 简写形式 -->
    <button @mouseenter="">鼠标经过</button>
</div>
<script>
    const vm = new Vue({
        el: "#app",
        data: {
            count: 0
        },
        methods: {
            add (n,e) {
                this.count += n;
            }
        }
    });
</script>
```

**事件修饰符**

```html
<button @click.stop="add(5,$event)">增加</button>
<button @click.prevent="add(5,$event)">增加</button>
```

**按键修饰符**

> 使用按键修饰符，在input 绑定key事件时，不用每次都判断按下的键盘是什么

```html
<div id="app">
    <input type="text" @keyup.enter="enter">
</div>
<script>
    const vm = new Vue({
        el: "#app",
        methods: {
            enter() {
                console.log("按下了enter键");
            }
        }
    });
</script>
```



#### 双向绑定指令

##### v-model

> 双向绑定指令，html元素的值发生变化会更新到对象数据，对象数据发生发生变化会更新到元素

**用法**

```html
<div id="app">
<input type="text" v-model="username">
<select v-model="city">
    <option value="">请选择</option>
    <option value="北京">北京</option>
    <option value="上海">上海</option>
    <option value="天津">天津</option>
</select>
</div>
<script>
    const vm = new Vue({
        el: "#app",
        data: {
            username: "",
            city: ""
        }
    });
</script>
```

**v-model专有的指令修饰符**

```html
<!-- .number 自动将用户的输入值转为数值类型 -->
<input type="text" v-model.number="username">

<!-- .trim 自动过滤用户输入的首尾空白字符 -->
<input type="text" v-model.trim="username">

<!-- .lazy 在"change"时，而非"input"时更新 -->
<input type="text" v-model.lazy="username">
```



#### 条件渲染指令

##### v-if和v-show

> 通过指令来控制元素的显示和隐藏，有两条指令

1. **v-if**

   > 满足条件为true，显示元素，否则隐藏，这个指令是通过动态的添加和删除元素实现效果，性能差，不过一般if使用多

2. **v-show**

   > 满足条件，即条件为true，显示元素，否则隐藏，这个指令是通过display来实现效果，性能好

**用法**

```html
<div id="app">
    <p v-if="show">v-if控制</p>
    <p v-show="show">v-show控制</p>
</div>
<script>
    const vm = new Vue({
        el: "#app",
        data: {
            show: true
        }
    });
</script>
```



##### v-if的配套指令

> 在vue中v-if条件指令也可以向if else 一样，进行分支选择的去渲染执行

**用法**

```html
<div id="app">
    <p v-if="grade >= 90">优秀</p>
    <p v-else-if="grade >= 80">良好</p>
    <p v-else-if="grade >= 70">一般</p>
    <p v-else>及格</p>
</div>
<script>
    const vm = new Vue({
        el: "#app",
        data: {
            grade: 90
        }
    });
</script>
```



#### 列表渲染指令

##### v-for

> 要循环生成什么元素，就在什么元素上加上v-for指令，自身和其子元素都能访问到v-for里面循环的值

**用法**

```html
<div id="app">
    <ul>
        <li v-for="(item,index) in users" :data-id="item.id">
            <p>索引下标: {{ index }}</p>
            <p>OID: {{ item.id }}</p>
            <p>姓名: {{ item.name }}</p>
        </li>
    </ul>
</div>
<script>
    const vm = new Vue({
        el: "#app",
        data: {
            users: [
                {id: 1, name: "xiangjiahui"},
                {id: 2, name: "renchunxiu"},
                {id: 3, name: "xiangyuansui"}
            ]
        }
    });
</script>
```

