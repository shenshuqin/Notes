**typeof、instanceof、Object.prototype.toString.call()、constructor**
##### typeof

```
运算数为数字 typeof(x) = "number" 

字符串 typeof(x) = "string" 

布尔值 typeof(x) = "boolean" 

对象,数组和null typeof(x) = "object" 

函数 typeof(x) = "function" 
```
##### instanceof
```
var a=new Array();
alert(a instanceof Array); // true，
同时 alert(a instanceof Object) //也会返回 true;
这是因为 Array 是 object 的子类。
```

##### Object.prototype.toString.call()

```
var a = "hello world";
var b = [];
var c = function(){};

console.log( object.prototype.toString.call( a ) );
console.log( object.prototype.toString.call( b ) );
console.log( object.prototype.toString.call( c ) );

//结果
[object String];
[object Array];
[object Function];

因此 想要得到具体的类型可以字符串截取： 
 console.log( object.prototype.toString.call( a ).slice( 8, -1) ); 
结果: String
```

##### constructor 属性

```
var test=new Array();

if (test.constructor==Array)
{
document.write("This is an Array");
}
if (test.constructor==Boolean)
{
document.write("This is a Boolean");
}
if (test.constructor==Date)
{
document.write("This is a Date");
}
if (test.constructor==String)
{
document.write("This is a String");
}
//This is an Array
```

# 类数组对象arguments和数组对象的区别

### 类数组对象的定义：

​       1）拥有length属性，其它属性（索引）为非负整数（对象中的索引会被当做字符串来处理）；
​       2）不具有数组所具有的方法；
​      javascript中常见的类数组有 arguments 对象和 DOM 方法的返回结果，比如
document.getElementsByTagName() ；

```jsx
// 类数组示例 1
  var nodeList = document.getElementsByTagName('ul');
  console.log(nodeList instanceof Array); // false 类数组
  console.log(nodeList[0]); // <ul>...</ul>
```

### 类数组转换成数组

```jsx
// 类数组转换成数组
Array.prototype.slice.call(arrayLike)
```

### new的原理
1. 创建一个类的实例：创建一个空对象obj，然后把这个空对象的__proto__设置为Person.prototype(即构造函数的prototype)；

2. 初始化实例：构造函数Person被传入参数并调用，关键字this被设定指向该实例obj；

3. 返回实例obj。
#### __proto__和prototype

1，什么是构造函数

既然是构造函数，那么久要有参数和返回值　

