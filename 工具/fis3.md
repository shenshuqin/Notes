## fis3
官网：http://fis.baidu.com/fis3/docs/
### 安装
```shell
npm install -g fis3
```
检查是否安装成功
```shell
fis3 -v
```
### 介绍
FIS3 的构建是不会对源码做修改的，而是构建产出到了另外一个目录，并且构建的结果才是用来上线使用的。

#### 进入项目根目录，执行命令，进行构建
```shell
fis3 release -d <path> 
```
构建发布到项目根目录的output目录下
```shell
fis3 release -d ../output
```
在默认不配置的情况下只是对资源相对路径修改成了绝对路径。通过配置文件可以轻松分离开发路径（源码路径）与部署路径。比如我们想让所有的静态资源构建后到 static 目录下。
```shell
// 配置配置文件，注意，清空所有的配置，只留下以下代码即可。
fis.match('*.{png,js,css}', {
  release: '/static/$0'
});
```
同样构建到 ../output 目录下看变化。
```shell
fis3 release -d ../output
```
### 配置文件
> fis.match(selector, props);

默认配置文件为 fis-conf.js，FIS3 编译的整个流程都是通过配置来控制的。FIS3 定义了一种类似 CSS 的配置方式。固化了构建流程，让工程构建变得简单。

> fis.media()

fis.media() 接口提供多种状态功能，比如有些配置是仅供开发环境下使用，有些则是仅供生产环境使用的。

```shell
fis.match('*', {
  useHash: false
});

fis.media('prod').match('*.js', {
  optimizer: fis.plugin('uglify-js')
});
```
命令
```shell
# fis3 release <media>

fis3 release prod
```
编译时使用 prod 指定的编译配置，即对 js 进行压缩。

如上，fis.media() 可以使配置文件变为多份（多个状态，一个状态一份配置）。

```shell
fis.media('rd').match('*', {
  deploy: fis.plugin('http-push', {
    receiver: 'http://remote-rd-host/receiver.php'
  })
});

fis.media('qa').match('*', {
  deploy: fis.plugin('http-push', {
    receiver: 'http://remote-qa-host/receiver.php'
  })
});
```
fis3 release rd push 到 RD 的远端机器上
fis3 release qa push 到 QA 的远端机器上

#### 更多配置接口
我们执行 fis3 inspect 来查看文件命中属性的情况。fis3 inspect 是一个非常重要的命令，可以查看文件分配到的属性，这些属性决定了文件将如何被编译处理。
```shell
 ~ /app.js
 -- useHash false `*.js`   (0th)


 ~ /img/list-1.png
 -- useHash false `*.png`   (2th)


 ~ /img/list-2.png
 -- useHash false `*.png`   (2th)


 ~ /img/logo.png
 -- useHash false `*.png`   (2th)


 ~ /style.css
 -- useHash false `*.css`   (1th)


 ~ ::package
 -- empty
```
#### 文件指纹
文件指纹，唯一标识一个文件。在开启强缓存的情况下，如果文件的 URL 不发生变化，无法刷新浏览器缓存。一般都需要通过一些手段来强刷缓存，一种方式是添加时间戳，每次上线更新文件，给这个资源文件的 URL 添加上时间戳。

而 FIS3 选择的是添加 MD5 戳，直接修改文件的 URL，而不是在其后添加 query。
对 js、css、png 图片引用 URL 添加 md5 戳，配置如下；
```shell
//清除其他配置，只剩下如下配置
fis.match('*.{js,css,png}', {
  useHash: true
});
```
构建到 ../output 目录下看变化。
```shell
fis3 release -d ../output
```
#### 片段编译
有些插件会对文件中的一部分先进行片段编译fis.compile.partial, 这时可以对相应的片段编译配置对应的规则。
```shell
// vue组件中的less片段处理
fis.match('src/vue/**.vue:less', {
  rExt: 'css', //文件最终输出的后缀
  parser: fis.plugin('less'), //使用插件
  release: 'xxx' // 发布路径
});

// 注意：因为组件中的样式片段编译只是编译内容，所以上面的release配置是无效的。要配置其release，需要针对生成的css文件：
fis.match('src/vue/(**.css)', {
  release: '/vue-style/$1'
});

```
#### 压缩资源
为了减少资源网络传输的大小，通过压缩器对 js、css、图片进行压缩是一直以来前端工程优化的选择。在 FIS3 中这个过程非常简单，通过给文件配置压缩器即可。
```shell
// 清除其他配置，只保留如下配置
fis.match('*.js', {
  // fis-optimizer-uglify-js 插件进行压缩，已内置
  optimizer: fis.plugin('uglify-js')
});

fis.match('*.css', {
  // fis-optimizer-clean-css 插件进行压缩，已内置
  optimizer: fis.plugin('clean-css')
});

fis.match('*.png', {
  // fis-optimizer-png-compressor 插件进行压缩，已内置
  optimizer: fis.plugin('png-compressor')
});
```
#### CssSprite图片合并
压缩了静态资源，我们还可以对图片进行合并，来减少请求数量。
FIS3 提供了比较简易、使用方便的图片合并工具。通过配置即可调用此工具并对资源进行合并。
FIS3 构建会对 CSS 中，路径带 ?__sprite 的图片进行合并。为了节省编译的时间，分配到 useSprite: true 的 CSS 文件才会被处理。
```css
li.list-1::before {
  background-image: url('./img/list-1.png?__sprite');
}

li.list-2::before {
  background-image: url('./img/list-2.png?__sprite');
}
```

```shell
// 启用 fis-spriter-csssprites 插件
fis.match('::package', {
  spriter: fis.plugin('csssprites')
})

// 对 CSS 进行图片合并
fis.match('*.css', {
  // 给匹配到的文件分配属性 `useSprite`
  useSprite: true
})
```

