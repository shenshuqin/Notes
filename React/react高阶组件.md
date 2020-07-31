###  React组件
#### ant-design的使用
安装:`npm install antd --save`
组件引入
```js
import React, { Component } from 'react'
import Button from 'antd/lib/button'
import 'antd/dist/antd.css'
export default class Test extends Component {
  render() {
    return (
      <div>
        <Button type="primary">按钮</Button>
      </div>
    )
  }
}
```

按需加载

1.安装react-app-rewired取代react-scripts，可以扩展webpack的配置 ，类似vue.config.js
`npm install react-app-rewired@2.0.2-next.0 babel-plugin-import --save`
2.在项目根目录下新建config-overrides.js

```js
const { injectBabelPlugin } = require("react-app-rewired");
module.exports = function override(config, env) {
  config = injectBabelPlugin(
    // 在默认配置基础上注入
    // 插件名，插件配置
    ["import", { libraryName: "antd", libraryDirectory: "es", style: "css" }],
    config
  );

  config = injectBabelPlugin(
    ["@babel/plugin-proposal-decorators", { legacy: true }],
    config
  );

  return config;
};

```
组件引入:
```js
import React, { Component } from 'react'
// import Button from 'antd/lib/button'
// import 'antd/dist/antd.css'
import {Button} from 'antd'
export default class Test extends Component {
  render() {
    return (
      <div>
        <Button type="primary">按钮</Button>
      </div>
    )
  }
}
```

注意:需要将package.json中 react-app-rewired取代react-scripts
### 容器组件VS展示组件
基本原则:容器组件负责数据获取,展示组件负责根据props展示数据
```js
import React, { Component } from "react";
import { func } from "prop-types";

// 容器组件
export default class CommentList extends Component {
  constructor(props) {
    super(props);
    this.state = {
      comments: []
    };
  }
  componentDidMount() {
    setInterval(() => {
      this.setState({
        comments: [
          { body: "react is very good", author: "facebook" },
          { body: "vue is very good", author: "youyuxi" }
        ]
      });
    }, 1000);
  }
  render() {
    return (
      <div>
        {this.state.comments.map((c, i) => (
          <Comment key={i} {...c} />
        ))}
      </div>
    );
  }
}
// 展示组件
// memo高阶组件
const Comment = React.memo(function(props) {
  console.log("render Comment");

  return (
    <div>
      <p>{props.body}</p>
      <p> --- {props.author}</p>
    </div>
  );
});
```

### PureComponent
通常render函数会执行过多的次数, 可以用shouldComponentUpdate返回false来阻止不必要的render
```js
class Comment extends React.Component{

  shouldComponentUpdate(nextProps){
      if (nextProps.data.body === this.props.data.body &&
        nextProps.data.author === this.props.data.author) {
          return false;
      }
      return true;
  }

  render() {
    console.log("render comment");

    return (
      <div>
        <p>{this.props.body}</p>
        <p> --- {this.props.author}</p>
      </div>
    );
  }
}

```
PureComponent定制了shouldComponentUpdate后的Component（浅比较）
```js
class Comp extends React.PureComponent {}
```
只能进行浅比较,尽量避免深层次的变量传递

### React.memo

React v16.6.0 之后的版本，可以使用 React.memo 让函数式的组件也有PureComponent的功能
```js
const Joke = React.memo(() => (
  <div>
    {this.props.value || 'loading...' }
  </div>
  )
)

```

### 高阶组件和链式调用

```js
import React, { Component } from "react";

function Kaikeba(props) {
  return (
    <div>
      {props.stage}-{props.name}
    </div>
  );
}

const withkaikeba = Comp => {
  // 获取name, name可能来自于接口或其他手段
  const name = "高阶组件";
  console.log('withKaikeba');
  //return prorps => <Comp {...props} name={name} />;

  return class extends React.Component{//匿名类
    componentDidMount(){
      console.log('do something');
    }
    render(){
      return <Comp {...this.props} name={name} />;
    }
  };
};

const withLog = Comp => {
  console.log(Comp.name + "渲染了");
  return props => <Comp {...props} ></Comp>
}

const NewKaikeba = withKaikeba(Kaikeba)
const NewKaikeba1 = withLog(withKaikeba(withLog(Kaikeba)));

export default class Hoc extends Component {
  render() {
    return (
      <div>
        <NewKaikeba stage="React" />
        <NewKaikeba1 stage="React" />
      </div>
    )
  }
}

```

