### Webpack
Webpack是前端的静态模块资源打包工具
目前使用的是webpack2版本
#### 全局安装
```shell
安装最新版本
npm install --global webpack
或者安装指定版本
npm install --global webpack@<version>
```
如果上面安装的是webpack V4+版本,还需要安装webpack-cli
```shell
npm install --global webpack-cli
```
可以通过`npm root -g`查看全局安装目录
如果安装后,webpack不可用.需要配置环境变量
#### webpack打包指令
`webpack 模版入口文件 -o 模版出口文件`
打包配置文件
在项目根目录下新建webpack.config.js
```js
// 引用 Node.js 中的 path 模块，处理文件路径的小工具 
const path = require("path"); 
// 1. 导出一个webpack具有特殊属性配置的对象
 module.exports = { 
     mode:'none',
     // 入口 
     entry: './src/main.js', // 入口模块文件路径 
     // 出口是对象 
     output: { 
         // path 必须是一个绝对路径 , __dirname 是当前js的绝对路径： D:\StudentProject\WebStudy\webpack-demo2 
         path: path.join(__dirname, './dist/'), // 打包的结果文件存储目录 
         filename: 'bundle.js' // 打包的结果文件名 
        } 
    }
```
配置完成后直接输入命令
`webpack`
#### 本地安装
不推荐全局安装,全局安装的 webpack ，在打包项目的时候，使用的是你安装在自己电脑上的
webpack，如果项目到了另一个人的电脑上，他可能安装的是旧版本 webpack。那么就可能涉及兼容性的问题。而且如果他没有在全局安装 webpack 则就无法打包。
本地安装命令:
```shell
npm install --save-dev webpack
npm install --save-dev webpack-cli
```
新建文件:
```shell
npm init -y
npm i -D webpack
npm i -D webpack-cli
```
注意:执行webpack会报错
需要在本地安装的 webpack ，要通过在项目文件夹下 package.json 文件中的 scripts 配置命令映射;
找到项目根目录下的package.json文件并打开，找到"scripts":{}这一段，在其中添加"dev": "webpack"这一行，效果如下：
```js
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "dev": "webpack"
  },
```
然后执行:`npm run dev`即可编译
#### 导入导出
导出默认成员(export default 只能导出一个)
```shell
export default 成员
比如:
export default "hello"
或者:
export default {
	name:'hello'
}
函数导出不能有函数名
export default function(){
	console.log('')
}
```
导入默认成员:
```shell
import xxx from 模版文件
```
导出非默认成员(必须有申明,函数需要函数名):
```shell
export 成员
比如:
export const x="hello";
export const y="12";
export function add(a,b){
	return a+b;
}
```
导出非默认成员
```shell
import {} from '模版文件'
或者全局导入
import *as bar from './bar'
```

#### css-loader打包
打包css
```shell
  npm install --save-dev style-loader css-loader
```
修改webpack.config.js
```js
	// 引用 Node.js 中的 path 模块，处理文件路径的小工具 
const path = require("path"); 
// 1. 导出一个webpack具有特殊属性配置的对象
 module.exports = { 
     mode:'none',
     // 入口 
     entry: './src/main.js', // 入口模块文件路径 
     // 出口是对象 
     output: { 
         // path 必须是一个绝对路径 , __dirname 是当前js的绝对路径： D:\StudentProject\WebStudy\webpack-demo2 
         path: path.join(__dirname, './dist/'), // 打包的结果文件存储目录 
         filename: 'bundle.js' // 打包的结果文件名 
        },
        module:{
            rules:[
                {
                    test:/\.css$/,// 正则表达式不能有单引号，匹配 .css 文件资源
                    use:[// 使用的 Loader ,注意顺序不能错
                        'style-loader',//js识别css
                        'css-loader'//css转换为js
                    ]
                }
            ]
        }
    }
```

#### 图片打包
```shell
npm install --save-dev file-loader
```
配置webpack.config.js
```js
	{
	 test:{ test: /\.(png|svg|jpg|gif)$/, 
	 use: ['file-loader']
	}
```

