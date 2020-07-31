# react

博客: https://jspang.com/detailed?id=46 

### react的安装----------方法1

内部如果依赖yarn工具(类似npm工具)直接

```shell
npm i -g yarn
```

设置淘宝镜像

```shell
npm config set registry https://registry.npm.taobao.org  设置成淘宝npm
```

1.安装官方脚手架

```shell
npm install -g create-react-app
```

2.创建项目

```shell
 create-react-app project 
```

3.启动

```shell
npm start
```

4.生成 dist

```shell
npm run build //打包发布

//测试环境打包运行

npm install -g serve 
serve build 

//访问: http://localhost:5000
```

### React安装------方法二

`npx create-react-app test`

####  [React项目中的registerServiceWorker的作用](https://www.cnblogs.com/wphl-27/p/10393666.html)

service worker是在后台运行的一个线程，可以用来处理离线缓存、消息推送、后台自动更新等任务。

registerServiceWorker就是为react项目注册了一个service worker，用来做资源的缓存，这样你下次访问时，就可以更快的获取资源。而且因为资源被缓存，所以即使在离线的情况下也可以访问应用（此时使用的资源是之前缓存的资源）。

但有一点要注意，registerServiceWorker注册的service worker 只在生产环境中生效（process.env.NODE_ENV === 'production'），所以开发的时候，可以注释掉。

当然了，在生产环境中，你也可以选择使用或者不适用这个功能

### 创建第一个React项目

```shell
D:  //进入D盘
mkdir ReactDemo  //创建ReactDemo文件夹
create-react-app demo01   //用脚手架创建React项目
cd demo01   //等创建完成后，进入项目目录
npm start   //预览项目，如果能正常打开，说明项目创建成功
```

### 脚手架生成的项目目录介绍

先从进入项目根目录说起，也就是第一眼看到的文件(版本不同，可能稍有不同)

- **README.md** :这个文件主要作用就是对项目的说明，已经默认写好了一些东西，你可以简单看看。如果是工作中，你可以把文件中的内容删除，自己来写这个文件，编写这个文件可以使用`Markdown`的语法来编写。
- **package.json**: 这个文件是`webpack`配置和项目包管理文件，项目中依赖的第三方包（包的版本）和一些常用命令配置都在这个里边进行配置，当然脚手架已经为我们配置了一些了，目前位置，我们不需要改动。如果你对webpack了解，对这个一定也很熟悉。
- **package-lock.json**：这个文件用一句话来解释，就是锁定安装时的版本号，并且需要上传到git，以保证其他人再`npm install` 时大家的依赖能保证一致。
- **gitignore** : 这个是git的选择性上传的配置文件，比如一会要介绍的`node_modules`文件夹，就需要配置不上传。
- **node_modules** :这个文件夹就是我们项目的依赖包，到目前位置，脚手架已经都给我们下载好了，你不需要单独安装什么。
- **public** ：公共文件，里边有公用模板和图标等一些东西。
- **src** ： 主要代码编写文件，这个文件夹里的文件对我们来说最重要，都需要我们掌握。

#### public文件夹介绍

- **favicon.ico** : 这个是网站或者说项目的图标，一般在浏览器标签页的左上角显示。
- **index.html** : 首页的模板文件，我们可以试着改动一下，就能看到结果。
- **mainifest.json**：移动端配置文件，这个会在以后的课程中详细讲解。

#### src文件夹介绍

- **index.js** : 这个就是项目的入口文件，视频中我们会简单的看一下这个文件。
- **index.css** ：这个是`index.js`里的CSS文件。
- **app.js** : 这个文件相当于一个方法模块，也是一个简单的模块化编程。
- **serviceWorker.js**: 这个是用于写移动端开发的，PWA必须用到这个文件，有了这个文件，就相当于有了离线浏览的功能。





## hello world

index.js

```js
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';
import * as serviceWorker from './serviceWorker';
ReactDOM.render(<App />, document.querySelector('#root'));
serviceWorker.unregister(); //加快react运行速度
```

App.js

```js
import React, { Component } from "react";

class App extends Component{
    render(){
        return (
            <div>hello world</div>
        );
    }
}
export default App;
```

## 基本语法

1.所有模块都要被一个根节点包含起来

2.{}绑定数据

3.组件的构造函数一定要注意super(用于父子组件传值)

4,组件首字母大写

5.本地图片引入(1.可以全局引入.2.src={require('../assets/img/1.jpg')})

6.远程图片:src="http://................"

App.js

```js
import React, { Component } from "react";
import logo from "./logo.svg"

class App extends Component{
    render(){
        const name = "hujin";
        const jsx = <p>hahaha</p>
        function hi(name){
            return "hi " + name
        }
        return (
            <div>
                <h1>hello {name}</h1>
                <h1>{hi(name)}</h1>
                <img src={logo} style={{width:"100px"}}></img>
                {jsx}
            </div>
        );
    }
}
export default App;
```

## 组件

#### Fragment标签