### 装饰器

ES7装饰器可用于简化高阶组件写法
`npm install --save-dev babel-plugin-transform-decorators-legacy`

修改config-overrides.js,加入如下config设置后 重启

```js
config = injectBabelPlugin( ['@babel/plugin-proposal-decorators', { "legacy": true }], config, )
```

```js
//将上面的Kaikeba函数改为class,并且将withLog,withKaikeba函数的定义放在前面

@withLog
@withKaikeba
@withLog
class Kaikeba extends Component {
  render() {
    return (
      <div>
        {this.props.stage}-{this.props.name}
      </div>
    );
  }
}

//装饰器的执行顺序从上至下 相当于 const Kaikeba = withLog(withKaikeba(withLog(Kaikeba)));
//后续 只需要调用 <Kaikeba stage="React" />
```

## 复合

```js
//Dialog 作为容器不关心内容和逻辑
function Dialog(props) {
  return (
    <div style={{ border: `4px solid ${props.color || "blue"}`}}>
      {props.children}
      <div className="footer">{props.footer}</div>
    </div>
  );
}

//WelcomeDialog 通过复合提供内容
function WelcomeDialog(props){
  return (
    <Dialog {...props}>
      <h1>欢迎光临</h1>
      <p>感谢使用react</p>
    </Dialog>
  );
}

const Api = {
  getUser(){
    return { name: "jerry", age: 20 };
  }
}

function Fetcher(props) {
  const user = Api[props.name]();
  return props.children(user)
}

function Filter({children, type}){
  return (
    <div>
      {React.Children.map(children, child =>{
        if (child.type !== type){
          return;
        }
        return child;
      })}
    </div>
  );
}

function RadioGroup(props){
  return (
    <div>
      {React.Children.map(props.children, child =>{
        return React.cloneElement(child, {name: props.name});//child.props.name = props.name的效果,虚拟dom不可以更改,必须克隆一个新的去改.
      })}
    </div>
  );
}

function Radio({children, ...rest}){
  return (
    <label>
      <input type="radio" {...rest} />
      {children}
    </label>
  );
}

export default function(){
  const footer = <button onClick={() => alert("确定!")}>确定</button>
  return (
    <div>
      <WelcomeDialog color="green" footer={footer} />
    </div>
    <Fetcher name="getUser">
      {({ name, age }) => (
        <p>
          {name}-{age}
        </p>
      )

      }
    </Fetcher>
    <Filter type="p">
      <h1>react</h1>
      <p>react很不错</p>
      <h1>vue</h1>
      <p>vue很不错</p>
    </Filter>
    <radioGroup name="mvvm">
      <Radio value="vue">vue</Radio>
      <Radio value="react">react</Radio>
      <Radio value="angular">angular</Radio>
    </radioGroup>
  )

}
```
### Hook
Hook是react16.8的新增项,它可以在不编写class的情况 下使用state以及react特性.
如果React版本不是16.8需要升级
`npm i react react-dom -S`

**状态钩子** **- State Hook** 

创建HooksTest.js 
```js
import React, { useState } from "react"; 
export default function HooksTest() { 
    // useState(initialState)，接收初始状态，返回一个状态变量和其更新函数 
    const [count, setCount] = useState(0); 
    return ( 
        <div> 
            <p>You clicked {count} times</p> 
             <button onClick={() => setCount(count + 1)}>Click me</button> 
         </div> 
     ); 
}
```

**副作用钩子** **- Effffect Hook**(副作用钩子会在每次渲染时都执行)

