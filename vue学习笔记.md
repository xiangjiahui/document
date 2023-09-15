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

> vue官方建议，使用v-for指令时，一定要绑定一个key属性，尽量把id作为key值，并且key值类型只能是字符串或者数字类型

```html
<li v-for="(item,index) in users" :data-id="item.id" :key="item.id">
            <p>索引下标: {{ index }}</p>
            <p>OID: {{ item.id }}</p>
            <p>姓名: {{ item.name }}</p>
</li>
```



### 过滤器

#### 基本用法

```html
<div id="app">
    <!-- 值是过滤器返回的值，msgFilter 是过滤器的名称 -->
    <div>message过滤的结果是: {{ msg | msgFilter }}</div>
</div>
<script>
    const vm = new Vue({
        el: "#app",
        data: {
            msg: "hello vue.js"
        },
        // 定义过滤器函数，必须被定义到filters节点之下
        // 过滤器本质上是函数，过滤器一定要有返回值，在filters节点下定义的过滤器是私有过滤器
        filters: {
            // 过滤器函数形参中的val，永远都是"管道符"前面的那个值，{{ msg | msgFilter }} 也就是 msg
            msgFilter(val) {
                const msgArr = val.split(" ");
                // slice方法截取字符串，从指定索引截取到最后
                const head = msgArr[0].charAt(0).toUpperCase() + msgArr[0].slice(1);
                const other = msgArr[1].charAt(0).toUpperCase() + msgArr[1].slice(1);
                return head +" " + other;
            }
        }
    });
</script>
```



#### 全局过滤器

> 可以new多个vue实例，定义全局过滤器，多个vue实例就不用重复定义私有的过滤器，所有的vue实例都可以使用全局过滤器
>
> 还可以连续的调用多个过滤器: {{ msg | filterA | filterB  }}

**用法**

```html
<div id="app">
    <div>message过滤的结果是: {{ msg | msgFilter }}</div>
</div>

<div id="app2">
    <div>message过滤的结果是: {{ msg | msgFilter }}</div>
</div>
<script>
    // 定义全局过滤器，参数1: 过滤器名称  参数2: 过滤器方法，和私有过滤器一样，同样可以获得"管道符"前面的那个值
    // 如果全局过滤器和私有过滤器名称重复，采取就近原则，优先调用的是私有的过滤器
    Vue.filter("msgFilter",function (str) {
        const msgArr = str.split(" ");
        const head = msgArr[0].charAt(0).toUpperCase() + msgArr[0].slice(1);
        const other = msgArr[1].charAt(0).toUpperCase() + msgArr[1].slice(1);
        return head +" " + other + "----global";
    });

    const vm = new Vue({
        el: "#app",
        data: {
            msg: "hello vue.js"
        },
        filters: {
            msgFilter(val) {
                const msgArr = val.split(" ");
                const head = msgArr[0].charAt(0).toUpperCase() + msgArr[0].slice(1);
                const other = msgArr[1].charAt(0).toUpperCase() + msgArr[1].slice(1);
                return head +" " + other;
            }
        }
    });
    const vm2 = new Vue({
        el: "#app2",
        data: {
            msg: "hello vue.js"
        }
    });
</script>
```



### 侦听器

> 监视data里的数据的变化，如果数据发生了变化就可以在侦听器里做一些操作，本质上是一个函数

**用法**

```html
<div id="app">
    <input type="text" v-model="username">
</div>
<script>
    const vm = new Vue({
        el: "#app",
        data: {
            username: ""
        },
        watch: {
            // newValue 新的值，oldValue 旧的值
            // 要侦听什么，就以哪个属性名为方法名
            username(newValue,oldValue) {
                console.log("username发生了值变化");
                console.log("新值:"+ newValue);
                console.log("旧值:"+ oldValue);
            }
        }
    })
</script>
```

##### immediate选项

> 将侦听器写成对象格式的，配置immediate属性，在页面刷新或者第一次进入页面时就能立即触发侦听器

**用法**