加上最外层的DIV，组件就是完全正常的，但是你的布局就偏不需要这个最外层的标签怎么办?比如我们在作`Flex`布局的时候,外层还真的不能有包裹元素。这种矛盾其实React16已经有所考虑了，为我们准备了`Fragement`标签。

要想使用`Fragement`，需要先进行引入。

```shell
import React,{Component,Fragment } from 'react'
```

代码如下。 

```js
import React,{Component,Fragment } from 'react'

class Xiaojiejie extends Component{
    render(){
        return  (
            <Fragment>
               <div><input /> <button> 增加服务 </button></div>
               <ul>
                   <li>头部按摩</li>
                   <li>精油推背</li>
               </ul> 
            </Fragment>
        )
    }
}
export default Xiaojiejie 
```

 这时候你再去浏览器的`Elements`中查看，就回发现已经没有外层的包裹了。 



#### 组件的应用

在src下新建components文件夹
新建CompType.js文件

```js
import React from "react"

//函数类型的组件
export function Welcome1(props){
    return <div>Welcome1, {props.name}</div>;
}

//基于类的组件
export class Welcome2 extends React.Component {
    render(){
        return <div>Welcome2, {this.props.name}</div>;
    }
}
```

在App.js中添加

```js
{/* 组件 */}
<Welcome1 name="hujin"></Welcome1>
<Welcome2 name="hujin"></Welcome2>
```

###　组件的状态变化

在components文件夹下新建Clock.js文件

```js
import React, { Component } from 'react'

export default class Clock extends Component {
    //状态固定名字
    state = {
        date: new Date()
    }

    componentDidMount(){
        this.timer = setInterval(() => {
            this.setState({
                date: new Date()
            })
        }, 1000);
    }

    componentWillUnmount(){
        clearInterval(this.timer);
    }

    render(){
        return (
            <div>
                {this.state.date.toLocaleTimeString()}
            </div>
        )
    }
}

```

在App.js中加入

```js
import Clock from './components/Clock';

// ...

{/* state状态变化 */}
<Clock></Clock>

```

可以看到不断变化的时钟

修改状态还可以这样,这样多次修改状态值依然生效

```js
this.setState(prevState => {
    return {
        counter: preState.counter + 1
    }
}))


```

### 条件渲染

```js
const title = this.props.title ? this.props.title:''
//等价于
{this.props.title && <h1>{this.props.title}</h1>}
```

### 列表渲染

```js
<ul>
    {this.state.goods.map(good => <li key={good.id}>{good.text}</li>)}
</ul>
```

### React实例

#### 响应式设计和数据的绑定

`React`不建议你直接操作`DOM`元素,而是要通过数据进行驱动，改变界面中的效果。React会根据数据的变化，自动的帮助你完成界面的改变。所以在写React代码时，你无需关注DOM相关的操作，只需要关注数据的操作就可以了（这也是React如此受欢迎的主要原因，大大加快了我们的开发速度）。 

```jsx
//js的构造函数，由于其他任何函数执行
constructor(props){
    super(props) //调用父类的构造函数，固定写法
    this.state={
        inputValue:'' , // input中的值
        list:[]    //服务列表
    }
}
```

 在`React`中的数据绑定和`Vue`中几乎一样，也是采用`字面量`(我自己起的名字)的形式，就是使用`{}`来标注，其实这也算是js代码的一种声明。比如现在我们要把`inputValue`值绑定到`input`框中，只要写入下面的代码就可以了。其实说白了就是在JSX中使用js代码。 

```html
<input value={this.state.inputValue} /> 
```

#### 绑定事件

 这时候你到界面的文本框中去输入值，是没有任何变化的，这是因为我们强制绑定了`inputValue`的值。如果要想改变，需要绑定**响应事件**，改变`inputValue`的值。比如绑定一个改变事件，这个事件执行`inputChange()`(当然这个方法还没有)方法。 

```jsx
<input value={this.state.inputValue} onChange={this.inputChange.bind(this)} />
```

 这步做完，我们还需要加入`setState`方法，改变值。代码如下: 

```jsx
inputChange(e){
    // console.log(e.target.value);
    // this.state.inputValue=e.target.value;
    this.setState({
        inputValue:e.target.value
    })
}
```

#### 让列表数据化

```jsx
constructor(props){
    super(props) //调用父类的构造函数，固定写法
    this.state={
        inputValue:'jspang' , // input中的值
        //----------主要 代码--------start
        list:['基础按摩','精油推背']   
        //----------主要 代码--------end
    }
}
```

有了数据后，可以在`JSX`部分进行循环输出，代码如下：

```javascript
render(){
    return  (
        <Fragment>
            <div>
                <input value={this.state.inputValue} onChange={this.inputChange.bind(this)} />
                <button> 增加服务 </button>
            </div>
            <ul>
                {
                    this.state.list.map((item,index)=>{
                        return <li>{item}</li>
                    })
                }
            </ul>  
        </Fragment>
    )
}
```



