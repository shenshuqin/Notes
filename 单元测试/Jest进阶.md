# Jest进阶

## 钩子函数概念：在代码执行的某个时刻，会自动运行的一个函数。 

 在jest中，如果测试用例中需要使用到某个对象 或 在执行测试代码的某个时刻需要做一些必要的处理，直接在测试文件中写基础代码是不推荐的，可以使用jest的钩子函数。 

  新建counter.js文件，代码如下： 

```js
export default class Counter{
    constructor(){
        this.number = 0 ;
    }
    addOne(){
        this.number += 1;
    }
    minusOne(){
        this.number -= 1;
    }
}
```

编写对应的测试用例：counter.test.js文件，代码如下：

```js
import  Counter  from './counter.js'
const counter = new Counter();
test('测试counter中addOne方法',()=>{
    counter.addOne();
    expect(counter.number).toBe(1)
})
test('测试counter中minusOne方法',()=>{
    counter.minusOne();
    expect(counter.number).toBe(0)
})
```

上边的测试用例可以正常执行，但是addOne()函数调用的次数会影响counter.number的值，就会影响到minusOne方法执行结果。如果你想addOne与minusOne方法调用互不影响时，此时就不得不引入jest的钩子函数。 

#### beforeAll  **在所有测试用例执行之前执行一次** 

```js
import  Counter  from './counter.js'
let counter =null;
beforeAll(()=>{
    console.log('beforeAll')
    counter = new Counter();
})
test('测试counter中addOne方法',()=>{
    counter.addOne();
    expect(counter.number).toBe(1)
})
test('测试counter中minusOne方法',()=>{
    counter.minusOne();
    expect(counter.number).toBe(0)
})
```

 此时还是会相互影响，依旧不能达成目标。 

####  **beforeEach：在每个测试用例执行之前执行一次** 

```js
import  Counter  from './counter.js'
let counter =null;
beforeAll(()=>{
    console.log('beforeAll')
})
beforeEach(()=>{
    console.log('beforeEach')
    counter = new Counter();
    console.log('counter.number',counter.number)
})
test('测试counter中addOne方法',()=>{
    counter.addOne();
    expect(counter.number).toBe(1)
})
test('测试counter中minusOne方法',()=>{
    counter.minusOne();
    expect(counter.number).toBe(0)
})
```

此时测试失败, 返回的结果已经不是0了 ，而是-1！此时addOne 与minusOne两个测试用例已经不会相互影响了。每次执行测试用例前，counter都会初始化！ 

####  ***afterEach：在每个测试用例执行完成之后执行一次\**** 

```js
import  Counter  from './counter.js'
let counter =null;
describe("测试用例",()=>{
beforeAll(()=>{
    console.log('beforeAll')
    counter = new Counter();
    console.log('counter.number',counter.number)
})
afterAll(()=>{
    console.log('afterAll')
})
beforeEach(()=>{
    console.log('beforeEach')
    counter = new Counter();
})
afterEach(()=>{
    console.log('afterEach')
})
describe("增加",()=>{
  test('测试counter中addOne方法',()=>{
    counter.addOne();
    expect(counter.number).toBe(1)
 })
})
describe("减少",()=>{
   test('测试counter中minusOne方法',()=>{
    counter.minusOne();
    expect(counter.number).toBe(-1)
 })
})
})
```

钩子函数执行顺序：

根据上边案例实际打印结果可以看出这四个钩子函数的执行顺序，如下：

(1)beforeAll > (2)beforeEach > (3)afterEach > (4)afterAll

靠前的一次优先执行。describe里面test外面的代码会最先执行

## Mock

举例://改变了原文件

新建文件

```js
expect const runCallBack = (callback) => {
    return callback() //改变了原文件
}
```

测试文件

```js
import {runCallback} from './demo'

test("测试runCallback",()=>{
  const func = ()=>{
      return 'hello'
  }
  expect(runCallBack(func)).toBe('hello')
})

```

#### 捕获函数的调用和设置函数返回

测试文件

```js
import { runCallback, createObject } from './demo';

test('测试runCallback',()=>{
    const func = jest.fn();  //mock函数，捕获函数的调用
    //.mockReturnValueOnce 是指调用一次返回的值
    // func.mockReturnValueOnce('Dell');
    // func.mockReturnValueOnce('Lee');
    // func.mockReturnValueOnce('Hello');
    
    //.mockReturnValueOnce/链式调用   
    //func.mockReturnValueOnce('Dell').mockReturnValueOnce('Lee').mockReturnValueOnce('Hello');
   
    //.mockReturnValue 指返回的所有的值
    func.mockReturnValue('Dell');
    
    //调用mock函数
    runCallback(func);
    runCallback(func);
	
	// 测试用例
    // expect(func).toBeCalled();
    // expect(runCallback(func)).toBe('hello');
    // expect(func.mock.calls.length).toBe(2);
    expect(func.mock.calls[0]).toEqual(['abc']);
    expect(func.mock.results[0].value).toBe('Dell');
    console.log(func.mock);
});

test('测试 createObject', ()=>{
    const func = jest.fn();
    createObject(func);
    console.log(func.mock);
});

```

