### vue解决跨域

1.在vue.config.js文件中使用deServer.proxy选项进行代理配置

```js
module.exports = {
    devServer: {
        port: 8888, // 端口号，如果端口号被占用，会自动提升1
        host: "localhost", //主机名， 127.0.0.1，  真机 0.0.0.0
        https: false, //协议
        open: true, //启动服务时自动打开浏览器访问
        proxy: { // 开发环境代理配置
           '/dev-api' :{
                // 目标服务器地址，代理访问 http://mengxuegu.com:7300/mock/5d477ccbfacc296cd6834fe5
                target:http://mengxuegu.com:7300/mock/5d477ccbfacc296cd6834fe5,
                changeOrigin: true, // 开启代理服务器，
                pathRewrite: {
                    // 将 请求地址前缀 /dev-api 替换为 空的，
                    '^/dev-api': '',
                }
            }
        }
    },

    lintOnSave: false, // 关闭格式检查
    productionSourceMap: false, // 打包时不会生成 .map 文件，加快打包速度 
}
```

2.将封装axios的文件进行修改

```js
const request = axios.create({
     baseURL: '/dev-api', 
    // baseURL: '/',
    timeout: 5000 // 请求超时，5000毫秒
})
```

3.接口文件

```js
import request from '@/utils/request'

export function login(username, password) {
    return request({ // Promise
        url: '/user/login',
        method: 'post',
        data: {
            username, // username: username
            password
        }
    })
}
}
```

#### 配置不同环境  常量值

1.在项目根目录下创建 .env.development 和 .env.production 文件

2.env.development 文件配置（注意开发和生产环境配置不要搞反了） 

```js
# 只有以 VUE_APP_ 开头的变量会被 webpack 静态嵌入到项目中进行使用 process.env.VUE_APP_xxxxxx


# 目标服务接口地址，这个地址是按照你自已环境来的, 添加 或者更改配置后，需要重启服务
VUE_APP_SERVICE_URL = 'http://mengxuegu.com:7300/mock/5d477ccbfacc296cd6834fe5'

# 开发环境的前缀
VUE_APP_BASE_API = '/dev-api'
```

3.env.production 文件配置（build之后将匹配这个文件）

```js
VUE_APP_BASE_API = '/pro-api'
```

4.重构代理配置

```js
module.exports = {
    devServer: {
        port: 8888, // 端口号，如果端口号被占用，会自动提升1
        host: "localhost", //主机名， 127.0.0.1，  真机 0.0.0.0
        https: false, //协议
        open: true, //启动服务时自动打开浏览器访问
        proxy: { // 开发环境代理配置
            // '/dev-api': {
            [process.env.VUE_APP_BASE_API] :{
                // 目标服务器地址，代理访问 
                target: process.env.VUE_APP_SERVICE_URL,
                changeOrigin: true, // 开启代理服务器，
                pathRewrite: {
                    // 将 请求地址前缀 /dev-api 替换为 空的，
                    // '^/dev-api': '',
                    [ '^' + process.env.VUE_APP_BASE_API]: ''
                }
            }
        }
    },

    lintOnSave: false, // 关闭格式检查
    productionSourceMap: false, // 打包时不会生成 .map 文件，加快打包速度 
}
```

5.修改 utils/request.js 文件配置： baseURL: process.env.VUE_APP_BASE_API, 

```js
const request = axios.create({
    // baseURL: '/dev-api', 
    baseURL: process.env.VUE_APP_BASE_API, 
    // baseURL: '/',
    timeout: 5000 // 请求超时，5000毫秒
})
```

注意当 npm run build 之后会自动运用生产环境

nginx代理

```shell
location /pro-api { # 代理转发后台服务接口
proxy_pass http://mengxuegu.com:7300/mock/5d477ccbfacc296cd6834fe5; }
```

附录：nginx

```shell
# 启动的进程数量
worker_processes  1;

events {
    # 单个进程的并发量
    worker_connections  1024; # 总并发量 = 进程数量 * 单个进程的并发量
}

http {
    include       mime.types;
    default_type  application/octet-stream;

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65; # 连接服务器超时时长, 65秒

    #gzip  on;

    server { # 一个虚拟主机配置, 多个虚拟机配置多个 server 即可
        listen       8888; # 端口号
        server_name  vue.mengxuegu.com; # 域名解析
		
        location / { # 配置默认访问页
            #root   html; # 网站根目录
            root /usr/local/nginx/html/mxg-mms;
            index  index.html index.htm; # 首页
        }
		
		location /pro-api {
			# 代理转发后台服务接口 匹配到 /pro-api
            proxy_pass http://mengxuegu.com:7300/mock/5d477ccbfacc296cd6834fe5;
        }
		
        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        # 配置错误页面
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
       
    }

}
```