```js
const vm = new Vue({
        el: "#app",
        data: {
            username: "xjh"
        },
        watch: {
            // username(newValue,oldValue) {
            //     console.log("username发生了值变化");
            //     console.log("新值:"+ newValue);
            //     console.log("旧值:"+ oldValue);
            // }
            // 对象格式的侦听器
            username: {
                // 处理侦听器的函数
                handler(newValue,oldValue) {
                    console.log(newValue,oldValue);
                },
                // 配置这项属性,页面刷新或者第一次进入立即触发侦听器
                immediate: true
            }
        }
    })
```

##### deep深度监听

> 如果监听的是一个对象，那么对象里面的数据发生了变化就不能被监听到，这时需要配置deep深度监听才能监听到对象值变化

**用法**

```html
<div id="app">
    <input type="text" v-model="info.msg">
</div>
<script>
    const vm = new Vue({
        el: "#app",
        data: {
            username: "xjh",
            info: {
                msg: ""
            }
        },
        watch: {
            info: {
                // 这里的newVal 是监听的对象 info，而不是里面的msg属性
                handler(newVal) {
                    console.log(newVal.msg);
                },
                deep: true
            },
            // 如果想要直接监听到对象的子属性，就需要换一种写法，这种写法比上面的写法比较简单
            "info.msg"(newVal) {
                console.log(newVal);
            },
            // 或者另一种写法
            "info.msg": {
                handler(newVal) {
                    console.log(newVal);
                },
                immediate: true
            }
        }
    })
</script>
```



### 计算属性

> 计算属性指的是通过一系列的运算之后，最终得到的一个属性值，这个属性值可以被模板结构或methods里面的方法使用
>
> 所有的计算属性，都要绑定到computed节点之下，在定义的时候，要定义成方法格式，但是在使用中要当成属性去用，
>
> 因为在实际的vue实例中，定义的计算属性就是一个属性

**用法**

```html
<div id="app">
    R:<input type="text"  v-model.number="r"><br>
    G:<input type="text"  v-model.number="g"><br>
    B:<input type="text"  v-model.number="b"><br>
    <hr>
    <div class="box" :style="{backgroundColor: getRGB}">
        {{ getRGB }}
    </div>
</div>
<script>
    const vm = new Vue({
        el: "#app",
        data: {
            r: "",
            g: "",
            b: ""
        },
        computed: {
            getRGB() {
                return `rgb(${this.r},${this.g},${this.b})`;
            }
        }

    })
</script>
```

#### axios技巧

```js
// 可以利用解构，直接获取到axios返回结果里面的真实数据
async function test() {
    // { data } 这是解构，直接拿到返回结果的data，也就是真实的数据
    // { data: res } 在data后面加上 : res 是将data重命名的意思
    const { data: res } = await axios.get("...");
    const code = res.code;
}
```



## vue-cli

> 通过使用vue-cli，就可以快速的构建基于webpack工程化的前端项目

### 安装和使用

> vue-cli是npm上的一个全局包，npm i -g 	-g即代表是全局包
>
> 直接在cmd命令工具框里输入npm i -g @vue/cli，安装完之后可以执行命令 vue -V，如果出现版本号，那么就代表安装成功
>
> 安装完成之后，进入对应的文件目录下，执行命令: vue create 项目的名称
>
> vue create vue-cli-basic