func.mock:

 calls------ 函数被调用返回的结果
instances----- 函数指向的this原型
invocationCallOrder ------ 函数执行的顺序
results----- 函数执行的结果，为数组类型，有返回的类型和返回的值 

#### 改变函数的内部实现

新建文件demo.js

```js
import axios from 'axios'
export const getData = ()=>{
    return axios.get('/api').then(res=>res.data)
}
export const getNumber=()=>{
    return 123
}
```

测试代码

```js
import axios from 'axios';
jest.mock('axios'); //mock模拟整个axios
test.only('测试 getData', async ()=>{
    //单次请求返回
    axios.get.mockResolvedValueOnce({data: 'hello'})
    //多次请求返回
    axios.get.mockResolvedValueOnce({data: 'world'})
    //不希望发真正的ajax请求
    await getData().then((data) => {
        expect(data).toBe('hello');
    })
    await getData().then((data) => {
        expect(data).toBe('world');
    })
});

```



### Mock用同步模拟异步请求

#### 同级目录下创建 ___mocks___/demo.js

```js
export const fetchData = () =>{
    return new Promise((resove,reject)=>{
        resolve({
            "function(){return '123'}"
        })
    })
}
```

测试文件:

```js
jest.mock('./demo')
import {fetchData} from './demo'

const {getNumber} = jest.requireActual('./demo')
test("测试",()=>{
    return fetchData().then(data=>{
        expect(eval(data)).toEqual('123')
    })
})

test("测试",()=>{
	expect(getNumber()).toEqual(123)
})
```

### Mock Timers

 平时开发中我们经常用到定时器setInterval 或者setTimeout ，现在我们就写一个定时器的测试用例代码如下： 

```
export  const lazy = (fn)=> {
  setTimeout(() => {
    fn();
  }, 3000);
}
```

  demo.test.js

```js
import {lazy} from './timerDemo'
test('should call fn after 3s', () => {
 const callback = jest.fn();
 lazy(callback);
 setTimeout(() => {
  expect(callback).toBeCalled();
 }, 3001);
})
```

 使用npm run test运行测试用例，运行结果如下： 不能通过

#### 方法一

done()

```js
import {lazy} from './timerDemo'
test('should call fn after 3s', (done) => {
    const callback = jest.fn();
    lazy(callback);
    setTimeout(() => {
        expect(callback).toBeCalled();
        done();
    }, 3001);
})
```

#### 方法二

使用mock timer解决这个问题。jest 提供了mock timer 的功能，不要再使用真实的时间在这里等了，一个假的时间模拟一下就可以了。首先是jest.useFakeTimers() 的调用，它就告诉jest 在以后的测试中，可以使用假时间。当然只用它还不行，因为它只是表示可以使用，我们还要告诉jest在哪个地方使用，当jest 在测试的时候，到这个地方，它就自动使用假时间。两个函数，jest.runAllTimers(), 它表示把所有时间都跑完。具体到我们这个测试，我们希望执完lazy(callback) 就调用， 把lazy函数中的3s时间立刻跑完。可以使用jest.runAllTimers(); 

 具体代码如下： 

```js
import {lazy} from './timerDemo'
jest.useFakeTimers();//可以使用假函数
test('should call fn after 3s', () => {
    const callback = jest.fn();
    lazy(callback);
    jest.runAllTimers();//让所有定时器立即执行
    expect(callback).toBeCalled();<br>　　　　//expect(callback).toHaveBeenCalledTimes(1) 也可以使用
```

 如果有多个定时器的时候，我们修改demo.js的代码如下： 

```js
export const lazy = (fn)=> {
    setTimeout(() => {
        fn();
        console.log('第一个定时器执行')
        setTimeout(()=>{
            console.log('第二个定时器执行')
        },3000)
    }, 3000);
}
```

 此时运行测试用例的时候，两个定时器会立马都被执行掉。但如果我们只想运行最外层的那个定时器时，我们需要引入　runOnlyPendingTimers() ，只执行一个定时操作。具体代码如下：　 

