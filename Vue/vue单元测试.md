## Vue单元测试

采用Vue官方工具(vue-cli)搭建出来的项目,在这个搭建工具中推荐两种测试:

+ 端到端的测试(E2E)----------UI测试
+ 单元测试(Unit Test)-----------测试一个模块,组件,函数

单元测试主要用到的两个工具分别是Karma和Mocha

### 测试工具

Karma是一个基于Node.js的js测试执行过程管理工具,改工具在Vue中主要作用是将项目运行在各种主流Web浏览器进行测试.

#### Mocha

它是一个测试框架,在vue-cli配合Mocha本身不带断言库,所以必须先引入断言库,Chai断言库实现单元测试.Chai(http://chaijs.com/);

```js
var expect = require('chai').expect;
expect(1+1).to.be.equal(2);
```

### vue 中使用测试的步骤

1.在Node环境中安装Vue-cli

```shell
npm install -g vue-cli
```

2.通过vue-cli初始化项目文件,命令如下

```shell
vue init webpack testtodo
```

3.可以运行初始化的测试

```shell
npm run unit
```

vue 脚手架已经初始化了一个helloword.spec.js的测试文件去测试helloword.vue

```js
import Vue from 'vue'
//引入需要测试的组件
import HelloWorld from '@/components/HelloWorld'
//创建测试套件 一个测试组价写一个测试套件
describe('HelloWorld.vue', () => {
  //测试用例,用来测试不同的方法或者显示的内容
  it('should render correct contents', () => {
    //通过vue来渲染helloword
    const Constructor = Vue.extend(HelloWorld)
    const vm = new Constructor().$mount()
    //判断页面中是否有渲染出来的内容
    expect(vm.$el.querySelector('.hello h1').textContent)
      .to.equal('Welcome to Your Vue.js App')
  })
})

```

### 案列

需要下载一个工具(官方提供方便操作)

```shell
npm install --save-dev @vue/test-utils@1.0.0-beta.12
```

其他与上一致

如果需要测试某个组件就在test/unit/specs下新建一个todo.spec.js

比如:

```js
//引入vue的测试工具
import {mount} from '@vue/test-utils'
//引入需要测试的组件
import Todo from '@/components/todo'
//创建测试套件 一个测试组价写一个测试套件
describe('todo组件测试套件', () => {
  //测试用例,用来测试不同的方法或者显示的内容
  it('测试查看功能', () => {
      //通过mount将组件渲染出来
    const wrapper = mount(Todo)
    //寻找指定的dom元素
    console.log(wrapper.find('.todo-list'));
      //通过vm寻找data的数据
      console.log(wrapper.vm.todos)
    //断言
    expect(wrapper.vm.todos.length)
      .to.be.equal(2)
  })
    //测试增加功能
      it('测试增加功能', () => {
      //通过mount将组件渲染出来
    	const wrapper = mount(Todo);
          wrapper.setdata({
              newtodo:'test'
          })
          wrapper.find('new-todo').trigger('keyup.enter')
    //断言
    expect(wrapper.vm.todos[2].text).to.be.equal('test')
  })
      //测试删除功能
      it('测试删除功能', () => {
      //通过mount将组件渲染出来
    	const wrapper = mount(Todo);
          //找到删除按钮
        const btn = wrapper.find('.destroy')
        btn.trigger('click')
    //断言
    expect(wrapper.vm.todos.length).to.be.equal(1)
  })
       //测试修改功能
      it('测试修改功能', () => {
      //通过mount将组件渲染出来
    	const wrapper = mount(Todo);
		const li = wrapper.find('li');
        const label = li.find('label');
        label.trigger('dbclick')
    //断言
    expect(li.classes()).to.be.include('edting')
  })
})

```

最后运行:

```shell
npm run unit
```

即可查看结果

### 使用mocha测试javascript文件

1.新建文件

```shell
mkdir mochatest
```

2.初始化

```shell
npm install -y
```

3.安装mocha

```shell
 npm install mocha
```

4.在文件夹中新建math.js文件

math.js

```js
function add(a,b) {
    return a+b;
}
function minus(a,b) {
    return a-b;
}
module.exports = {
    add,
    minus
}
```

5.新建测试目录test/math.test.js

```js
var math = require('../math.js');
var assert = require('assert');
describe('测试math.js',function(){
    describe('测试add方法',function(){
       it('2+3',function(){
        assert.equal(math.add(2,3),5);
       })
       it('4+3',function(){
        assert.equal(math.add(4,3),5);
       })
    })
    describe('测试minus方法',function(){
        it('5-3',function(){
         assert.equal(math.add(5,3),2);
        })
     })
})
```



6.修改package.json将test修改为'mocha'

```js
 "test": "mocha"
```



7.运行

```shell
npm test
```

### 使用jest测试javascript文件

1.新建文件

```shell
mkdir jestdemo
```

2.初始化

```shell
npm init -y
```

3.安装

```shell
npm install jest
```

4.新建文件,测试文件(同上)

```shell
touch math.js math.test.js
```

math.js

```js
function add(a,b) {
    return a+b;
}
function minus(a,b) {
    return a-b;
}
function getMsg(name,cb){
    setTimeout(()=>{
        cb(`hey ${name`)
    },2000)
}
module.exports = {
    add,
    minus
}
```

math.test.js

```js
var math = require('../math.js');
describe('测试add方法',function(){
    test('add',function(){
        let res = math.add(1,2)
        expect(res).toBe(3)
    })
})
describe('测试异步方法',function(){
    test('getMsg',function(){
		math.getMsg('luck',(msg)=>{
            expect(msg).toBe('hey luck')
        })
    })
})
```



5.运行(修改package.json)---------jest

```shell
npm run test
```