#### 功能组合
```shell
// 加 md5
fis.match('*.{js,css,png}', {
  useHash: true
});

// 启用 fis-spriter-csssprites 插件
fis.match('::package', {
  spriter: fis.plugin('csssprites')
});

// 对 CSS 进行图片合并
fis.match('*.css', {
  // 给匹配到的文件分配属性 `useSprite`
  useSprite: true
});

fis.match('*.js', {
  // fis-optimizer-uglify-js 插件进行压缩，已内置
  optimizer: fis.plugin('uglify-js')
});

fis.match('*.css', {
  // fis-optimizer-clean-css 插件进行压缩，已内置
  optimizer: fis.plugin('clean-css')
});

fis.match('*.png', {
  // fis-optimizer-png-compressor 插件进行压缩，已内置
  optimizer: fis.plugin('png-compressor')
});
```

fis3 release 时添加 md5、静态资源压缩、css 文件引用图片进行合并
可能有时候开发的时候不需要压缩、合并图片、也不需要 hash。那么给上面配置追加如下配置；
```shell
fis.media('debug').match('*.{js,css,png}', {
  useHash: false,
  useSprite: false,
  optimizer: null
})
```
fis3 release debug 启用 media debug 的配置，覆盖上面的配置，把诸多功能关掉。
## 调试
FIS3 构建后，默认情况下会对资源的 URL 进行修改，改成绝对路径。这时候本地双击打开文件是无法正常工作的。这给开发调试带来了绝大的困惑。

FIS3 内置一个简易 Web Server，可以方便调试构建结果。

### 目录

构建时不指定输出目录，即不指定 -d 参数时，构建结果被发送到内置 Web Server 的根目录下。此目录可以通过执行以下命令打开。
```shell

fis3 server open

```
### 发布
```shell
fis3 release
```
不加 -d 参数默认被发布到内置 Web Server的根目录下，当启动服务时访问此目录下的资源。

### 启动
```shell
fis3 server start
```
来启动本地 Web Server，当此 Server 启动后，会自动浏览器打开 http://127.0.0.1:8080，默认监听端口 8080
### 文件监听
为了方便开发，FIS3 支持文件监听，当启动文件监听时，修改文件会构建发布。而且其编译是增量的，编译花费时间少。
```shell
fis3 release -w
```
### 浏览器自动刷新
```shell
fis3 release -wL
```
### 发布到远端机器
当我们开发项目后，需要发布到测试机（联调机），一般可以通过如 SMB、FTP 等上传代码。FIS3 默认支持使用 HTTP 上传代码，首先需要在测试机部署上传接收脚本（或者服务），这个脚本非常简单，现在给出了 php 的实现版本，可以把它放到测试机上某个 Web 服务根目录，并且配置一个 url 能访问到即可。
假定这个 URL 是：http://cq.01.p.p.baidu.com:8888/receiver.php

那么我们只需要在配置文件配置
```shell
fis.match('*', {
  deploy: fis.plugin('http-push', {
    receiver: 'http://cq.01.p.p.baidu.com:8888/receiver.php',
    to: '/home/work/htdocs' // 注意这个是指的是测试机器的路径，而非本地机器
  })
})
```
可能上传测试机是最后联调时才会有的，更好的做法是设置特定 media。
```shell
// 其他配置
...
fis.media('qa').match('*', {
  deploy: fis.plugin('http-push', {
    receiver: 'http://cq.01.p.p.baidu.com:8888/receiver.php',
    to: '/home/work/htdocs' // 注意这个是指的是测试机器的路径，而非本地机器
  })
});
```
fis3 release qa 上传测试机器
fis3 release 产出到本地测试服务器根目录
## 内置语法
嵌入资源即内容嵌入，可以为工程师提供诸如图片base64嵌入到css、js里，前端模板编译到js文件中，将js、css、html拆分成几个文件最后合并到一起的能力。有了这项能力，可以有效的减少http请求数，提升工程的可维护性。
### 在html中嵌入资源
在html中可以嵌入其他文件内容或者base64编码值，可以在资源定位的基础上，给资源加 ?__inline 参数来标记资源嵌入需求。
+ html中嵌入图片base64
源码：
```shell
 <img title="百度logo" src="images/logo.gif?__inline"/>
```
编译后：
```shell
 <img title="百度logo" src="data:image/gif;base64,R0lGODlhDgGBALMAAGBn6eYxLvvy9PnKyfO...Jzna6853wjKc850nPeoYgAgA7"/>
```
+ html中嵌入样式文件
```shell
 <link rel="stylesheet" type="text/css" href="demo.css?__inline">
```
+ html中嵌入脚本资源
源码：
```shell
<script type="text/javascript" src="demo.js?__inline"></script>
```
编译后：
```shell
  <script type="text/javascript">console.log('inline file');</script>
```
+ html中嵌入页面元素
源码：
```shell
<link rel="import" href="demo.html?__inline">
```
编译后：
```shell
<!-- this is the content of demo.html -->
  <h1>demo.html content</h1>
```
### 在js中嵌入资源
在js中，使用编译函数 __inline() 来提供内容嵌入能力。可以利用这个函数嵌入图片的base64编码、嵌入其他js或者前端模板文件的编译内容， 这些处理对html中script标签里的内容同样有效。
 + 在js中嵌入js文件
 源码：
 ```shell
   __inline('demo.js');
 ```
 + 在js中嵌入图片base64
 ```shell
  var img = __inline('images/logo.gif');
 ```
 + 在js中嵌入其他文本文件
 ```shell
 var css = __inline('a.css');
 ```
 ### 在css中嵌入资源
 + 在css文件中嵌入其他css文件
 ```shell
   @import url('demo.css?__inline');
 ```