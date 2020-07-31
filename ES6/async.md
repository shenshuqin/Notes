### async

如果使用promise

```js
      function say() {
          return new Promise(function(resolve, reject) {
              setTimeout(function() {
                  let age = 26
                  resolve(`hello, joel。今年我 ${age} 岁`);
              }, 1000);
         });
      }
     let p =  say();
     p.then((res)=>{
         cosnole.log(res)
     })
```

使用async

```js
 1     function say() {
 2         return new Promise(function(resolve, reject) {
 3             setTimeout(function() {
 4                 let age = 26
 5                 resolve(`hello, joel。今年我 ${age} 岁`);
 6             }, 1000);
 7         });
 8     }
 9 
10     async function demo() {
11         const v = await say(); // 输出：hello, joel。今年我 26 岁  等待这个say 的异步，如果成功把回调 resole 函数的参数作为结果
12         console.log(v);
13     }
14     demo();
```

#### async 是一步一步执行

```js
function say() {
  return new Promise(function(resolve, reject) {
     setTimeout(function() {
          let age = 26
        resolve(`hello, joel，发生了异常。今年我 ${age} 岁`);
      }, 1000);
  });
} 


function sing() {
  return new Promise(function(resolve, reject) {
      setTimeout(function() {
          resolve(`来一首好听的歌吧~~~`);
      }, 2000);
  });
}
async function demo() {
  try {
     const v = await say(); 
     const s = await sing(); 
     console.log(v); // 输出：hello, joel。今年我 26 岁
     console.log(s) // 来一首好听的歌吧~~~
 } catch (e) {
      console.log(e)//如果发生错误直接执行catch捕获错误
}
}
demo();
```





#### ajax请求的举例（涉及到嵌套的回调）

```js
 //定义一个请求news的方法
    function getNews(url) {
        //创建一个promise对象
        let promise = new Promise((resolve, reject) => {
            //初始化promise状态为pending
            //启动异步任务
            let request = new XMLHttpRequest();
            request.onreadystatechange = function () {
                if (request.readyState === 4) {
                    if (request.status === 200) {
                        let news = request.response;
                        resolve(news);
                    } else {
                        reject('请求失败了。。。');
                    }
                }
            };
            request.responseType = 'json';//设置返回的数据类型
            request.open("GET", url);//规定请求的方法，创建链接
            request.send();//发送
        })
        return promise;
    }

    getNews('http://localhost:3000/news?id=2')
        .then((news) => {
            console.log(news);
            document.write(JSON.stringify(news));
            console.log('http://localhost:3000' + news.commentsUrl);
            return getNews('http://localhost:3000' + news.commentsUrl);
        }, (error) => {
            alert(error);
        })
        .then((comments) => {
            console.log(comments);
            document.write('<br><br><br><br><br>' + JSON.stringify(comments));
        }, (error) => {
            alert(error);
        })
```

#### 改进async

```js
 //定义一个请求news的方法
    function getNews(url) {
        //创建一个promise对象
        let promise = new Promise((resolve, reject) => {
            //初始化promise状态为pending
            //启动异步任务
            let request = new XMLHttpRequest();
            request.onreadystatechange = function () {
                if (request.readyState === 4) {
                    if (request.status === 200) {
                        let news = request.response;
                        resolve(news);
                    } else {
                        reject('请求失败了。。。');
                    }
                }
            };
            request.responseType = 'json';//设置返回的数据类型
            request.open("GET", url);//规定请求的方法，创建链接
            request.send();//发送
        })
        return promise;
    }
   async function test(){
       var url = '';
       var res = await getNews(url) //拿到的是resolve的数据
       console.log(res)
   }
test()
```

  

#### 循环加载排行榜音乐信息

```js
async function music () {
    var rankListUrl = '';
    var result = await getAjax(rankListUrl);
    for (let i=0;i<result.length;i++){
        let httpUrl = ''+result[i]
        //耗性能 需要等待渲染完成才可以循环下一个
        //let musicDetail =  await getAjax(httpUrl+result[i]);
        //改进1promise 不需要等,异步
        //getAjax(httpUrl+result[i]).then((musicDetail)=>{
         //   console.log(musicDetail)
       // })
        //改进2 循环不需要等待
        let fn = async function () {
            //获取音乐详情数据
            let musicDetail = await getAjax(httpUrl)
            console.log(musicdetail)
        }
        fn()
}
```

