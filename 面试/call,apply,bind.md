### call,apply,bind的使用

####  1、什么是this
// 作为对象方法调用
```js
var test = {
    a : 5,
    b : 6,
    sum : function () {
        return this.a + this.b;     // 此处this = test
    }
}
alert(test.sum());     // 11
```
// 作为函数调用
```js
a = 4;
b = 3;
function sum(){
    return this.a + this.b;         // 此处this = window
}
alert(sum());      // 7
```
实例:
```js
  var test = {
        a : 5,
        b : 6,
        sum : function (a,b) {
            console.log("function sum" + this);//test
            function getA(a) {
                console.log("function getA" + this);//window
                this.a = a;         // 在window上增加了一个全局变量a
                return this.a;     // 此处this = window
            }
            function getB(b){
                console.log("function getB" + this);//window
                this.b = b;         //在window上增加了一个全局变量b
                return this.b;     // 此处this = window
            }
            var c = this.a + this.b;//test
            var d = getA(a) + getB(b);
            //test对象中定义的一级函数作用域中的this指向test对象本身
            //而其二级函数中的this指向window
            return [c, d];
        }
    }
    console.log("function main" + this);//window
    console.log(test.sum(4,3));  // 11 7
```
对比:
```js
var test = {
    a : 5,
    b : 6,
    sum : function () {
        var self = this;    // 此处this = test的实例
        function getA() {
            return self.a;
        }
        function getB(){
            return self.b;
        }
        return getA() + getB();
    }
}
alert(test.sum());//11
alert(a);     // 此处报错：a is not defined
alert(b);    // 此处报错：a is not defined
```
#### 2、使用call、apply和bind改变函数执行时的上下文（this）
```js
    var test = {
            a : 5,
            b : 6,
            sum : function (a,b) {
                var self = this;
                function getA() {
                    return self.a;
                }
                function getB(){
                    return self.b;
                }
                console.log(a);
                console.log(b);
                // console.log(a)
                return getA() + getB();
            }
        }
        var obj = {a:2,b:3};
        console.log(test.sum.call(obj,4,5));      // 调用时self = this = obj，alert顺序4,5,5
        console.log(test.sum.apply(obj,[6,7]));   // 调用时self = this = obj，alert顺序6,7,5
        var sum = test.sum.bind(obj,8);     // 此处返回一个只有一个参数的函数sum（b）
        console.log(sum(9));   
```
### 介绍基本用法
一:
```js
var name ='小王',age=18;
var obj = {
    name:'小张',
    objAge:this.age,
    myFun:function(){
        console.log(this.name+'年龄'+this.age);
    }
}
var db={
name:"ssq",
age:30
}
obj.myFun.call(db)；　　　　// ssq 30
obj.myFun.apply(db);　　　 //  ssq 30
obj.myFun.bind(db)();　　　//  ssq 30
```

**了解this 的指向**

在 ES5 中，其实 this 的指向，始终坚持一个原理：this 永远指向最后调用它的那个对象

例 1：

```js
var name = "windowsName";
function a() {
 var name = "Cherry";
 console.log(this.name);   // windowsName
 console.log("inner:" + this); // inner: Window
}
a();
console.log("outer:" + this)   // outer: Window
```

这个相信大家都知道为什么 log 的是 windowsName，因为根据刚刚的那句话“this 永远指向最后调用它的那个对象”，我们看最后调用 a 的地方 a();，前面没有调用的对象那么就是全局对象 window，这就相当于是 window.a()；注意，这里我们没有使用严格模式，如果使用严格模式的话，全局对象就是 undefined，那么就会报错 Uncaught TypeError: Cannot read property 'name' of undefined。

例 2：

```js
var name = "windowsName";
 var a = {
  name: "Cherry",
  fn : function () {
   console.log(this.name);  // Cherry
  }
 }
 a.fn();
```
在这个例子中，函数 fn 是对象 a 调用的，所以打印的值就是 a 中的 name 的值。
我们做一个小小的改动：