```js
// 执行vue create vue-cli-basic命令之后出现以下界面
// 前面两项选择会自动安装对应的插件，最后一个选项是可以自定义安装哪些插件，一般选择最后一项
Vue CLI v5.0.8
? Please pick a preset: (Use arrow keys)
> Default ([Vue 3] babel, eslint)
  Default ([Vue 2] babel, eslint)
  Manually select features


// 选择Manually select features 选择之后出现以下界面
// Linter / Formatter 代表安装代码风格的组件，会统一所有的代码风格，可以不选，按空格键来确认选与不选
  Vue CLI v5.0.8
? Please pick a preset: Manually select features
? Check the features needed for your project: (Press <space> to select, <a> to toggle all, <i> to invert selection, and <enter> to proceed)
>(*) Babel
 ( ) TypeScript
 ( ) Progressive Web App (PWA) Support
 ( ) Router
 ( ) Vuex
 ( ) CSS Pre-processors
 (*) Linter / Formatter
 ( ) Unit Testing
 ( ) E2E Testing

// 选择完成之后按下enter键，出现以下界面，根据实际情况去选择vue的版本
? Please pick a preset: Manually select features
? Check the features needed for your project: Babel
? Choose a version of Vue.js that you want to start the project with
  3.x
> 2.x

// 确认版本之后按下enter键，出现以下界面
// 这是代表将这些插件是独立建立一个配置文件还是写入到package.json文件里去
// 一般是选择第一个选项 In dedicated config files，按下enter键确认
? Please pick a preset: Manually select features
? Check the features needed for your project: Babel
? Choose a version of Vue.js that you want to start the project with 2.x
? Where do you prefer placing config for Babel, ESLint, etc.? (Use arrow keys)
> In dedicated config files
  In package.json

// 确认之后出现以下界面
// 这是代表是否将这次的配置项保存为一个配置项，yes/no 都可
? Please pick a preset: Manually select features
? Check the features needed for your project: Babel
? Choose a version of Vue.js that you want to start the project with 2.x
? Where do you prefer placing config for Babel, ESLint, etc.? In dedicated config files
? Save this as a preset for future projects? (y/N)

// 如果选了y,那么就要为这个配置项命名
? Save preset as:  // 写上命名

// 命名完之后，就等待node安装项目即可
// 安装完成之后，进入目录修改以下package.json文件里的配置，使用命令 npm run dev 启动项目

```



### 目录结构

src文件夹下的目录构成

```tex
assets文件夹:	存放的是项目使用到的一些静态资源，例如css、图片或一些第三方库文件
components文件夹:	存放的是封装好了的组件
main.js:	是项目的入口文件，整个项目的运行，要先执行main.js
App.vue:	是项目的根组件
```



### 替换根组件的使用

#### 先定义组件

```vue
<!-- 首先在src目录下定义一个Test.vue组件 -->
<!-- template 是组件里的模板结构 -->
<!-- 在template中，只能有一个根节点，也就是说最外层只能有一个div或其它元素，其它的元素都要包裹在最外层根节点元素里面-->
<template>
  <div>
    <p>这是自定义的Test.vue组件</p>
    <p>{{ username }}</p>
  </div>
</template>
<!-- script js语法都写在这个区域里 -->
<script>
// 默认导出，这是固定写法,只要是.vue 组件,都要用这种写法导出
export default {
  name: "Test",
  //在.vue 组件中定义数据源,不能再向以前一样定义成{} 对象格式的,要定义成函数格式的,要进行return
  //在.vue 组件中，除了数据源要定义成函数，其它的methods、filters、watch、computed还是定义成 {} 对象格式的
  data() {
    return {
      username: "administrator"
    }
  }
}
</script>
<!-- css样式区域 -->
<style scoped>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
div {
  margin: 50px auto;
  width: 500px;
}
p:nth-child(1) {
  font-size: 20px;
  font-weight: 700;
  color: aquamarine;
}
p:nth-child(2) {
  font-weight: 700;
  color: red;
}
</style>
```

#### main.js中引用

```js
import Vue from 'vue';
import App from './App.vue';
// 导入
// 也可以这样写 import Test from "./Test.vue"
import Test from "./Test";

Vue.config.productionTip = false

new Vue({
  // 将原来的 h => h(App) 替换成 h => h(Test)
  render: h => h(Test),
}).$mount('#app')
```



### 组件的使用

#### 定义组件

```vue
<!-- 在components目录下定义新的组件Left.vue -->
<template>
  <div id="root">
    <h1>Left组件</h1>
  </div>
</template>

<script>
export default {
  name: "Left"
}
</script>

<style scoped>
#root {
  margin: 0;
  padding: 0;
  width: 300px;
  height: 200px;
  background-color: chocolate;
}
</style>
```