#### 增加的方法

```jsx
<button onClick={this.addList.bind(this)}> 增加服务 </button>
```

接下来就是把`this.addList`方法，代码如下：

```javascript
//增加服务的按钮响应方法
addList(){
    this.setState({
        list:[...this.state.list,this.state.inputValue]
    })

}
```

#### 解决key值错误

```jsx
<ul>
    {
        this.state.list.map((item,index)=>{
            return <li key={index+item}>{item}</li>
        })
    }
</ul>  
```



### 删除方法

如果要删除一个东西，就要得到数组里的一个编号，这里指下标。传递下标就要有事件产生，先来绑定一个双击事件.代码如下:

```javascript
<ul>
    {
        this.state.list.map((item,index)=>{
            return (
                <li 
                    key={index+item}
                    onClick={this.deleteItem.bind(this,index)}
                >
                    {item}
                </li>
            )
        })
    }
</ul>  
```

获得了数据下标后,删除数据就变的容易起来.先声明一个局部变量,然后利用传递过来的下标,删除数组中的值.删除后用`setState`更新数据就可以了.

```javascript
//删除单项服务
deleteItem(index){
    let list = this.state.list
    list.splice(index,1)
    this.setState({
        list:list
    })

}
```

其实这里边是有一个坑的,有的小伙伴肯定会认为下面的代码也是正确的.

```javascript
//删除单项服务
deleteItem(index){
    this.state.list.splice(index,1)
    this.setState({
        list:this.state.list
    }) 
}
```

记住React是禁止直接操作state的,虽然上面的方法也管用,但是在后期的性能优化上会有很多麻烦,所以一定不要这样操作.这也算是我`React`初期踩的比较深的一个坑,希望小伙伴们可以跳坑.



### 事件----购物车案例

在components文件夹下新建CartSample.js文件

```js
import React, { Component } from "react";

export default class CartSample extends Component {
  //   状态初始化一般放在构造器中
  constructor(props) {
    super(props);

    this.state = {
      goods: [
        { id: 1, text: "hhhhh" },
        { id: 2, text: "heiheihei" }
      ],
      text: "",
      cart: []
    };

    this.addGood = this.addGood.bind(this);
    //原生函数写法的this默认指向调用该函数的元素对象(在这里指button),
    //所以需要在constructor中绑定到当前CartSample对象
  }

  //回调函数声明为箭头函数
  textChange = e => {
     let text =  e.target.value
    //箭头函数中的this默认指向当前对象(CartSample)
    this.setState({ text });
  };

  addGood() {
    this.setState(prevState => {
      return {
        goods: [//将原值和新值一起赋给goods
          ...prevState.goods,//需要把原值解包
          {
            id: prevState.goods.length + 1,
            text: prevState.text
          }
        ]
      };
    });
  }

  //   加购函数
  addToCart = good => {
    // 创建新购物车
    const newCart = [...this.state.cart];
    const idx = newCart.findIndex(c => c.id === good.id);
    const item = newCart[idx];
    if (item) {
      newCart.splice(idx, 1, { ...item, count: item.count + 1 });
    } else {
      newCart.push({ ...good, count: 1 });
    }
    // 更新
    this.setState({ cart: newCart });
  };

  //   处理数量更新
  add = good => {
    // 创建新购物车
    const newCart = [...this.state.cart];
    const idx = newCart.findIndex(c => c.id === good.id);
    const item = newCart[idx];
    newCart.splice(idx, 1, { ...item, count: item.count + 1 });

    // 更新
    this.setState({ cart: newCart });
  };

  minus = good => {
    // 创建新购物车
    const newCart = [...this.state.cart];
    const idx = newCart.findIndex(c => c.id === good.id);
    const item = newCart[idx];

    newCart.splice(idx, 1, { ...item, count: item.count - 1 });

    // 更新
    this.setState({ cart: newCart });
  };

  render() {
    //   const title = this.props.title ? <h1>this.props.title</h1> : null;
    return (
      <div>
        <ul>
          {this.state.goods.map(good => (
            <li key={good.id}>
              {good.text}
              <button onClick={() => this.addToCart(good)}>加购</button> //click函数里面写箭头函数是为了正确传参
            </li>
          ))}
        </ul>

        <div>
          <input
            type="text"
            value={this.state.text}
            onChange={this.textChange}
          />
          <button onClick={this.addGood}>添加商品</button>
        </div>

        {/* 购物车 */}
        <Cart data={this.state.cart} minus={this.minus} add={this.add} />
      </div>
    );
  }
}

function Cart({ data, minus, add }) {
  return (
    <table>
      <tbody>
        {data.map(d => (
          <tr key={d.id}>
            <td>{d.text}</td>
            <td>
              <button onClick={() => minus(d)}>-</button>
              {d.count}
              <button onClick={() => add(d)}>+</button>
            </td>
            {/* <td>{d.price*d.count}</td> */}
          </tr>
        ))}
      </tbody>
    </table>
  );
}


```
