### 安装Node

- 查看Node环境的版本号
- 下载：https://nodejs.org/en/
- 安装：
  - 傻瓜式安装，一路`next`
  - 安装过再次安装会升级
- 确认Node环境是否安装成功
  - 查看node的版本号：`node --version`
  - 或者`node -v`
- 配置环境变量

### 解析执行JavaScript

1. 创建编写JavaScript脚本文件
2. 打开终端，定位脚本文件的所属目录
3. 输入`node 文件名`执行对应的文件

注意：文件名不要用`node.js`来命名，也就是说除了`node`这个名字随便起，最好不要使用中文。

## 文件的读写

文件读取:

```
//浏览器中的JavaScript是没有文件操作能力的
//但是Node中的JavaScript具有文件操作能力
//fs是file-system的简写，就是文件系统的意思
//在Node中如果想要进行文件的操作就必须引用fs这个核心模块
//在fs这个和兴模块中，就提供了人所有文件操作相关的API
//例如 fs.readFile就是用来读取文件的

//  1.使用fs核心模块
var fs = require('fs');

// 2.读取文件
fs.readFile('./data/a.txt',function(err,data){
   if(err){
        console.log('文件读取失败');
   }
    else{
         console.log(data.toString());
    }
})
```

文件写入：

```
//  1.使用fs核心模块
var fs = require('fs');

// 2.将数据写入文件
fs.writeFile('./data/a.txt','我是文件写入的信息',function(err,data){
   if(err){
        console.log('文件写入失败');
   }
    else{
         console.log(data.toString());
    }
})
```

## http

服务器：

```
// 1.加载http核心模块
var http = require('http');

// 2.使用http.createServer()创建一个web服务器
var server = http.createServer();

// 3.服务器要做的事儿
// 提供服务：对数据服务
// 发请求
//	接收请求
//	处理请求
//	反馈（发送响应）
//	当客户端请求过来，就会自动触发服务器的request请求事件，然后执行第二个参数：回调处理函数
//request 请求对象
//reponse 响应对象
server.on('request',function(request,response){
    console.log('收到客户的请求了'+request.url)
    //给客户端发送响应数据
    response.write('hello');
    //结束响应
    response.end()
})

// 4.绑定端口号，启动服务
server.listen(3000,function(){
    console.log('runing...')
})
```

### 根据不同路径请求数据以及请求头设置

```js
var http = require('http');

var server = http.createServer();
server.on('request',function(req,res){
    let url = req.url;   
    //服务器默认发送的数据,其实是utf-8编码的内容,但是浏览器不知道是utf-8,会按照当前操作系统默认的编码去解析,中文操作系统是gbk,所以会出现乱码
    if(url === '/'){
        //普通文本
     res.setHeader( 'Content-Type': 'text/plain;charset=utf-8' )
      res.end('index page 首页') //简写方式
    }else if(url === '/p'){
        //html格式
      res.setHeader( 'Content-Type': 'text/html;charset=utf-8' )
      res.end('<p>index page 首页</p>') //简写方式
    }
    else if(url === '/product'){
        let products = [
           {
              name:'苹果',price:900
           },{
               name:'香蕉',price:700
           }
         ]
        //响应数据只能是二进制数据和字符串
        res.end(JSON.stringify(products))
    }else{
        res.end('404 Not Found')
    }
})
server.listen(80,function(){
    console.log('runing...')
})

```

#### 请求头

服务器默认发送的数据,其实是utf-8编码的内容,但是浏览器不知道是utf-8,会按照当前操作系统默认的编码去解析,中文操作系统是gbk,所以会出现乱码.

1.普通文本

```js
res.setHeader( 'Content-Type': 'text/plain;charset=utf-8' )
```

2.html文本

```js
res.setHeader( 'Content-Type': 'text/html;charset=utf-8' )
```

3.html文件

```js
var http = require('http');

var server = http.createServer();
server.on('request',function(req,res){
    let url = req.url;   
    if(url === '/'){
        fs.readFile('./data/index.html',function(err,data){
   if(err){
        res.setHeader( 'Content-Type': 'text/plain;charset=utf-8' )
        res.end('文件读取失败');
   }
    else{
        res.setHeader( 'Content-Type': 'text/html;charset=utf-8' );
        res.end(data)
    }
    }
})
server.listen(80,function(){
    console.log('runing...')
})

```

4.图片,不需要指定编码

```js
res.setHeader( 'Content-Type': 'image/jepg' );
```



#### Node模块

使用Node编写应用程序主要就是在使用：

- EcmaScript语言
  - 和浏览器一样，在Node中没有Bom和Dom
- 核心模块
  - 文件操作的fs
  - http服务操作的http
  - url路径操作模块
  - path路径处理模块
  - os操作系统信息
- 第三方模块
  - art-template
  - 必须通过npm来下载才可以使用
- 自己写的模块
  - 自己创建的文件

引入

```js
var 自定义变量名 = require('模块')
```

#### 实例------搭建登录服务器

server.js