#### 引入和注册组件

```vue
<!-- 在App根组件里面导入Left.vue -->
<script>
// 导入组件，可以导入使用多个组件
import Left from "@/components/Left"
import Right from "@/components/Left"    
export default {
  name: 'App',
  // 注册组件，标准写法应该是 key: value --->  "Left": Left, 如果完全相同，则直接写即可
  components: {
    Left,
    Right
  }
}
</script>
```

#### 使用组件

```vue
<!-- 在App.vue 中的template中使用组件，将其作为标签来使用 -->
<template>
  <div id="app">
    <h1>App根组件</h1>
    <hr style="border-color: red">

    <div id="box">
      <!-- 以标签的形式使用组件 -->
      <Left></Left>
      <Right></Right>
    </div>
  </div>
</template>
```

#### 全局注册组件

> 使用全局注册组件，如果一个组件频繁的被使用，那么就不要频繁的去私有注册组件

```vue
<!-- 在main.js中全局注册组件 -->
import Left from "@/components/Left"
import Right from "@/components/Right"

Vue.component("Left",Left);
Vue.component("Right",Right);

<!-- 使用组件 -->
<div id="box">
      <!-- 以标签的形式使用组件 -->
      <Left></Left>
      <Right class="right"></Right>
</div>
```

#### 组件的props属性

> props是组件的自定义属性，在封装通用组件的时候，合理的使用props属性可以极大的提高组件的复用性
>
> 概念: 一个组件中有一个属性，这个组件被其它多个组件调用，但是调用的每个组件使用被调用的那个组件里的属性时，都希望能
>
> 自定义那个被调用的属性的值

```vue
<!-- 先定义一个Count.vue组件 -->
<template>
  <!-- 因为自定义了init属性,所以这里绑定的是init属性,而不再是count属性 -->
  <!-- 当通过this来获取init的值后，也可以这样写: count的值是: {{ count }}-->
  <p>count的值是: {{ init }}</p>
</template>

<script>
export default {
  name: "Count",
  data() {
    return {
        // 可以通过this来获取init的值并且修改，因为props里面的是只读的
      //count: 0
        count: this.init
    }
  },
  // 自定义init属性
  props: ["init"]
}
</script>
<!-- 然后再main.js中注册Count.vue组件 -->
import Count from "@/components/Count";
Vue.component("Count",Count);

<!-- 最后在Left和Right组件中使用 -->
<!-- Left.vue -->
<Count init="6"></Count>
<!-- Right.vue -->
<Count init="9"></Count>
```

##### props属性的其它配置属性

> props可以设置默认值，需要将其定义为对象格式，而不是数组格式

```js
props: {
    initA: {
      default: 0,
      // 规定props属性传过来的值必须是数值类型
      type: Number,
      // 必填校验，对default默认值无效，只对传递的参数值生效，也就是哪怕default默认值有值，但是使用的时候没有传值就会报错
      required: true
    },
    initB: {
      default: 1
}
```



#### 样式冲突

> 在vue里面都是以组件的形式去互相使用，如果有一些组件里面有相同的元素，这样的话就会产生修改一个组件里元素的样式，其它
>
> 有相同元素的组件里元素的样式都发生了改变，这样就产生了样式冲突

**解决**

```vue
<!-- 在每一个组件的style结构里面加上scope属性 -->
<style scoped>

</style>

<!-- 加上上面的属性之后，就会产生另一个问题，如果想要在父组件里面直接修改子组件里元素的样式，就会不起效果 -->
<!-- 或者是在项目中使用了其它第三方组件库，但是又需要修改第三方组件库的样式，所以这个时候也需要使用到/deep/ -->
<!-- 这个时候需要使用/deep/ -->
<!-- 例如:在App父组件里引用了另一个子组件，子组件里有一个h5元素，想要在App.vue这个父组件里直接修改h5这个元素样式 -->
/deep/ h5 {}
```



### vue的生命周期&生命周期函数

