### js中forEach，for in，for of循环的用法
一、一般的遍历数组的方法:
```js
  var array = [1,2,3,4,5,6,7];  
    for (var i = 0; i < array.length; i) {  
        console.log(i,array[i]);  
    }  
```
二、用for in的方遍历数组
```js
  for(let index in array) {  
        console.log(index,array[index]);  
    };  
```
三、forEach
```js
array.forEach(v=>{  
    console.log(v);  
});
array.forEach（function(v){  
    console.log(v);  
});
 
```
四、用for in不仅可以对数组,也可以对enumerable对象操作
```js
var A = {a:1,b:2,c:3,d:"hello world"};  
for(let k in A) {  
    console.log(k,A[k]);  
} 
```
五、在ES6中,增加了一个for of循环,使用起来很简单
```js
  for(let v of array) {  
        console.log(v);  
    };  
      let s = "helloabc"; 

      for(let c of s) {  

      console.log(c); 

     }
```
总结来说:for in总是得到对像的key或数组,字符串的下标,而for of和forEach一样,是直接得到值
结果for of不能对象用
对于新出来的Map,Set上面

```js
 var set = new Set();  
    set.add("a").add("b").add("d").add("c");  
    var map = new Map();  
    map.set("a",1).set("b",2).set(999,3);  
    for (let v of set) {  
        console.log(v);  
    }  
    console.log("--------------------");  
    for(let [k,v] of map) {  
        console.log(k,v);  
    }  
```
javascript遍历对象详细总结

1.原生javascript遍历

（1）for循环遍历
```js
let array1 = ['a','b','c'];
for (let i = 0;i < array1.length;i++){
  console.log(array1[i]);  // a  b  c 
}
```
2）JavaScript 提供了 foreach()  map() 两个可遍历 Array对象 的方　　　　

    forEach和map用法类似，都可以遍历到数组的每个元素，而且参数一致； 
不同点：

  forEach() 方法对数组的每个元素执行一次提供的函数。总是返回undefined；

  map() 方法创建一个新数组，其结果是该数组中的每个元素都调用一个提供的函数后返回的结果。返回值是一个新的数组；
  例子如下：

  ```js
  var array1 = [1,2,3,4,5];
 
var x = array1.forEach(function(value,index){
 
    console.log(value);   //可遍历到所有数组元素
 
    return value + 10
});
console.log(x);   //undefined    无论怎样，总返回undefined
 
var y = array1.map(function(value,index){
 
    console.log(value);   //可遍历到所有数组元素
 
    return value + 10
});
console.log(y);   //[11, 12, 13, 14, 15]   返回一个新的数组
  ```
对于类似数组的结构，可先转换为数组，再进行遍历
```js
let divList = document.querySelectorAll('div');   //divList不是数组，而是nodeList
 
//进行转换后再遍历
[].slice.call(divList).forEach(function(element,index){
  element.classList.add('test')
})
 
 
Array.prototype.slice.call(divList).forEach(function(element,index){
  element.classList.remove('test')
})
 
[...divList].forEach(function(element,index){   //<strong>ES6写法</strong>
  //do something
})
```
（3）for ··· in ···     /      for ··· of ···

for...in 语句以任意顺序遍历一个对象的可枚举属性。对于每个不同的属性，语句都会被执行。每次迭代时，分配的是属性名　　
```js

let array2 = ['a','b','c']
let obj1 = {
  name : 'lei',
  age : '16'
}
 
for(variable  in array2){   //variable  为 index
  console.log(variable )   //0 1 2
}
 
for(variable  in obj1){   //variable 为属性名
  console.log(variable)   //name age
}
```
 ES6新增了 遍历器(Iterator)机制，为不同的数据结构提供统一的访问机制。只要部署了Iterator的数据结构都可以使用 for ··· of ··· 完成遍历操作  ( Iterator详解 ：  http://es6.ruanyifeng.com/#docs/iterator )，每次迭代分配的是 属性值

 原生具备 Iterator 接口的数据结构如下：

 Array   Map Set String TypedArray 函数的arguments对象 NodeList对象
 ```js
let array2 = ['a','b','c']
let obj1 = {
  name : 'lei',
  age : '16'
}
 
for(variable  of array2){   //<strong>variable  为 value</strong>
  console.log(variable )   //'a','b','c'
}
 
for(variable  of obj1){  //<strong>普通对象不能这样用</strong>
  console.log(variable)   // 报错 ： main.js:11Uncaught TypeError: obj1[Symbol.iterator] is not a function
}<br><br>let divList = document.querySelectorAll('div');<br><br>for(element of divlist){  //可遍历所有的div节点<br>　　//do something <br>}
 ```
 　for...in循环会遍历一个object所有的可枚举属性。

　　　   for...of会遍历具有iterator接口的数据结构

　　　   for...in 遍历（当前对象及其原型上的）每一个属性名称,而 for...of遍历（当前对象上的）每一个属性值
```js
　Object.prototype.objCustom = function () {};
Array.prototype.arrCustom = function () {};

let iterable = [3, 5, 7];
iterable.foo = "hello";

for (let i in iterable) {
  console.log(i); // logs 0, 1, 2, "foo", "arrCustom", "objCustom"
}

for (let i of iterable) {
  console.log(i); // logs 3, 5, 7
}
```