```
`Person = ``function``(name,age,force){`
```

`Person.prototype    ``//Object {constructor:function(name,age,force),__proto__:Object}`

`Person.__proto__ ``//function(){}`

好的，我们的构造函数完成了，现在我们来通过构造函数创建一个对象

```js
var mike = new Person('mike',23,'strong');<br>mike  //Person {name: "mike", age: 23, force: "strong"}<br>mike.prototpe  //undefined<br>mike.__proto__  //Object {constructor:function(name,age,force),__proto__:Object}
```

`alert(mike.__proto__ === Person.prototype)    ``//true`

__proto__按照定义来讲，是对象自带的一个属性，这个__proto__它本身又是一个对象，__proto__的属性，可以被mike读取到，但不会显示在mike对象里，当然__proto__对象中的属性读取优先级要低于mike本身

而prototype呢，则是函数的一个自带属性，其中prototype又有两个元素，1,constructor指向函数本身，2,__proto__同对象中的__proto__

构造函数在创建对象是最大的特点就是会把自身的prototype按值传递给被构造对象的__proto__，因为prototype可以当做对象来看，所以是按引用传递//纠正上次的一个错误

暂时我知道的prototype的作用，就是在构造对象时，将自身的prototype（引用）传递给新函数的__proto__

#### 继承
https://www.cnblogs.com/youyang-2018/p/9304245.html
在js中，继承就是指使一个对象有权去访问另一个对象的能力。
继承的实现方式有很多，常用的有：1.原型继承、2.类式继承、3.组合继承、4.extend方法
#### 函数防抖和节流

https://segmentfault.com/a/1190000016261602

+ 函数防抖和函数节流都是防止某一时间频繁触发，但是这两兄弟之间的原理却不一样。

+ 函数防抖是某一段时间内只执行一次，而函数节流是间隔时间执行
  结合应用场景
  debounce

  函数防抖（debounce）：当持续触发事件时，一定时间段内没有再触发事件，事件处理函数才会执行一次，如果设定的时间到来之前，又一次触发了事件，就重新开始延时。如下图，持续触发scroll事件时，并不执行handle函数，当1000毫秒内没有触发scroll事件时，才会延时触发scroll事件。

  ![img](https://images2018.cnblogs.com/blog/1022151/201806/1022151-20180613144209623-862434090.jpg)

  `// 防抖`
  `function debounce(fn, wait) {    
      var timeout = null;    
      return function() {        
          if(timeout !== null)   clearTimeout(timeout);        
          timeout = setTimeout(fn, wait);    
      }`
  `}`
  `// 处理函数`
  `function handle() {    
      console.log(Math.random());` 
  `}`
  `// 滚动事件`
  `window.addEventListener('scroll', debounce(handle, 1000));`

  应用场景:
  
+ search搜索联想，用户在不断输入值时，用防抖来节约请求资源。
  + window触发resize的时候，不断的调整浏览器窗口大小会不断的触发这个事件，用防抖来让其只触发一次

  throttle
  
  函数节流（throttle）：当持续触发事件时，保证一定时间段内只调用一次事件处理函数。节流通俗解释就比如我们水龙头放水，阀门一打开，水哗哗的往下流，秉着勤俭节约的优良传统美德，我们要把水龙头关小点，最好是如我们心意按照一定规律在某个时间间隔内一滴一滴的往下滴。如下图，持续触发scroll事件时，并不立即执行handle函数，每隔1000毫秒才会执行一次handle函数。
  
  ![img](https://images2018.cnblogs.com/blog/1022151/201806/1022151-20180613144342847-660853255.jpg)
  
```js
// 节流throttle代码（定时器）：
var throttle = function(func, delay) {            
    var timer = null;            
    return function() {                
        var context = this;               
        var args = arguments;                
        if (!timer) {                    
            timer = setTimeout(function() {                        
                func.apply(context, args);                        
                timer = null;                    
            }, delay);                
        }            
    }        
}        
function handle() {            
    console.log(Math.random());        
}        
window.addEventListener('scroll', throttle(handle, 1000));
```

  + 鼠标不断点击触发，mousedown(单位时间内只触发一次)
  + 监听滚动事件，比如是否滑到底部自动加载更多，用throttle来判断

#### js垃圾回收机制
手动清除法：适用于闭包不用了的情况下
```js
function outer() {
        var a = 1;
      return function inner() {
            a = a + 1;
            console.log(a)
        }
    }
    // fn和inner函数连在一起，不能自动清理a
    var fn = outer();
   //  不用闭包了，跟inner断开联系
    fn = null ；
```
标记-清除法（浏览器默认，有内存储存不连续缺点）：说的简单点，不同浏览器都是固定时间清理一次，清理那些不再需要的变量，只不过不同浏览器的时间不同而已，都说了是标记-清除，首先要标记。【CG算法】（共轭梯度）（迭代）

标记：从windows全局对象开始往下找，找到了做个标记，做什么标记不重要（红宝书），没找到的也就是没标记的都要清除。也就是为什么把outer()给全局fn就不用清除a了呢，因为赋值给fn，windows下找到了fn,fn又找到了a所以清除不了。

清除：垃圾清理器清理就好

引用计数法（循环引用无法清除）：记录引用次数，0次就可以清除了，循环引用是致命的，所以浏览器都抛弃了引用计数法
```js
var obj1 = {
        fn: function(a) {
          console.log(a);
        }
      };
      var obj2 = {
        fn: function(b){
            console.log(b);  
        }
      };
      // obj1引用了obj2,obj2引用了obj1,引用计数法无法清除
      obj1.fn(obj2)
      obj2.fn(obj1)
```
#####  addEventListener和onClick()的区别
onclick添加的相同事件会被覆盖 ； 
addEventListener可以添加多个相同事件，先后执行；
```js
 <script type="text/javascript">     
            var addEvent = document.getElementById("addEvent");
                    addEvent.addEventListener("click",function(){
                        alert("我是addEvent1");//两条alert均执行
                    },false);
                    addEvent.addEventListener("click",function(){
                        alert("我是addEvent2");//
                    },false);

            var addEvent = document.getElementById("on_click"); 

            on_click.onclick = function() {
                alert("我是click1");
            }
            on_click.onclick = function() {
                alert("我是click2");//只alert我是click2
            }

    </script>
```
##### js中的arguments
arguments是什么？
在JavaScript的函数内部，参数用数组表示，arguments就是用来访问这个数组的的对象。
arguments与数组类似，但不是Array的实例。
arguments.length可以用来确定传递的参数个数。
arguments[i]也可以像数组一样访问每一个传递的参数，i从0开始，必须为正整数。
```js
!DOCTYPE html>
<html>
<head lang="en">
 <meta charset="UTF-8">
 <title>求一组数的平均数</title>
</head>
<body>
 <script>
  	 function getAvg()
	 {
 		//len 的值即为传递进来的参数个数
 		var sum = 0, len = arguments.length, i;
 		for(i = 0; i < len; i++)
 		{
 			sum += arguments[i];
 		}
 		return sum/len;

 	}
 	var avg = getAvg(5,6,7);
 	document.write("平均数："+avg);
 </script>
</body>
</html>
```
#### 微任务和宏任务
JavaScript的事件循环

由于JavaScript是单线程，所以很多任务只能一个一个排队，这对于用户体验是非常不友好的。因此聪明的程序员就把任务分为两类：

同步任务
异步任务
当我们打开一个网页的时候，网页的渲染和dom解析就是同步任务，而加载图片、音乐等大量下载任务的时候就是异步任务。
借用一张图片

![](F:\desktop\18220469-d0b039be193621d5.webp)

## Promise和process.nextTick(callback)

除了广义的同步任务和异步任务，我们对任务的定义有更精细的定义：

- macro-task（宏任务）：包括整体代码script，setTimeout，setInterval
- micro-task（微任务）：Promise，process.nextTick
  不同类型的任务会进入对应的Event Queue，比如setTimeout和setInterval会进入相同的Event Queue

事件循环的顺序，决定js的执行顺序。**进入整体代码（宏任务）后开始第一次循环。接着执行所有的微任务。然后再次从宏任务开始，找到其中一个任务队列执行完毕，再执行所有的微任务**。

在一个事件循环中，异步事件返回结果后会被放入一个任务队列中。根据这个异步事件的类型，这个事件实际上会到对应的宏任务队列或者微任务队列中去。并且在当前执行栈为空的时候，主线程会查看微任务队列中是否有事件存在。如果不存在，那么去宏任务队列中取出一个事件并把对应的回调函数加入到当前执行栈；如果存在，则会依次执行队列中事件对应的回调，直到微任务队列为空，然后去宏任务队列中取出最前面的事件，把对应的回调加入当前的执行栈。如此反复循环。

我们只需记住，**当当前执行栈执行完毕时会立刻先处理所有微任务队列中的事件，然后再去宏任务队列中取出一个事件。同一个事件循环中，微任务永远在宏任务之前执行**。

```jsx
console.log('1');

setTimeout(function() {
    console.log('2');
    process.nextTick(function() {
        console.log('3');
    })
    new Promise(function(resolve) {
        console.log('4');
        resolve();
    }).then(function() {
        console.log('5')
    })
})
process.nextTick(function() {
    console.log('6');
})
new Promise(function(resolve) {
    console.log('7');
    resolve();
}).then(function() {
    console.log('8')
})

setTimeout(function() {
    console.log('9');
    process.nextTick(function() {
        console.log('10');
    })
    new Promise(function(resolve) {
        console.log('11');
        resolve();
    }).then(function() {
        console.log('12')
    })
})
```

执行结果为1,7,6,8,2,4,3,5,9,11,10,12

# 常用BOM属性对象方法

### Window对象

### Navigator对象

### Screen对象

### History对象(back()和forward(),go())

### Location对象