> vue的生命周期分为三个阶段: 创建——>运行——>销毁，每个阶段里还有不同的阶段和不同的生命周期函数
>
> 创建: 创建前——>创建完成——>渲染前——>渲染完成
>
> 运行: 运行前——>正在运行
>
> 销毁: 销毁前——>已经销毁

#### 生命周期函数

> 生命周期函数的定义与data、methods等属性评级

```js
  // 创建阶段的第一个生命周期函数,创建之前
  beforeCreate() {
  },
  // 创建阶段的第二个生命周期函数,创建完成
  // 这个函数较常用，在methods里面定义方法发起ajax请求获取数据，然后在这个生命周期函数里调用那个方法，将获取到的数据转存到data
  // 供template模板渲染数据的时候使用
  created() {
  },
  // 创建阶段的第三个生命周期函数,渲染之前
  beforeMount() {
  },
  // 创建阶段的第四个生命周期函数,渲染完成
  // 如果要操作组件的DOM元素，最早只能在这个阶段，因为只有在mounted阶段才把页面渲染完成
  mounted() {
  },
  // 运行阶段的第一个生命周期函数,运行之前
  // 根据最新的数据重新渲染DOM结构，这个时候数据已经是最新的，但是DOM结构还不是最新的
  beforeUpdate() {
  },
  // 运行阶段的第二个生命周期函数,正在运行
  // 这个时候的数据和DOM结构都已经是最新的
  updated() {
  },
  // 销毁阶段的第一个生命周期函数,销毁之前
  beforeDestroy() {
  },
  // 销毁阶段的第二个生命周期函数,已经销毁
  destroyed() {
  }
```



### 组件之间的数据共享

> 在项目中，组件之间最常见的关系就是父子和兄弟关系

#### 父组件向子组件共享数据

> 父组件向子组件共享数据，使用自定义属性props属性即可，即在子组件中定义props自定义属性，然后父组件在使用子组件时，将自己data中的属性通过props属性传递给子组件

```vue
<!-- 父组件 -->
<template>
  <Son :init="num"></Son>
</template>
<script>
export default {
  name: "App",
  data() {
      return {
          num: 1
      }
  }
}
</script>


<!-- 子组件Son.vue，这样父组件传递过来的值就是父组件的num的值，也就是1 -->
<template>
  <p>父组件传递过来的count的值是: {{ init }}</p>
</template>
<script>
export default {
  name: "Count",
  // 自定义init属性
  // props: ["init"]
  props: {
    init: {
      default: 0,
      type: Number,
      required: true
    }
  }
}
</script>
```

#### 子组件向父组件共享数据

> 子组件向父组件共享数据用到了自定义事件$emit() 方法

**父组件App.vue**

```vue
<template>
  <div id="app">
    <h1>App组件</h1>
    <h3>来自子组件Son.vue的值count:{{ countFrom }}</h3>
    <hr>
    <!-- 在子组件标签上绑定事件,必须要和子组件那边$emit()方法的第一个参数名称一致 -->
    <Son @numchange="getNumFrom"></Son>
  </div>
</template>
<script>
import Son from "@/components/Son";
export default {
  name: 'App',
  components: {
    Son
  },
  data() {
    return {
      countFrom: 0
    }
  },
  methods: {
    // 这里的参数val就是子组件传递过来的值
    getNumFrom(val) {
      this.countFrom = val;
    }
  }
}
</script>
```

**子组件Son.vue**

```vue
<template>
<div id="son-container">
  <h1>子组件count的值:{{ count }}</h1>
  <button @click="add">count+1</button>
</div>
</template>
<script>
export default {
  name: "Son",
  data() {
    return {
      count: 0
    }
  },
  methods: {
    add() {
      this.count ++;
      // 子组件给父组件共享数据
      // $emit(para1,para2),两个参数,参数是父组件那边绑定的事件名称,也就是第一个参数必须要和父组件那边绑定的事件名称一致
      // 第二个参数就是要传给父组件的数据
      this.$emit("numchange",this.count);
    }
  }
}
</script>
```