更新HooksTest.js 

```js
import React, { useState, useEffect } from "react"; 
useEffect(() => { 
    // Update the document title using the browser API 
    //副作用钩子会在每次渲染时都执行
    document.title = `您点击了 ${count} 次`;
});
```

**自定义钩子** **- Custom Hook**

自定义hook是一个函数，名称用“use"开头，函数内部可以调用其他钩子

```js
function useAge() {
  const [age, setAge] = useState(0);
  useEffect(() => {
    setTimeout(() => {
      setAge(20);
    }, 2000);
  });
  return age;
}
 const age = useAge();
```
完整HookTest.js
```js
import React, { useState, useEffect } from "react";

// 自定义hook是一个函数，名称用“use"开头，函数内部可以调用其他钩子
function useAge() {
  const [age, setAge] = useState(0);
  useEffect(() => {
    setTimeout(() => {
      setAge(20);
    }, 2000);
  });
  return age;
}

export default function HookTest() {
  // useState(initState)
  const [count, setCount] = useState(0);

  // 副作用钩子会在每次渲染时都执行
  useEffect(() => {
    document.title = `您点击了${count}次`;
  }, [count]);

  //   如果仅打算执行一次，传递第二个参数为[]
  //   componentDidMount
  useEffect(() => {
    // api调用
    console.log("api调用");
  }, []);

  // 多个状态
  const age = useAge();
  const [fruit, setFruit] = useState("banana");
  const [input, setInput] = useState("");
  const [fruits, setFruits] = useState(["apple", "banana"]);

  return (
    <div>
      <p>点击了{count}次</p>
      <button onClick={() => setCount(count + 1)}>点击</button>

      <p>年龄：{age ? age : 'loading...'}</p>
      <p>选择的水果：{fruit}</p>
      <p>
        <input
          type="text"
          value={input}
          onChange={e => setInput(e.target.value)}
        />
        <button onClick={() => setFruits([...fruits, input])}>新增水果</button>
      </p>
      <ul>
        {fruits.map(f => (
          <li key={f} onClick={() => setFruit(f)}>
            {f}
          </li>
        ))}
      </ul>
    </div>
  );
}
```
### 组件跨层级通信 - Context
上下文提供一种不需要每层设置props就能跨多级组件传递数据的方式
基本用法
+ 创建上下文
```js
const MyContext = React.createContext();
```
+ 提供上下文
```js
const { Provider } = MyContext; 
export default function App() { 
    return ( 
        <div> 
          <Provider value={{foo: "bar"}}> <Child /> </Provider> 
         </div> 
		); 
}
```
+ 消费上下文
```js
function Child3(props){ 
    return 
      <div>{props.foo}</div> 
 }
export default function App() { 
    return ( 
        <div> 
            <Provider value={{ foo: "bar" }}> 
            <Consumer> {value => <Child2 {...value}/>} </Consumer> 
            </Provider> 
      </div> 
); 
}
```

ContetTest.js
```js
import React, { useContext } from "react";

// 1.创建上下文
const MyContext = React.createContext();
const { Provider, Consumer } = MyContext;

function Child(prop) {
  return <div>Child: {prop.foo}</div>;
}
// 使用hook消费
function Child2() {
  const context = useContext(MyContext);
  return <div>Child2: {context.foo}</div>;
}
// 使用class指定静态contextType
class Child3 extends React.Component {
    // 设置静态属性通知编译器获取上下文中数据并赋值给this.context
    static contextType = MyContext;
    render() {
        return <div>Child3: {this.context.foo}</div>
    }
}
export default function ContextTest() {
  return (
    <div>
      <Provider value={{ foo: "bar" }}>
        {/* 消费方法1：Consumer */}
        <Consumer>{value => <Child {...value} />}</Consumer>
        {/* 消费方法2：hook */}
        <Child2 />
        {/* 消费方法3：contextType */}
        <Child3 />
      </Provider>
    </div>
  );
}

```