### Proxy

 Proxy 用于修改某些操作的默认行为，等同于在语言层面做出修改，所以属于一种“元编程”（meta programming），即对编程语言进行编程。 

 Proxy 可以理解成，在目标对象之前架设一层“拦截”，外界对该对象的访问，都必须先通过这层拦截，因此提供了一种机制，可以对外界的访问进行过滤和改写。 

#### get()

`get`方法用于拦截某个属性的读取操作，可以接受三个参数，依次为目标对象、属性名和 proxy 实例本身（严格地说，是操作行为所针对的对象），其中最后一个参数可选。

`get`方法的用法，上文已经有一个例子，下面是另一个拦截读取操作的例子。

```javascript
var person = {
  name: "张三"
};

var proxy = new Proxy(person, {
  get: function(target, propKey) {
    if (propKey in target) {
      return target[propKey];
    } else {
      throw new ReferenceError("Prop name \"" + propKey + "\" does not exist.");
    }
  }
});

proxy.name // "张三"
proxy.age // 抛出一个错误
```

#### set()

```js
set方法用来拦截某个属性的赋值操作，可以接受四个参数，依次为目标对象、属性名、属性值和 Proxy 实例本身，其中最后一个参数可选。

假定Person对象有一个age属性，该属性应该是一个不大于 200 的整数，那么可以使用Proxy保证age的属性值符合要求。

let validator = {
  set: function(obj, prop, value) {
    if (prop === 'age') {
      if (!Number.isInteger(value)) {
        throw new TypeError('The age is not an integer');
      }
      if (value > 200) {
        throw new RangeError('The age seems invalid');
      }
    }

    // 对于满足条件的 age 属性以及其他属性，直接保存
    obj[prop] = value;
  }
};

let person = new Proxy({}, validator);

person.age = 100;

person.age // 100
person.age = 'young' // 报错
person.age = 300 // 报错
```

#### apply()

`apply`方法拦截函数的调用、`call`和`apply`操作。

`apply`方法可以接受三个参数，分别是目标对象、目标对象的上下文对象（`this`）和目标对象的参数数组。

```javascript
var handler = {
  apply (target, ctx, args) {
    return Reflect.apply(...arguments);
  }
};
```

下面是一个例子。

```javascript
var target = function () { return 'I am the target'; };
var handler = {
  apply: function () {
    return 'I am the proxy';
  }
};

var p = new Proxy(target, handler);

p()
// "I am the proxy"
```

#### has()

`has`方法用来拦截`HasProperty`操作，即判断对象是否具有某个属性时，这个方法会生效。典型的操作就是`in`运算符。

`has`方法可以接受两个参数，分别是目标对象、需查询的属性名。

下面的例子使用`has`方法隐藏某些属性，不被`in`运算符发现。

```javascript
var handler = {
  has (target, key) {
    if (key[0] === '_') {
      return false;
    }
    return key in target;
  }
};
var target = { _prop: 'foo', prop: 'foo' };
var proxy = new Proxy(target, handler);
'_prop' in proxy // false
```

#### construct()

```javascript
var p = new Proxy(function () {}, {
  construct: function(target, args) {
    console.log('called: ' + args.join(', '));
    return { value: args[0] * 10 };
  }
});

(new p(1)).value
// "called: 1"
// 10
```



#### Proxy的用法以及在实际中的应用

1.拦截对象属性的读取

```jsx
{
  // Proxy 代理
  let obj = {    //原始对象
    time：'2017-03-11',
    name:'net',
    _r:123
  };
  let monitor = new Proxy(obj,{
    //拦截对象属性的读取
    get(target,key) {
      return target[key].replace('2017','2018')
    }
  })
  console.log(monitor.time); // 2018-03-11
  console.log(obj.time);// 2017-03-11
}
```

2.拦截对象设置属性

```jsx
{
  // Proxy 代理
  let obj = {    //原始对象
    time：'2017-03-11',
    name:'net',
    _r:123
  };
  let monitor = new Proxy(obj,{
    //拦截对象设置属性
    set(target,key,value) {
      if(key==='name') {
        return target[key] = value
      } else {
        return target[key]
      }
    }
  })
  monitor.name = 'xiaowang'
  console.log(monitor.name);// xiaowang
  console.log(obj.name);// xiaowang
  monitor._r = 5555; 
  console.log(monitor._r);//123，没有设置成功
}
```

3.拦截key in object操作

```js
{
  // Proxy 代理
  let obj = {    //原始对象
    time：'2017-03-11',
    name:'net',
    _r:123
  };
  let monitor = new Proxy(obj,{
  	has(target,key){
        if(key === 'name'){
            return target[key]
        }else{
            return false
        }
    }
  })
 console.log("time" in monitor) //false
}
```

4删除拦截

```js
    var handler = {
        deleteProperty (target, key) {
            invariant(key, 'delete');
            delete target[key];
            return true;
        }
    };
    function invariant (key, action) {
        if (key[0] === '_') {
            console.log("删除",key)
            // throw new Error(`Invalid attempt to ${action} private "${key}" property`);
        }
    }

    var target = { _prop: 'foo' };
    var proxy = new Proxy(target, handler);
    delete proxy._prop
    console.log(target)
    // Error: Invalid attempt to delete private "_prop" property
```