#### 兄弟组件之间的数据共享

> 兄弟组件之间的数据共享通过eventBus.js来实现，主要是在这个js文件中定义一个vue实例然后导出，在通过导出的vue实例来实现

##### 先定义eventBus.js文件

```js
import Vue from "vue";

// new一个vue实例,然后导出,通过导出的vue实例来实现兄弟组件之间的数据共享
export default new Vue();
```

##### 发送数据的组件

```vue
<!-- Son.vue组件发送数据 -->
<template>
<div id="son-container">
  <button @click="send">点击将数据共享给C组件</button>
</div>
</template>

<script>
// 兄弟之间要想共享数据,首先要导入定义的eventBus组件
import bus from "./eventBus.js";
export default {
  name: "Son",
  data() {
    return {
      shareMsg: "千山鸟飞绝"
    }
  },
  methods: {
    send() {
      // 兄弟组件之间共享数据,发送方使用$emit() 方法发送数据,同父子之间共享数据一致用法
      // 通过导入的bus里面new 的vue实例来发送数据
      bus.$emit("share",this.shareMsg);
    }
  }
}
</script>
```

##### 接收数据的组件

```vue
<!-- C.vue -->
<template>
  <div>
    <p>C组件从Son组件接收到的数据是:{{ msg }}</p>
  </div>
</template>

<script>
// 兄弟组件接收数据,首先也要先导入定义的eventBus组件
import bus from "@/components/eventBus.js";
export default {
  name: "C",
  data() {
    return {
      msg: ""
    }
  },
  created() {
    // 固定用法,$on() 来接收数据, 回调函数里的参数就是发送发组件传递过来的数据,类似jq里的$().on("click",function() {});
    bus.$on("share", (val) => {
      this.msg = val;
    });
  }
}
</script>
```



### ref引用

> ref用来辅助开发者在不依赖于jquery的情况下，获取DOM元素或组件的引用，在vue中也不推荐去安装和使用jquery
>
> ref不仅可以获取DOM元素，也可以获取vue组件，用法都是一样的

#### 初步使用

```vue
<!-- HelloWorld.vue组件 -->
<template>
  <div class="hello">
    <!-- 在DOM元素中定义ref属性，就像DOM元素的id，class一样，ref的值最好不要重复 -->
    <h1 ref="title">{{ msg }}</h1>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  mounted() {
    // 使用$refs.名称去拿到对应的DOM元素，然后就可以去操作对应元素的样式了
    this.$refs.title.style.color = "red";
  }
}
</script>

<!-- 在App.vue组件中操作HelloWorld.vue组件的属性和方法 -->
<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
    <button @click="resetHello">重置HelloWorld组件的count值</button>
    <HelloWorld msg="Welcome to Your Vue.js App" ref="hello"/>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'

export default {
  name: 'App',
  components: {
    HelloWorld
  },
  methods: {
    resetHello() {
      // 通过refs获取到组件
      const hello = this.$refs.hello;
      // 操作组件的data的属性
      // hello.count = 0;
      // 调用组件的方法
      hello.resetCount();
    }
  }
}
</script>
```

#### $nextTick()的使用

> 当数据发生改变的时候，DOM元素就会重新渲染，而一些代码就需要放在DOM元素被渲染完成之后才能正常执行，this.$nextTick(callback) 方法就能将代码延迟到DOM元素被渲染完成时才去执行，从而保证能获取到页面上最新的DOM元素

```vue
<script>
export default {
  name: 'HelloWorld',
  methods: {
    countAdd() {
      this.count ++;
    },
    resetCount() {
      this.count = 0;
    },
    show() {
        // 只有页面渲染完成时才能获取到DOM，如果需要获取数据变化之后的DOM，所以就需要使用this.$nextTick(callback)方法
        this.$nextTick(() => {
            //todo
        });
    }
  }
}
</script>
```

### 提高性能的数组方法