```js
//写一个登录的接口
//通过http模块创建一个服务器
const http=require('http')
const url=require('url')
const querystring = require('querystring');
const server = http.createServer((req, res) => {
    //req 请求信息
    //res 回复信息
    // console.log(req)
  
    let string=req.url
    // 过滤浏览器请求图标文件
    if(string!=='/favicon.ico'){
        console.log(string)
        let urlobj=url.parse(string)
        let pathname=urlobj.pathname  //用户截取的地址信息
        let query=querystring.parse(urlobj.query) // 用户get传递的参数
        if(pathname==='/login'){
            console.log(query)
            res.writeHead(200, { 'Content-Type': 'text/plain' }); //回复的格式设置
            if(query.username==='网易'&&query.password==='123'){
                res.end('login ok');
            }else{
                res.end('login nook');
            }
        }else if(pathname==='/reg'){
            res.writeHead(200, { 'Content-Type': 'text/plain' }); //回复的格式设置
            res.end('reg ok');
        }
    
        
    }
 
  });

```

login.js

```js
$(function(){
    $("#login").click(function(){
        let username = $('.username').val();
        let password = $('.password').val();
        let data = {"username":username,"password":password}
        $.ajsx({
            type:"post",
            url:'/login',
            data:data,
            async:false,
            success:(res)=>{
                console.log(res);
            },
            error:(err)=>{
                console.log(err)
            }
        })
    })
})
```

#### Node处理静态资源

将所有静态资源 放在public文件夹统一处理

```js
if(url.indexof('/public') === 0 ){
    fs.readFile('.'+url, function(err,data){
        if(err){
            return res.end('err')
        }else{
            res.end(data)
        }
    })
}
```





模仿Apache

```
// 引用服务
var http = require('http');
var fs = require('fs');
// 引用模板
var template = require('art-template');
// 创建服务
var server = http.createServer();
// 公共路径
var wwwDir = 'D:/app/www';
server.on('request', function (req, res) {
    var url = req.url;
    // 读取文件
    fs.readFile('./template-apche.html', function (err, data) {
        if (err) {
            return res.end('404 Not Found');
        }
        fs.readdir(wwwDir, function (err, files) {
            if (err) {
                return res.end('Can not find www Dir.')
            }
            // 使用模板引擎解析替换data中的模板字符串
            // 去xmpTempleteList.html中编写模板语法
            var htmlStr = template.render(data.toString(), { 
                title: 'D:/app/www/ 的索引',
                files:files 
            });
            // 发送响应数据
            res.end(htmlStr);
        })
    })
});
server.listen(3000, function () {
    console.log('running....');
})
```

## path路径操作模块

> 参考文档：https://nodejs.org/docs/latest-v13.x/api/path.html

- path.basename：获取路径的文件名，默认包含扩展名
- path.dirname：获取路径中的目录部分
- path.extname：获取一个路径中的扩展名部分
- path.parse：把路径转换为对象
  - root：根路径
  - dir：目录
  - base：包含后缀名的文件名
  - ext：后缀名
  - name：不包含后缀名的文件名
- path.join：拼接路径
- path.isAbsolute：判断一个路径是否为绝对路径

# Node中的其它成员(__dirname,__filename)

在每个模块中，除了`require`,`exports`等模块相关的API之外，还有两个特殊的成员：

- `__dirname`，是一个成员，可以用来**动态**获取当前文件模块所属目录的绝对路径
- `__filename`，可以用来**动态**获取当前文件的绝对路径（包含文件名）
- `__dirname`和`filename`是不受执行node命令所属路径影响的

在文件操作中，使用相对路径是不可靠的，因为node中文件操作的路径被设计为相对于执行node命令所处的路径。

所以为了解决这个问题，只需要把相对路径变为绝对路径（绝对路径不受任何影响）就可以了。

就可以使用`__dirname`或者`__filename`来帮助我们解决这个问题

在拼接路径的过程中，为了避免手动拼接带来的一些低级错误，推荐使用`path.join()`来辅助拼接

```
var fs = require('fs');
var path = require('path');

// console.log(__dirname + 'a.txt');
// path.join方法会将文件操作中的相对路径都统一的转为动态的绝对路径
fs.readFile(path.join(__dirname + '/a.txt'),'utf8',function(err,data){
	if(err){
		throw err
	}
	console.log(data);
});
```

## 修改完代码自动重启

我们在这里可以使用一个第三方命名行工具：`nodemon`来帮助我们解决频繁修改代码重启服务器的问题。

`nodemon`是一个基于Node.js开发的一个第三方命令行工具，我们使用的时候需要独立安装：

```
#在任意目录执行该命令都可以
#也就是说，所有需要 --global安装的包都可以在任意目录执行
npm install --global nodemon
npm install -g nodemon

#如果安装不成功的话，可以使用cnpm安装
cnpm install -g nodemon
```

安装完毕之后使用：

```
node app.js

#使用nodemon
nodemon app.js
```

只要是通过`nodemon`启动的服务，则他会监视你的文件变化，当文件发生变化的时候，会自动帮你重启服务器。