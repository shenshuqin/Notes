# React 测试

## 配置环境

### 方法

**脚手架安装react**

```shell
create-react-app jeast-react
```

创建react文件已经创建好了jest环境

 **测试工具库 (Enzyme)** 

 虽然最好的react测试框架出自Facebook，但是最受欢迎的React测试工具库却出自Airbnb，这个工具库叫Enzyme，脚手架中并不包括Enzyme，需要我们自己来添加：

```
npm i --save-dev enzyme react-test-renderer enzyme-adapter-react-16   
```

 ==注意：enzyme-adapter-react-16的版本必须和react的版本对应== 

 **断言库（chai）**

Chai 是一个行为驱动开发/测试驱动开发的断言库,chai有三种断言风格供我们使用，expect，should，assert。本次只用到了expect风格的断言用法。expect断言的写法都是一样的。头部是expect方法，尾部是断言方法，比如equal、deepequal、a/an、ok、match等。两者之间使用to或to.be连接, 

```shell
npm install chai -D
```



 **编写测试用例** 

 打开src文件夹，在该文件夹下新建一个setupTests.js（该配置文件的文件名不可随意修改），该文件需要简单的配置： 

```shell
import Enzyme from 'enzyme'
import Adapter from 'enzyme-adapter-react-16'
 
Enzyme.configure({
    adapter:new Adapter()
}) 
```

 一切准备工作都做好啦！新建一个App.test.js文件，该文件就是我们用来编写单元测试代码所需的文件。判断App.js中的标题是否正确，这个用例不需要与DOM互动，不涉及子组件，所以使用浅渲染非常合适。App.test.js: 

```shell
import App from './App'
import React from 'react'
import {expect} from 'chai';
import {shallow } from 'enzyme'
//第一个测试用例：浅渲染
describe('Enzyme Shallow', function () {
    it('App\'s title should be Todos', function () {
      let app = shallow();
      expect(app.find('#title').text()).to.equal('Todos');
   });
}); 
//第二个测试用例：测试所有todo项的初始状态
describe('Enzyme Render', function () {
  it('Todo item should not have todo-done class', function () {
    let app = render();
    expect(app.find('.todo-done').length).to.equal(0);
  });
});  
// 第三个测试用例，测试todo项的删除操作
describe('Enzyme Mount', function () {
    it('Delete Todo', function () {
      let app = mount();
      let todoLength = app.find('li').length;
      app.find('button.delete').at(0).simulate('click');
      expect(app.find('li').length).to.equal(todoLength - 1);
    });
});第一个测试用例：
```

shallow方法返回App的浅渲染，然后app.find方法找出h1元素，text方法取出该元素的文本。第二个测试用例：

render方法将React组件渲染成静态的HTML字符串，然后分析这段HTML代码的结构，返回一个对象。第三个测试用例：

mount方法用于将React组件加载为真实DOM节点，find方法返回一个对象，包含所有符合条件的子组件，at方法返回指定位置的子组件，simulate方法就在这个组件上触发绑定的事件。使用断言库的expect方法：expect（期待）一个表达式的结果equal/deepequal（等于/深度相等）一个表达式。 

#### find 属性

```js
//App.js
<div data-test="container"></div>
//App.test.js
it('App\'s title should be Todos', function () {
      let app = shallow(<App/>);
      expect(app.find('[data-test='container']').length).toBe(1);
   });
```