```js
// some() 方法，只要符合了筛选的条件就可以return true来终止循环
const arr = [1,2,3,5,2,21,13];
arr.some((item,index) => {
    if(item === 2){
        console.log(index);
        return true;
    }
});
//every() 方法，只有数组里的每一个元素都符合条件才会返回true，否则返回false
const f = arr.every(item => item > 30);
// 返回的是false


//reduce() 方法，累加器
// 语法格式: array.reduce((累加的值，循环项) => {}, 初始值);
const res = arr.reduce((amount,item) => amount += item, 0);
// 结果是1,2,3,5,2,21,13相加的结果: 47
```



### 动态组件

> 组件也可以实现像DOM元素一样的去动态的显示和隐藏
>
> 利用vue提供的<component>标签就可以实现组件的动态渲染

```vue
<!-- is属性是用来指定组件名称，而用:绑定is，就可以动态的去切换组件的名称，以此来实现组件的动态渲染 -->
<component :is="componentName"></component>
```

```vue
<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
    <button @click="componentName = 'HelloWorld'">显示HelloWorld</button>
    <button @click="componentName = ''">隐藏HelloWorld</button>
    
    <!-- 因为动态的去显示一个组件的话,那么这个组件就会被频繁的创建和销毁,组件里的数据也会重新初始化 -->
    <!-- 所以使用vue提供的keep-alive标签去将需要动态展示的组件包裹起来,这样就会将里面的组件进行缓存
         就算将其隐藏了也不会进行销毁,数据也不会被初始化
     -->
    <!-- include属性可以指定哪些组件被缓存,如果有多个,那么以英文格式的逗号分隔,传的值是注册的组件的名称
         exclude属性可以指定哪些组件不被缓存,如果有多个,那么以英文格式的逗号分隔
         但是这两个属性不能同时使用,只能二选一,一般使用include属性即可
     -->
    <keep-alive include="HelloWorld,componentName2" exclude="HelloWorld,componentName2">
      <component :is="componentName"></component>
    </keep-alive>
  </div>
</template>
<script>
import HelloWorld from './components/HelloWorld.vue'
export default {
  name: 'App',
  components: {
    HelloWorld
  },
  data() {
    return {
      componentName: "HelloWorld",
      count: 0
    }
  }
}
</script>

<!-- HelloWorld.vue -->
<template>
  <div class="hello">
    <h1>HelloWorld组件</h1>
    <button @click="num++">num+1</button>
    <p>值:{{ num }}</p>
  </div>
</template>
<script>
export default {
  name: 'HelloWorld',
  data() {
    return {
      num: 0
    }
  },
  // keep-alive 对应的生命周期函数,激活状态的函数
  activated() {
    console.log("组件被激活了,activated")
  },
  // keep-alive 对应的生命周期函数,缓存状态的函数
  deactivated() {
    console.log("组件被缓存了,deactivated")
  }
}
</script>
```



### 插槽

> 在封装组件的时候，可以预留一个插槽，允许组件的调用者在使用组件的时候插入自己自定义的内容

```vue
<template>
  <div id="app">
    <HelloWorld>
    <!-- 在使用插槽的时候,外面要用template标签包括起来,在标签中使用v-slot:插槽名的方法,去指定调用的是哪个插槽
             v-slot: 简写形式为 #,# 后面写插槽的name
     -->
      <template v-slot:slot1>
        <p>这是组件调用者自定义的内容,这是插槽1</p>
      </template>
	  <!-- 接收插槽定义的属性值,接收到的是一个对象,这种使用方式叫作用域插槽，有name属性的插槽叫具名插槽 -->
      <template #slot2="obj">
        <p>这是组件调用者自定义的内容,这是插槽2</p>
		<p>{{ obj.msg }}</p>
      </template>
    </HelloWorld>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'
export default {
  name: 'App',
  components: {
    HelloWorld
  }
}
</script>

<!-- HelloWorld.vue -->
<template>
  <div class="hello">
    <!-- 声明一个插槽 -->
    <!-- 定义插槽的时候,必须以name属性给插槽命名 -->
    <slot name="slot1">
      这是插槽的默认内容,如果用户指定了内容,就会覆盖掉这块内容
    </slot>
    <!-- 在封装组件插槽的时候,可以定义一些属性和对应的值,在调用组件插槽的时候,能接收到这些数据,接收到的是一个对象 -->
    <slot name="slot2" msg="hello vue.js"></slot>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld'
}
</script>
```