例 3：
```js
var name = "windowsName";
 var a = {
  name: "Cherry",
  fn : function () {
   console.log(this.name);  // Cherry
  }
 }
 window.a.fn();
```
这里打印 Cherry 的原因也是因为刚刚那句话“this 永远指向最后调用它的那个对象”，最后调用它的对象仍然是对象 a。
我们再来看一下这个例子：
```js
var name = "windowsName";
var a = {
 // name: "Cherry",
 fn : function () {
  console.log(this.name);  // undefined
 }
}
window.a.fn();
```
例4:
```js
var name = "windowsName";
var a = {
 name : null,
 // name: "Cherry",
 fn : function () {
  console.log(this.name);  // windowsName
 }
}
var f = a.fn;
f();
```
这是因为虽然将 a 对象的 fn 方法赋值给变量 f 了，但是没有调用，再接着跟我念这一句话：“this 永远指向最后调用它的那个对象”，由于刚刚的 f 并没有调用，所以 fn() 最后仍然是被 window 调用的。所以 this 指向的也就是 window。
例 5：
```js
var name = "windowsName";
function fn() {
 var name = 'Cherry';
 innerFunction();
 function innerFunction() {
  console.log(this.name);  // windowsName
 }
}
fn()
```
#### 怎么改变 this 的指向
改变 this 的指向我总结有以下几种方法：
+ 使用 ES6 的箭头函数
+ 在函数内部使用 _this = this
+ 使用 apply、call、bind
+ new 实例化一个对象
例 7：
```js
var name = "windowsName";
 var a = {
  name : "Cherry",
  func1: function () {
   console.log(this.name)  
  },
  func2: function () {
   setTimeout( function () {
    this.func1()
   },100);
  }
 };
 a.func2()  // this.func1 is not a function
```
在不使用箭头函数的情况下，是会报错的，因为最后调用 setTimeout 的对象是 window，但是在 window 中并没有 func1 函数。
我们在改变 this 指向这一节将把这个例子作为 demo 进行改造。
#### 箭头函数
```js
var name = "windowsName";
var a = {
 name : "Cherry",
 func1: function () {
  console.log(this.name)  
 },
 func2: function () {
  setTimeout( () => {
   this.func1()
  },100);
 }
};
a.func2()  // Cherry
```
#### 在函数内部使用 _this = this
```js
var name = "windowsName";
var a = {
 name : "Cherry",
 func1: function () {
  console.log(this.name)  
 },
 func2: function () {
  var _this = this;
  setTimeout( function() {
   _this.func1()
  },100);
 }
};
a.func2()  // Cherry
```
这个例子中，在 func2 中，首先设置 var _this = this;，这里的 this 是调用 func2 的对象 a，为了防止在 func2 中的 setTimeout 被 window 调用而导致的在 setTimeout 中的 this 为 window。我们将 this(指向变量 a) 赋值给一个变量 _this，这样，在 func2 中我们使用 _this 就是指向对象 a 了。
#### 使用 apply、call、bind
apply:
```js
var a = {
 name : "Cherry",
 func1: function () {
  console.log(this.name)
 },
 func2: function () {
  setTimeout( function () {
   this.func1()
  }.apply(a),100);
 }
};
a.func2()   // Cherry
```
call:
```js
var a = {
 name : "Cherry",
 func1: function () {
  console.log(this.name)
 },
 func2: function () {
  setTimeout( function () {
   this.func1()
  }.call(a),100);
 }
};
a.func2()   // Cherry
```
bind:
```js
var a = {
  name : "Cherry",
  func1: function () {
   console.log(this.name)
  },
  func2: function () {
   setTimeout( function () {
    this.func1()
   }.bind(a)(),100);
  }
 };
 a.func2()   // Cherry
```
#### bind 和 apply、call 区别

他们的区别只是传入的参数不同。bind 是创建一个新的函数

```js
var a ={
  name : "Cherry",
  fn : function (a,b) {
   console.log( a + b)
  }
 }
 var b = a.fn;
 b.apply(a,[1,2])  // 3
 =================
 var a ={
  name : "Cherry",
  fn : function (a,b) {
   console.log( a + b)
  }
 }
 var b = a.fn;
 b.call(a,1,2)  // 3
 =================
 var a ={
  name : "Cherry",
  fn : function (a,b) {
   console.log( a + b)
  }
 }
 var b = a.fn;
 b.bind(a,1,2)()   // 3
```