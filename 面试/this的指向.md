**this 是什么**
箭头函数的this只取决于箭头函数的第一个普通函数的this

在 ES5 中，其实 this 的指向，始终坚持一个原理：this 永远指向最后调用它的那个对象

## this 的四种绑定规则

this的4种绑定规则分别是：默认绑定、隐式绑定、显示绑定、new 绑定。优先级从低到高。

### 默认绑定

```js
function foo() { 
}       console.log( this.a );
var a = 2; 
foo(); //2
```

### 隐式绑定

除了直接对函数进行调用外，有些情况是，函数的调用是在某个对象上触发的，即调用位置上存在上下文对象。

```js

function foo() { 
console.log( this.a );
}
var a = 2;
var obj = { 
a: 3,
foo: foo 
};
obj.foo(); // 3
```

#### 多层调用链

```js
function foo() { 
    console.log( this.a );
}

var a = 2;

var obj1 = { 
    a: 4,
    foo: foo 
};

var obj2 = { 
    a: 3,
    obj1: obj1
};

obj2.obj1.foo(); //4
```

#### 隐式丢失（函数别名）

**注意：这里存在一个陷阱，大家在分析调用过程时，要特别小心**

```js
function foo() { 
    console.log( this.a );
}

var a = 2;

var obj = { 
    a: 3,
    foo: foo 
};

var bar = obj.foo;
bar(); //2
```

为什么会这样，obj.foo 赋值给bar，那调用bar()为什么没有触发隐式绑定，使用的是默认绑定呢。

这里有个概念要理解清楚，obj.foo 是引用属性，赋值给bar的实际上就是foo函数（即：bar指向foo本身）。

那么，实际的调用关系是：通过bar找到foo函数，进行调用。整个调用过程并没有obj的参数，所以是默认绑定，全局属性a。
**隐式丢失（回调函数）**
```js
function foo() { 
    console.log( this.a );
}

var a = 2;

var obj = { 
    a: 3,
    foo: foo 
};

setTimeout( obj.foo, 100 ); // 2
```
同样的道理，虽然参传是obj.foo，因为是引用关系，所以传参实际上传的就是foo对象本身的引用。对于setTimeout的调用，还是 setTimeout -> 获取参数中foo的引用参数 -> 执行 foo 函数，中间没有obj的参与。这里依旧进行的是默认绑定。
**显示绑定**
```js
function foo() { 
    console.log( this.a );
}

var a = 2;

var obj1 = { 
    a: 3,
};

var obj2 = { 
    a: 4,
};
foo.call( obj1 ); // 3
foo.call( obj2 ); // 4
```
**硬绑定**
```js
function foo() { 
    console.log( this.a );
}

var a = 2;

var obj1 = { 
    a: 3,
};

var obj2 = { 
    a: 4,
};

var bar = function(){
    foo.call( obj1 );
}

setTimeout( bar, 100 ); // 3
bar.call( obj2 ); // 3这里需要注意下，虽然bar被显示绑定到obj2上，对于bar，function(){…} 中的this确实被绑定到了obj2，而foo因为通过foo.call( obj1 )已经显示绑定了obj1，所以在foo函数内，this指向的是obj1，不会因为bar函数内指向obj2而改变自身。所以打印的是obj1.a（即3）。
```
**new 绑定**
```js
function foo(a) { 
    this.a = a;
}

var a = 2;

var bar1 = new foo(3);
console.log(bar1.a); // 3

var bar2 = new foo(4);
console.log(bar2.a); // 4

```
**扩展：箭头函数**
最后，介绍一下ES6中的箭头函数。通过“=>”而不是function创建的函数，叫做箭头函数。它的this绑定取决于外层（函数或全局）作用域。
case 1 (正常调用)
+ 普通函数
```js
function foo(){     
    console.log( this.a );
}

var a = 2;

var obj = { 
    a: 3,
    foo: foo 
};

obj.foo(); //3
```
+ 箭头函数
```js
var foo = () => {   
    console.log( this.a );
}

var a = 2;

var obj = { 
    a: 3,
    foo: foo 
};

obj.foo(); //2
foo.call(obj); //2 ，箭头函数中显示绑定不会生效

```
case 2 （函数回调）
+ 普通函数
```JS
function foo(){ 
    return function(){
        console.log( this.a );
    }   
}

var a = 2;

var obj = { 
    a: 3,
    foo: foo 
};

var bar = obj.foo();
bar(); //2
```
+ 箭头函数
```js
function foo(){ 
    return () => {
        console.log( this.a );
    }   
}



var a = 2;

var obj = { 
    a: 3,
    foo: foo 
};

var bar = obj.foo();
bar(); //3
```