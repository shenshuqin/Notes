### Promise实现异步编程

### 回调函数解决异步编程

```js
getdata(cb){
    setTimeout(()=>{
        let usrname = '张三',
            cb(username)
    },1000)
}
///获取username
getdata((data)={
    console.log(data)
})
```



#### 基本用法

ES6 规定，`Promise`对象是一个构造函数，用来生成`Promise`实例。

下面代码创造了一个`Promise`实例。

```javascript
const promise = new Promise(function(resolve, reject) {
  // ... some code

  if (/* 异步操作成功 */){
    resolve(value);
  } else {
    reject(error);
  }
});
```

`Promise`构造函数接受一个函数作为参数，该函数的两个参数分别是`resolve`和`reject`。它们是两个函数，由 JavaScript 引擎提供，不用自己部署。

`Promise`实例生成以后，可以用`then`方法分别指定`resolved`状态和`rejected`状态的回调函数。

```javascript
promise.then(function(value) {
  // success
}, function(error) {
  // failure
});
```

`then`方法可以接受两个回调函数作为参数。第一个回调函数是`Promise`对象的状态变为`resolved`时调用，第二个回调函数是`Promise`对象的状态变为`rejected`时调用。

Promise 新建后就会立即执行。

```javascript
let promise = new Promise(function(resolve, reject) {
  console.log('Promise');
  resolve();
});

promise.then(function() {
  console.log('resolved.');
});

console.log('Hi!');

// Promise
// Hi!
// resolved
```

上面代码中，Promise 新建后立即执行，所以首先输出的是`Promise`。然后，`then`方法指定的回调函数，将在当前脚本所有同步任务执行完才会执行，所以`resolved`最后输出。

一般来说，不要在`then`方法里面定义 Reject 状态的回调函数（即`then`的第二个参数），总是使用`catch`方法。

```javascript
// bad
promise
  .then(function(data) {
    // success
  }, function(err) {
    // error
  });

// good
promise
  .then(function(data) { //cb
    // success
  })
  .catch(function(err) {
    // error
  });
```

#### 应用场景

实现异步编程// 100个c -> b -> a

```jsx
var promise = new Promise(function(resolve, reject){
    console.log('............');
    resolve(); // 这是promise的一个机制，只有promise实例的状态变为resolved，才会会触发then回调函数
});

promise.then(function(){
    for(var i=0;i<100;i++) {
        console.log('c')
    }    
})
.then(function(){
    console.log('b')
})
.then(function(){
    console.log('a')
})
// .............->100个c -> b -> a
```

附上：

```js
$.ajax({
    type:'get',
    async:true,//异步请求
    cache:true,//缓存此页面
    url:'',
    dada:data,
    dataType:'json',
    contentType:"application/json;charset=utf-8",
    headers: {"Authorization","Basic "+"cGluZ2FuOjEyMzQ1Na=="}
    header:'',
    success:function(res=>{
                      
    }),
    error:function(error=>{
    
   })
})
```



### Promise.all()

应用场景:当所有图片加载完才显示到页面上

```js
// 所有图片加载完再添加到页面
  function loadImg(src){
    return new Promise((resolve,reject)=>{
      let img=document.createElement('img');
      img.src=src;
      img.onload=function(){
        resolve(img);
      }
      img.onerror=function(err){
        reject(err);
      }
    })
  }

  function showImgs(imgs){
    imgs.forEach(function(img){
      document.body.appendChild(img);
    })
  }

  Promise.all([
    loadImg('http://i4.buimg.com/567571/df1ef0720bea6832.png'),
    loadImg('http://i4.buimg.com/567751/2b07ee25b08930ba.png'),
    loadImg('http://i2.muimg.com/567751/5eb8190d6b2a1c9c.png')
  ]).then(showImgs)

```

图片加载进度条

```js
//在所有图片没有完全加载出来的时候,出现loading图,全部加载完,loading图消失
<img class="loading" src="img/loading.gif">
<div class="progress">
  <div class="progress-bar progress-bar-striped active" role="progressbar" aria-valuenow="45" aria-valuemin="0" aria-valuemax="100" style="width: 0">
  </div>
</div>
<script>
    let num = 0,pList = [];
    for(let i=0;i<8;i++){
        let p = new Promise((resovle,reject)=>{
        let img = new Image();
        img.src = 'img/'+i+'.jpg';
        img.onload = function () {
            resovle(img)
        }
     })
        //图片加载修改进度条
       p.then(()=>{
            num++
            let percent = parseInt(num/8 *1000);
             document.querySelector(".progress-bar").style.width = percent +'%';
        })
      pList.push(p)
    }
//处理所有图片完成之后的事情
   let pAll = promise.all(pList)
   pAll.then((res)=>{
       console.log(res)
      document.querySelector('.loading').style.display = none;
      document.querySelector('.progress').style.display = none;
    })
    </script>
```





### Promise.race()

场景:有一个图片加载完就添加到页面

```js
// 有一个图片加载完就添加到页面
  function loadImg(src){
    return new Promise((resolve,reject)=>{
      let img=document.createElement('img');
      img.src=src;
      img.onload=function(){
        resolve(img);
      }
      img.onerror=function(err){
        reject(err);
      }
    })
  }

  function showImgs(img){
    let p=document.createElement('p');
    p.appendChild(img);
    document.body.appendChild(p)
  }

  Promise.race([
    loadImg('http://i4.buimg.com/567571/df1ef0720bea6832.png'),
    loadImg('http://i4.buimg.com/567751/2b07ee25b08930ba.png'),
    loadImg('http://i2.muimg.com/567751/5eb8190d6b2a1c9c.png')
  ]).then(showImgs)

```