```js
//demo.js
 
export const lazy = (fn)=> {
    setTimeout(() => {
        fn();
        console.log('第一个定时器执行')
        setTimeout(()=>{
            console.log('第二个定时器执行')
        },3000)
    }, 3000);
}
 
//demo.test.js
 
import {lazy} from './timerDemo'
jest.useFakeTimers();//可以使用假函数
test('should call fn after 3s', () => {
    const callback = jest.fn();
    lazy(callback);
    jest.runOnlyPendingTimers();//让所有定时器立即执行
    //expect(callback).toBeCalled();
    expect(callback).toHaveBeenCalledTimes(1)
})
```

自己试着运行一下我们会发现，只有第一个执行了。

同时也可以使用 jest.advanceTimer() 快进几秒。

具体代码使用如下：

```js
//demo.js
export const lazy = (fn)=> {
    setTimeout(() => {
        fn();
        console.log('第一个定时器执行')
        setTimeout(()=>{
            console.log('第二个定时器执行')
        },3000)
    }, 3000);
}
 
//demo.test.js
 
import {lazy} from './timerDemo'
jest.useFakeTimers();//可以使用假函数
test('should call fn after 3s', () => {
    const callback = jest.fn();
    lazy(callback);
    jest.advanceTimersByTime(3000)
    expect(callback).toHaveBeenCalledTimes(1)
})
```

 运行结果发现只执行了第一个定时器，如果还想执行第二个，我们可以修改demo.test.js代码如下： 

```js
import {lazy} from './timerDemo'
jest.useFakeTimers();//可以使用假函数
test('should call fn after 3s', () => {
    const callback = jest.fn();
    lazy(callback);
    jest.advanceTimersByTime(3000)
    jest.advanceTimersByTime(3000)//第二个的时间以第一个的时间为基数
    //expect(callback).toBeCalled();
    expect(callback).toHaveBeenCalledTimes(1)
})
```

 此时两个定时器都会执行。　　使用jest.advanceTimersByTime(n)快进n时间执行定时，多个advanceTimerByTime连用时，后一个会以前一个的时间为基点，如果不想互相影响，我们可以使用钩子函数beforeEach解决这个问题。 

## snapshot快照

 项目中经常有一些配置文件。比如 

```js
export const generateConfig  = () => {
    return {
        server: 'http://localhost',
        port: '8080'
    }
}
```

 那测试它的测试用例可以这样写 

```js
import { generateConfig } from './snapshot.js'
 
test('测试 generateConfig', () => {
    expect(generateConfig()).toEqual({
        server: 'http://localhost',
        port: '8080'
    })
})
```

当配置项不断增加的时候，就需要不断去更改测试用例。

那么我们可以这样写测试用例：

```js
import { generateConfig } from './snapshot.js'
 
test('测试 generateConfig', () => {
    expect(generateConfig()).toMatchSnapshot()
})
```

toMatchSnapshot() 会为expect 的结果做一个快照并与前面的快照做匹配。（如果前面没有快照那就保存当前生成的快照即可）

这在配置文件的测试的时候是很有用的，因为配置文件，一般不需要变化。

当然，确实要改配置文件，然后要更新快照，也可。

如下

按‘u’ 可以更新快照（先按w，当有多个快照报错时，可以选i 一个个更新快照）。

有一种情况，假设配置项中有随机数或者当前时间，如下。

```js
export const generateConfig  = () => {
    return {
        server: 'http://localhost',
        port: '8080',
        time: new Date()
    }
}
```

 那case 中snapshot 就需要使用expect.any() 了，如下 

```js
import { generateConfig } from './snapshot.js'
 
test('测试 generateConfig', () => {
    expect(generateConfig()).toMatchSnapshot({
        time: expect.any(Date)
    })
})
```

之前使用snapshot 的时候，都会生成一个snapshot 的文件。

下面我们来使用行内snapshot。要使用行内snapshot ，我们需要在项目中安装 prettier

```shell
npm install prettier@1.18.2
```

 然后，我们改一下测试用例 

```js
import { generateConfig } from "./snapshot.js";
 
test("测试 generateConfig", () => {
  expect(generateConfig()).toMatchInlineSnapshot(
    {
      time: expect.any(Date)
    }
  );
});
```

 运行测试用例，会发现测试用例代码变成如下了。快照被保存到了测试代码中  

```js
import { generateConfig } from "./snapshot.js";
 
test("测试 generateConfig", () => {
  expect(generateConfig()).toMatchInlineSnapshot(
    {
      time: expect.any(Date)
    },
    `
    Object {
      "port": "8080",
      "server": "http://localhost",
      "time": Any<Date>,
    }
  `
  );
});
```

### 对Dom节点的测试

demo.js

```js
import $ from 'jquery'
const addDivToBody =() =>{
    $('body').append('<div/>')
}
eport addDivToBody
```

测试

```js
import addDivToBody from './demo'
import $ from 'jquery'
 test('测试',()=>{
     addDivToBody();
     expect($('body').find('div').length).toBe(1)
 })
```

