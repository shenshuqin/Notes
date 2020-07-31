### 1、变量

常见面试题：
```js
for(var a = 0; a < 3; a++) {
  setTimeout(function() {
    console.log(a);
  }, 1000)
}
//输出结果都为3
```
es6处理方法 -- 更加简单明了
```js
for(let a = 0; a < 3; a++) {
  setTimeout(function() {
    console.log(a);
  }, 1000)
}
//输出结果为：0,1,2
```
### 2、函数 （箭头函数）
不需要function关键字来创建函数
ES5
```js
var aa = function() {}
```
ES6
```js
var aa = () => {}
```
### 3、数组
（1）map --映射
```js
let arr=[66,59,80];
let result=arr.map(item => {
  if(item >= 60){
    return "及格"
  }else{
    return "不及格"
  }
});
//result:["及格", "不及格", "及格"]
```
（2）reduce --汇总
```js
let arr = [12,69,180,8763]; 
let result = arr.reduce((tmp, item, index) => {
            console.log(tmp, item, index);   
            return tmp + item;
});
console.log(result);//求和
```
（3）filter --过滤
```js
let arr=[
    {title: '电源线', price: 50},
    {title: '电脑', price: 13000},
    {title: '键盘', price: 120},
    {title: '手机', price: 9000}
];
let result=arr.filter(json=>json.price>=5000);
console.log(result);//[{title: '电脑', price: 13000},{title: '手机', price: 9000}]
```
（4）forEach --迭代
```js
let arr=[12,5,8,9];
arr.forEach((item,index)=>{
   console.log(index+': '+item); //0: 12  1: 5  2: 8  3: 9
});
```
### 4、函数的参数
（1）收集参数
```js
function show(a, b, ...args){
  console.log(args)
}
show(1,2,3,4,5,6) //输出[3,4,5,6]
```
（2）展开数组
```js
const arr1 = [1,2,3];
const arr2 = [4,5,6];
console.log([...arr1, ...arr2]); //输出[1,2,3,4,5,6]
console.log(...arr1); //输出1，2，3
```
（3）默认值
```js
const show = (a=666, b=999) => {
  console.log(a); //输出666
  console.log(b); //输出999
}
show();
```
### 5、解构赋值
```js
let [a,b,c]=[1,2,3,999];
let {e,d,g}={e: 4, d: 5, f: 6, g: 7};
console.log(a,b,c,e,d,g);
//输出1 2 3 4 5 7
```
### 6、字符串
```js
let title='标题';
let content='内容';
let str='<div>\
  <h1>'+title+'</h1>\
  <p>'+content+'</p>\
</div>';
let str2=`<div>
  <h1>${title}</h1>
  <p>${content}</p>
</div>`;
```
###  Promise
promise的作用:

+ 解决回调地狱，使函数顺序执行；
+ 使用then，继续返回一个promise对象可以实现链式调用，使顺序执行的代码调理、清晰
+ 使用构造函数的方法Promise.all(),该函数传一个数组，数组中都是promise对象，等所有的数组中的所有promise都执行完成后开始执行then方法，比如。所有的函数执行完返回的结果存放到results中，也是数组的形式，


题目一
```js
    const promise = new Promise((resolve, reject) => {
        console.log(1)
        resolve()
        console.log(2)
    })
    promise.then(() => {
        console.log(3)
    })
    console.log(4)
```
结果:1 2 4 3
题目二:
```js
const promise = new Promise((resolve, reject) => {
  resolve('success1')
  reject('error')
  resolve('success2')
})
 
promise
  .then((res) => {
    console.log('then: ', res)
  })
  .catch((err) => {
    console.log('catch: ', err)
  })
```
结果:then: success1
解释：构造函数中的 resolve 或 reject 只有第一次执行有效，多次调用没有任何作用，呼应代码二结论：promise 状态一旦改变则不能再变。
题目三:
```js
Promise.resolve(1)
  .then((res) => {
    console.log(res)
    return 2
  })
  .catch((err) => {
    return 3
  })
  .then((res) => {
    console.log(res)
  })
```
结果:1 2
题目四:
红灯3秒亮一次，绿灯1秒亮一次，黄灯2秒亮一次；如何让三个灯不断交替重复亮灯？（用Promise实现
```js
function red() {
    console.log('red');
}
function green() {
    console.log('green');
}
function yellow() {
    console.log('yellow');
}

var light = function (timmer, cb) {
    return new Promise(function (resolve, reject) {
        setTimeout(function () {
            cb();
            resolve();
        }, timmer);
    });
};

var step = function () {
    Promise.resolve().then(function () {
        return light(3000, red);
    }).then(function () {
        return light(2000, green);
    }).then(function () {
        return light(1000, yellow);
    }).then(function () {
        step();
    });
}

step();
```
题目五:
实现mergePromise函数，把传进去的数组按顺序先后执行，并且把返回的数据先后放到数组data中。
```js
const timeout = ms => new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve();
    }, ms);
});

const ajax1 = () => timeout(2000).then(() => {
    console.log('1');
    return 1;
});

const ajax2 = () => timeout(1000).then(() => {
    console.log('2');
    return 2;
});

const ajax3 = () => timeout(2000).then(() => {
    console.log('3');
    return 3;
});

const mergePromise = ajaxArray => {
    // 在这里实现你的代码

};

mergePromise([ajax1, ajax2, ajax3]).then(data => {
    console.log('done');
    console.log(data); // data 为 [1, 2, 3]
});

// 要求分别输出
// 1
// 2
// 3
// done
// [1, 2, 3]
```
题目六:
```js
const first = () => (new Promise((resolve, reject) => {
    console.log(3);
    let p = new Promise((resolve, reject) => {
        console.log(7);
        setTimeout(() => {
            console.log(5);
            resolve(6);
        }, 0)
        resolve(1);
    });
    resolve(2);
    p.then((arg) => {
        console.log(arg);
    });

}));

first().then((arg) => {
    console.log(arg);
});
console.log(4);
```
结果:3 7 4 1 2 5
题目七:封装一个异步加载图片的方法

```js
function loadImageAsync(url) {
    return new Promise(function(resolve,reject) {
        var image = new Image();
        image.onload = function() {
            resolve(image) 
        };
        image.onerror = function() {
            reject(new Error('Could not load image at' + url));
        };
        image.src = url;
     });
}  
```