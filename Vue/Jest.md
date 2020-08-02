### Jest

博客： https://jspang.com/detailed?id=63#toc217 

#### Jest环境搭建

1.创建文件夹

```shell
mkdir JestTest
```

2.生成package.json文件

```shell
npm init -y
```

3.安装

```shell
npm install jest@24.8.0 -D
```

4.编写dabao.js

```js
function baojian1(money){
    return money>=200? '至尊享受':'基本按摩'
}

function baojian2(money){
    return money>=1000? '双人服务':'单人服务'
}
module.exports = {
    baojian1,baojian2  
}
```

5.编写dabao.test.js

```js
const dabaojian = require('./dabaojian.js')
const { baojian1 , baojian2 }  = dabaojian

test('保健1 300元',()=>{
    expect(baojian1(300)).toBe('至尊享受')
})

test('保健2  2000元',()=>{
    expect(baojian2(2000)).toBe('双人服务')
})
```

6.测试

 要进行测试，我们可以打开`package.json`文件，然后把里边的`scripts`标签的值修改为`jest` 

```js
{
  "name": "jesttest",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "jest"
  },
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "jest": "^24.8.0"
  }
}
```

 然后VSCode的终端窗口中输入`yarn test` 或者`npm run test`，就可以进行测试了 

#### 测试覆盖率

1.初始化(npx执行命令)

```shell
npx jest --init
```

2.我们就可以使用下面的命令,`jest`就会自动给我们生成一个代码测试覆盖率的说明。 

```shell
npx jest --coverage
```

 当然这个不仅会有一个简单的终端图表，还会生成一个`coverage`的文件夹，这里边有很多文件。 

 我们可以打开`coverage-lcov-reporrt-index.html`文件，这时候就可以看到一个网页形式的，非常漂亮的测试覆盖率报告。 

3.解释npx

可以更改package.json

```js
{
  "name": "jesttest",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "jest",
    "jest":'jest --coverage'
  },
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "jest": "^24.8.0"
  }
}
```

运行:npm run jest

#### 自动测试

每次修改测试用例，我们都手动输入`yarn test` ，这显得很lower。可以通过配置`package.json`文件来设置。修改如下：

```js
{
  "name": "jesttest",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "jest --watchAll",
    "coverage":"jest --coverage"
  },
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "jest": "^24.8.0"
  }
}
```

 修改保存后，我们在终端再次运行`yarn test`,这时候测试一次后，它并没有结束，而是等待测试文件的变化，变化后就会自动进行测试。 

#### 匹配器

##### 1.toBe()

 `toBe()`匹配器，是在工作中最常用的一种匹配器，简单的理解它就是相等。这个相当是等同于`===`的，也就是我们常说的严格相等。 

```js
test('测试严格相等',()=>{
    const a = {number:'007'}   
    expect(a).toBe({number:'007'})
}) 
```

##### 2.toEqual()

 只要内容相等，就可以通过测试 

```js
test('测试严格相等',()=>{
    const a = {number:'007'}   
    expect(a).toEqual({number:'007'})
}) 
```

 所以说当你不严格匹配但要求值相等时时就可以使用`toEqual（）`匹配器。 

##### 3.其他

+  `toBeNul()`匹配器只匹配`null`值 
+  要匹配`undefined`时，就可以使用`toBeUndifined()`匹配器 
+  `toBeDefined()`匹配器的意思是只要定义过了，都可以匹配成功 
+ `toBeFalsy()` 就相当于判断真假的 
+ `toBeTruthy()` 就相当于判断真假的 

##### 4.toBeGreaterThan()----大于,toBeLessThan()----小小于,toBeGreaterThanOrEqual()----大于等于,toBeLessThanOrEqual()----小于等于

##### 5. `toBeCloseTo()`匹配器 

这个是可以自动消除`JavaScript`浮点精度错误的匹配器，举个例子，比如我们让`0.1`和`0.2`相加，这时候`js`得到的值应该是`0.30000000000004`,所以如果用`toEqual()`匹配器，测试用例会通过不了测试的。

比如我们把测试用例写成这样，就不会通过:

```js
test('toEqual匹配器',()=>{
    const one = 0.1
    const two = 0.2
    expect(one + two).toEqual(0.3)
})
```

 这时候我们就需要使用`toBeCloseTo()`匹配器，可以顺利通过测试,代码如下： 

```js

test('toBeCloseTo匹配器',()=>{
    const one = 0.1
    const two = 0.2
    expect(one + two).toBeCloseTo(0.3)
})
```

##### 6.toMatch()---字符串

```js
test('toMatch匹配器',()=>{
    const str="谢大脚、刘英、小红"
    expect(str).toMatch('谢大脚')
})
```

 因为确实有“谢大脚”,所以就通过测试了，当然你也可以写正则表达式。 

```js
test('toMatch匹配器',()=>{
    const str="谢大脚、刘英、小红"
    expect(str).toMatch(/谢大脚/)
})
```

##### 7.toContain() ----数组

```js
test('toContain匹配器',()=>{
    const arr=['谢大脚','刘英','小红']
    expect(arr).toContain('谢大脚')
})
```

##### 8.toThrow()

 专门对异常进行处理的匹配器，可以检测一个方法会不会抛出异常。比如我们下面一段代码。 

