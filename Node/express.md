### Express

原生的http在某些方面表现不足以应对我们的开发需求，所以就需要使用框架来加快我们的开发效率，框架的目的就是提高效率，让我们的代码高度统一。

在node中有很多web开发框架。主要学习express

- `http://expressjs.com/`,其中主要封装的是http。

- ```
  // 1 安装
  // 2 引包
  var express = require('express');
  // 3 创建服务器应用程序
  //      也就是原来的http.createServer();
  var app = express();
  
  // 公开指定目录
  // 只要通过这样做了，就可以通过/public/xx的方式来访问public目录中的所有资源
  // 在Express中开放资源就是一个API的事
  app.use('/public/',express.static('/public/'));
  
  //模板引擎在Express中开放模板也是一个API的事
  
  // 当服务器收到get请求 / 的时候，执行回调处理函数
  app.get('/',function(req,res){
      res.send('hello express');
  })
  
  // 相当于server.listen
  app.listen(3000,function(){
      console.log('app is runing at port 3000');
  })
  ```

### 学习Express

#### 起步

1.安装

```shell
cnpm install express
```

2.hello world

```js
// 引入express
var express = require('express');

// 1. 创建app
var app = express();

//  2. 
app.get('/',function(req,res){
    // 1
    // res.write('Hello');
    // res.write('World');
    // res.end()

    // 2
    // res.end('hello world');

    // 3
    res.send('hello world');
})

app.listen(3000,function(){
    console.log('express app is runing...');
})
```

##### 基本路由

路由：

- 请求方法
- 请求路径
- 请求处理函数

get:

```
//当你以get方法请求/的时候，执行对应的处理函数
app.get('/',function(req,res){
    res.send('hello world');
})
```

post:

```
//当你以post方法请求/的时候，执行对应的处理函数
app.post('/',function(req,res){
    res.send('hello world');
})
```

##### Express静态服务API

```
// app.use不仅仅是用来处理静态资源的，还可以做很多工作(body-parser的配置)
app.use(express.static('public'));

app.use(express.static('files'));

app.use('/stataic',express.static('public'));
// 引入express
var express = require('express');

// 创建app
var app = express();

// 开放静态资源
// 1.当以/public/开头的时候，去./public/目录中找对应资源
// 访问：http://127.0.0.1:3000/public/login.html
app.use('/public/',express.static('./public/')); 

// 2.当省略第一个参数的时候，可以通过省略/public的方式来访问
// 访问：http://127.0.0.1:3000/login.html
// app.use(express.static('./public/'));   

// 3.访问：http://127.0.0.1:3000/a/login.html
// a相当于public的别名
// app.use('/a/',express.static('./public/')); 

//  
app.get('/',function(req,res){
    res.end('hello world');
});

app.listen(3000,function(){
    console.log('express app is runing...');
});
```

##### 在Express中配置使用`art-templete`模板引擎

- [art-template官方文档](https://aui.github.io/art-template/)

- 在node中，有很多第三方模板引擎都可以使用，不是只有

  ```
  art-template
  ```

  - 还有ejs，jade（pug），handlebars，nunjucks

安装：

```
npm install --save art-template
npm install --save express-art-template

//两个一起安装
npm i --save art-template express-art-template
```

配置：

```
app.engine('html', require('express-art-template'));
```

使用：

```
app.get('/',function(req,res){
    // express默认会去views目录找index.html
    res.render('index.html',{
           title:'hello world'     
    });
})
```

如果希望修改默认的`views`视图渲染存储目录，可以：

```
// 第一个参数views千万不要写错
app.set('views',目录路径);
```