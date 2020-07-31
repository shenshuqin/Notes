**1.浅拷贝**

拷贝就是把父对像的属性，全部拷贝给子对象。

浅拷贝就是数据双向改变。

JavaScript存储对象都是存地址的，所以浅拷贝会导致 obj1 和obj2 指向同一块内存地址。改变了其中一方的内容，都是在原来的内存上做修改会导致拷贝对象和源对象都发生改变，而深拷贝是开辟一块新的内存地址，将原对象的各个属性逐个复制进去。对拷贝对象和源对象各自的操作互不影响。

```js
  var obj={
        name:"dahuang",
        age:10
    }
    var newObj=obj;  //浅拷贝
    newObj.name="xiaohuang";
    console.log(obj.name); //xiaohuang
    console.log(newObj.name);//xiaohuang
    console.log(obj ===newObj)//true
```
但是当我们用Object.create这个函数创建一个对象时，obj对象就不会被篡改，话不多说，先上代码：
```js
    var obj2 = {
        name: "dahuang",
        age: 10
    }
    var newObj2 = Object.create(obj2);
    newObj2.name = "xiaohuang";
    console.log(obj2.name);//dahuag
    console.log(newObj2.name);//xiaohuang
    console.log(obj2 ===newObj2)//false
```

### [ES6拷贝方法](https://www.cnblogs.com/little-ab/p/6965181.html)
+ 方法一： Object.assign()
```js
// 对象浅拷贝， 复制所有可枚举属性
const obj1 = {a: 1};
const obj2 = {b: 2};
// copy obj1 and obj2 to a new obj;
Object.assign({}, obj1, obj2)

```
+ 方法二 ：Res参数
```js
//等同于方法一， 属于对象浅拷贝
const obj1 = {a: 1, b: 2};
// obj2 equal obj1
const obj2 = {...obj1};
```
### js实现浅拷贝和深拷贝

JS中的数据类型有两种，分别是基本数据类型和引用数据类型，前者存放在栈中，我们可以通过值的形式去访问；后者则是存放在堆和栈中，我们不能直接操作对象的堆内存空间，只能按照引用进行访问，即只能访问栈中的“地址”，这个地址指向存储在堆中的对象。

我们首先来实现个浅拷贝。
```js

var obj={
        name:'zs',
        age:18,
        friends:['Kate','Bob','Mike'],
        hobby:{
            hobby1:'codes',
            hobby2:'piano'
        }
    }
    function copy(obj1){
        var obj2={};
        for(var key in obj1){
            obj2[key]=obj1[key]
        }
        return obj2;
    }
    var result=copy(obj);   
    console.log(result.friends);//["Kate", "Bob", "Mike"]
```
以上代码可以将obj的值完全拷贝给obj2，看起来确实没有问题，但是一旦修改了原始对象或者拷贝对象的引用类型的属性值，另一个对象的属性值也会发生相应的改变，很明显这并不是我们想要的。那么为什么修改基本类型的属性值并没有产生影响呢？那就要从浅拷贝原理说起了，其实浅拷贝只是拷贝一层，对于基本数据类型来说，拷贝的是值，而深层次对象级别的则是拷贝引用，也就是平常说的地址。既然拷贝的对象与原始对象指向的地址相同，那么二者便会相互影响。


```js

obj.friends.push('Cherry');
console.log(result.friends);//["Kate", "Bob", "Mike", "Cherry"]
obj.name='Rose';
console.log(result.name);//zss
```

ES6中对象新增了一个assign方法，可以迅速实现浅拷贝，这里演示一下。当然这个方法也存在着引用拷贝互相影响的问题。

```js

var obj3=Object.assign(obj);
console.log(obj3.friends)//["Kate", "Bob", "Mike"]
```
在拷贝完成后，我们希望两个对象之间互不影响，也就是说改变任一对象的属性，另一个对象都不会发生改变。那么怎样实现深拷贝呢？这里介绍两种方法，一种是通过JSON来实现，另一种是运用递归来解决。

借用JSON的属性可以先用JSON.stringify把对象转成字符串，再用JSON.parse把字符串转成新的对象。当我们改变原始对象的属性值时，拷贝对象的属性并不会发生变化，但是这种方法存在一个弊端，我们无法拷贝原始对象的函数，因为函数是无法转成字符串的。于是，我们就可以考虑用递归的方法来解决问题。
```js
function deepCopy(obj1){
        var copyObj=JSON.parse(JSON.stringify(obj1));
        return copyObj;
    }
    var result=deepCopy(obj)
    console.log(result.friends);//["Kate", "Bob", "Mike"]
    obj.friends.push('Cherry');
    console.log(result.friends);// ["Kate", "Bob", "Mike"]
    console.log(obj.friends);//["Kate", "Bob", "Mike", "Cherry"]
```
终极办法：递归。因为我们无法确定原始对象里面的嵌套层数，因此可以借助递归的方法，再次调用拷贝函数，直到最深层数据为基本类型。

```js
var obj={
        name:'zs',
        age:18,
        friends:['Kate','Bob','Mike'],
        hobby:{
            hobby1:'codes',
            hobby2:'piano'
        }
    };
    function deepCopy(origin,target){
        //目标值先置为空
        var target=null;
        //判断原始对象的数据类型
        if(typeof origin==='object'&&origin!==null){
            //判断拷贝的是数组还是对象
            target=origin instanceof Array?[]:{};
            for(var key in origin){
                //递归拷贝
                target[key]=deepCopy(origin[key],target[key])
            }
        }else{
            //基本类型直接赋值
            target=origin;
        }
        return target;
    }
    var newObj={};
    var result=deepCopy(obj,newObj);
    console.log(result);
    obj.friends.push('Cherry');
    console.log(result.friends);// ["Kate", "Bob", "Mike"]
    console.log(obj.friends)// ["Kate", "Bob", "Mike", "Cherry"]

```