```js
const throwNewErrorFunc = ()=>{
    throw new Error('this is a new error')
}

test('toThrow匹配器',()=>{
    expect(throwNewErrorFunc).toThrow()
})
```

 我们也可以对这个匹配器中加一些字符串，意思就是抛出的异常必须和字符串相对应。 

```js
const throwNewErrorFunc = ()=>{
    throw new Error('this is a new error')
}

test('toThrow匹配器',()=>{
    expect(throwNewErrorFunc).toThrow('this is a new error')
})
```

##### 9.not

 `not`匹配器是`Jest`中比较特殊的匹配器，意思就是`相反`或者说`取反`.比如上面的例子，我们不希望方法抛出异常，就可以使用`not`匹配器。 

```js
const throwNewErrorFunc = ()=>{
    throw new Error('this is a new error')
}

test('toThrow匹配器',()=>{
    expect(throwNewErrorFunc).not.toThrow()
})
```

#### Jest支持ES6

目前我们的Jest是不支持`import...from....`这种形式，如果使用就会报错，因为Jest默认支持的是`CommonJS`规范，也就是`Node.js`中的语法，他只支持`require`这种引用。所以我们使用`import...from...`是ES6的语法，所以使用就会报错 

##### 修改dabao.js

```js
export function baojian1(money){
    return money>=200? '至尊享受':'基本按摩'
}

export function baojian2(money){
    return money>=1000? '双人服务':'单人服务'
}
```

 然后再打开`dabao.test.js`文件，修改为下面的代码。 

```js
import {baojian1,baojian2} from './math.js'

test('保健1 300元',()=>{
    expect(baojian1(300)).toBe('至尊享受')
})

test('保健2  2000元',()=>{
    expect(baojian2(2000)).toBe('双人服务')
})
```

 这时候你用`yarn test`来测试，你会发现是没办法使用的，会报很多错误。这是因为我们需要用Babel进行转换，没有Babel转换是肯定会报错的。 

##### Babel转换器

1.安装

```shell
npm install @babel/core@7.4.5 @babel/preset-env@7.4.5 -D
```

 耐心等待`babel`安装完成，然后打开`package.json`文件，可以看到文件里有这样两行代码。 

```js
 "devDependencies": {
    "@babel/core": "^7.4.5",
    "@babel/preset-env": "^7.4.5",
    "jest": "^24.8.0"
  },
```

2.配置

 我们在项目根目录下新建一个`.babelrc`的文件

```js
{
    "presets":[
        [
                "@babel/preset-env",{
                "targets":{
                    "node":"current"
                }
            }
        ]
    ]
}
```

3.运行

```shell
npm run test
```

#### 测试异步方法

1.新建文件

```js
import axios from 'axios'

export const fetchData = (fn)=>{
    axios.get('http://a.jspang.com/jestTest.json').then((response)=>{
        fn(response.data)
    })
}
```

2.测试文件

```js
import { fetchData } from './fetchData.js'

test('fetchData 测试',()=>{
   fetchData((data)=>{
       expect(data).toEqual({
           success: true
       })

   })
  })
```

注意这样写是由问题的，因为方法还没有等到回调，我们的结果已经完成了，所以这时候你对于没测试完，只是方法可用，就返回了测试结果，这种结果是不保证正确的。

比如现在我们把请求的地址后边加一个`1`,这时候才测试，依然是正确的。

```js
axios.get('http://a.jspang.com/jestTest1.json').then((response)=>{
        fn(response.data)
    })
```

所以我们必须加入一个done方法，保证我们的回调已经完成了，这时候我们表示测试完成。 

```js
import { fetchData } from './fetchData.js'

test('fetchData 测试',(done)=>{
   fetchData((data)=>{
       expect(data).toEqual({
           success: true
       })
       done()
   })
  })
```

#### 异步代码测试方法----直接返回promise

1.编写异步代码

```js
export const fetchTwoData = ()=>{
    return axios.get('http://a.jspang.com/jestTest.json')
}
```

2.测试代码

```js
import { fetchData , fetchTwoData } from './fetchData.js'
test('FetchTwoData 测试', ()=>{
       return  fetchTwoData().then((response)=>{
            expect(response.data).toEqual({
                success: true
            })
        })
  })
```

#### 异步测试方法---不存在接口

在工作中有时候会测试异步接口不存在的需求（虽然不多，但这里有坑），比如有些后台需求不允许前台访问时，这时候就会返回404（页面不存在），这时候在测试时也存在一些坑，所以单独拿出来给大家讲一下。 

1.异步代码

```js
export const fetchThreeData = ()=>{
    return axios.get('http://a.jspang.com/jestTest_error.json')
}
```

2.测试代码

```js
test('FetchThreeData 测试', ()=>{
      expect.assertions(1)  // 断言，必须执行一次expect
      return fetchThreeData().catch((e)=>{
        expect(e.toString().indexOf('404')> -1).toBe(true)

      })
  })
```

#### 异步测试方法----async-await

1.异步代码

```js
 export const fetchFourData = ()=>{
    return axios.get('http://a.jspang.com/jestTest.json')
}
```

2.测试代码

```js
test('FetchFourData 测试', async()=>{
        const response  = await fetchFourData()
        expect(response.data).toEqual({
            success : true
        })
})
```