问题:图片将会显示不出来
解决:
如果直接访问根目录下的 index.html ，那么图片资源路径就无法访问到。
解决方案：就是把 index.html 放到 dist 目录中。
但是 dist 是打包编译的结果，而非源码，所以把 index.html 放到 dist 就不合适。
而且如果我们一旦把打包的结果文件名 bundle.js 改了之后，则 index.html 也要手动修改。
综合以上遇到的问题，可以使用一个插件： html-webpack-plugin 来解决。
####  使用 HtmlWebpackPlugin 插件
作用:解决文件路径问题
+ 将 index.html 打包到 bundle.js 所在目录中
+ 同时也会在 index.html 中自动的 <script> 引入 bundle.js
安装:
```shel
	npm install --save-dev html-webpack-plugin
```
修改webpack.config.js
```js
// 引用 Node.js 中的 path 模块，处理文件路径的小工具 
const path = require("path"); 
// 引入插件 
const HtmlWebpackPlugin = require('html-webpack-plugin');
// 1. 导出一个webpack具有特殊属性配置的对象
 module.exports = { 
     mode:'none',
     // 入口 
     entry: './src/main.js', // 入口模块文件路径 
     // 出口是对象 
     output: { 
         // path 必须是一个绝对路径 , __dirname 是当前js的绝对路径： D:\StudentProject\WebStudy\webpack-demo2 
         path: path.join(__dirname, './dist/'), // 打包的结果文件存储目录 
         filename: 'bundle.js' // 打包的结果文件名 
        },
        // 配置插件
        plugins: [ new HtmlWebpackPlugin({ 
            // 此插件作用是将 index.html 打包到 bundle.js 所在目录中, 
            // 同时也会在 index.html 中自动的 <script> 引入 bundle.js 
            // 注意：其中的文件名 bundle 取决于上面output.filename中指定的名称
             template: './index.html' 
            }) 
        ],
        module:{
            rules:[
                {
                    test:/\.css$/,// 正则表达式，匹配 .css 文件资源
                    use:[// 使用的 Loader ,注意顺序不能错
                        'style-loader',//js识别css
                        'css-loader'//css转换为js
                    ]
                }
            ]
        }
    }
```
修改index.html
```html
<html lang="en"> 
    <head>
        <meta charset="UTF-8"> 
        <title>Document</title> 
    </head>
    <body>
        <!-- 使用了HtmlWebpackPlugin 插件会自动引入bundle.js中 --> <!-- <script src="./dist/bundle.js"></script> --> 
        <div id="app"></div> 
    </body>
```

重新打包:

`npm run build`

运行后，你会发现 dist 目录下多有一个 index.html , 并且文件中自动引入了 bundle.js 

#### 实时重新加载
问题：
每一次手动打包很麻烦，打包后还需要手动刷新浏览器。
解决：
采用 webpack 提供的工具： webpack-dev-server ，它允许在运行时更新所有类型的模块后，而无需手动打
包和刷新页面，会自动打包和刷新页面。可以很大程度提高开发效率。
安装:
```shell
npm install --save-dev webpack-dev-server
```
修改webpack.config.js
```js
	// 引用 Node.js 中的 path 模块，处理文件路径的小工具 
const path = require("path"); 
// 引入插件 
const HtmlWebpackPlugin = require('html-webpack-plugin');
// 1. 导出一个webpack具有特殊属性配置的对象
 module.exports = { 
     mode:'none',
     // 入口 
     entry: './src/main.js', // 入口模块文件路径 
     // 出口是对象 
     output: { 
         // path 必须是一个绝对路径 , __dirname 是当前js的绝对路径： D:\StudentProject\WebStudy\webpack-demo2 
         path: path.join(__dirname, './dist/'), // 打包的结果文件存储目录 
         filename: 'bundle.js' // 打包的结果文件名 
        },
        // 实时重新加载 
        devServer: { 
            contentBase: './dist' 
        },
        // 配置插件
        plugins: [ new HtmlWebpackPlugin({ 
            // 此插件作用是将 index.html 打包到 bundle.js 所在目录中, 
            // 同时也会在 index.html 中自动的 <script> 引入 bundle.js 
            // 注意：其中的文件名 bundle 取决于上面output.filename中指定的名称
             template: './index.html' 
            }) 
        ],
        module:{
            rules:[
                {
                    test:/\.css$/,// 正则表达式，匹配 .css 文件资源
                    use:[// 使用的 Loader ,注意顺序不能错
                        'style-loader',//js识别css
                        'css-loader'//css转换为js
                    ]
                }
            ]
        }
    }
```
修改package.json中的script
```shell
	"scripts": {
    "show": "webpack -v", 
    "build": "webpack",
    "watch-build": "webpack --watch", 
    "dev": "webpack-dev-server --open"
    },
```
打包:`npm run dev`
#### Babel 浏览器兼容性
安装:
```shell
npm install -D babel-loader @babel/core @babel/preset-env
```
配置:
```js
	module: { 
	rules: [ 
	{ 
		test: /\.m?js$/,
        exclude: /(node_modules|bower_components)/, // 排除的目录 
        use: { 
        loader: 'babel-loader', 
        options: { 
           presets: ['@babel/preset-env'] // 内置好的转译工具 
          }
        }
       } 
     ] 
   }
```