### 自定义指令

> 在vue中，除了使用vue提供的指令，还可以自定义指令去使用，分为局部和全局的自定义指令

#### 局部自定义指令

> 局部自定义的指令实在vue组件里面定义，只能自己使用

```vue
<script>
export default {
  name: 'App',
  // 自定义私有指令
  directives: {
    color: {
      // element 是绑定指令的DOM对象
      // binding 是指令后面绑定的值,是一个对象,例如 v-color="red",那么binding.value 就是red
      // bind() 函数只有第一次绑定的时候才生效,也就是只有第一次被绑定的时候才会触发bind() 函数,每当DOM更新时不会触发
      bind(element,binding) {
        element.style.color = "red";
      },
      // 每次DOM更新时被调用,弥补了bind() 函数只被调用一次的不足
      update(element,binding) {
        element.style.color = binding.value;
      }
    },
    // 如果bind函数和update函数逻辑完成相同,则自定义指令方法可以简写
    // color(element,binding) {
    //   element.style.color = binding.value;
    // }
  }
}
</script>
```

#### 全局自定义指令

> 全局自定义指令是在main.js中定义，所有的组件都可以使用，有两种定义方式

```js
// 第一种定义方式
Vue.directive("color",{
  bind(element,binding) {

  },
  update(element,binding) {

  }
})

// 第二种定义方式
Vue.directive("color",function (element,binding) {

})
```



### vue中使用axios的优化

> 在main.js中导入axios，并把axios挂载到vue的原型上，这样每个组件去使用axios的时候，就不用每个组件都需要去导入axios
>
> 并且在main.js中配置axios请求的根路径，这样去挂载有一个缺点，就是不利于API接口的复用

```js
// 先下载axios
npm i axios -S
// 在导入axios
import axios from 'axios'

// 把axios挂载到vue的原型上
// 两种写法都可以，第一种写法是仿造vue的$emit()，$on() 等写法，为了标准化
Vue.prototype.$http = axios;
Vue.prototype.axios = axios;

// 配置axios请求的根路径
axios.defaults.baseURL = "xxxx"

// 使用axios
this.$http.get()
// 第二种配置的写法
this.axios.post()
```



### 路由

#### 概念

> 概念: 路由router，就是对应关系。也就是Hash地址与组件之间的对应关系。

**前端路由的工作方式**

1. 用户点击了页面上的路由链接(就是普通的a链接)
2. 导致了URL地址栏中的==Hash==值发生了变化
3. 前端路由监听到了Hash地址的变化
4. 前端路由把当前Hash地址对应的组件渲染到浏览器中

#### 安装和配置路由

1. 安装路由

   > 安装路由有两种方式

   **第一种方式** 

​		**手动去安装路由**

```js
// 可以在已经创建的vue项目里去手动安装路由
// 先安装路由，也可以不指定版本
npm i vue-router@3.5.2 -S

// 安装完成之后，在src目录下新建一个router文件夹，在router文件夹下新建一个index.js
// 编写index.js文件
import Vue from 'vue'
import VueRouter from 'vue-router'

// Vue.use() 方法，是将某个模块或其它的东西安装为vue项目的插件
// 这里是将VueRouter安装为vue的插件
Vue.use(VueRouter)

// new 一个VueRouter实例
const router = new VueRouter()
// 导出VueRouter实例
export default router

// 最后在main.js中将VueRouter挂载到vue中
// 先导入
import router from './router'

// 再挂载
new Vue({
  router,
  render: h => h(App)
}).$mount('#app')
```

​		**第二种方式**

​		**使用vue-cli创建vue项目的时候，把VueRouter也加进去，项目创建完成之后，配置文件都已全部自动编写并且已经配置好了**
