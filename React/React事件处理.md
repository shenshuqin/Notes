## React事件绑定

### 1.在构造函数中使用bind绑定this

```js
class Button extends React.Component {
constructor(props) {
    super(props);
    this.handleClick = this.handleClick.bind(this);
  }
  handleClick(){
    console.log('this is:', this);
  }
  render() {
    return (
      <button onClick={this.handleClick}>
        Click me
      </button>
    );
  }
}
```

### 2. 在调用的时候使用bind绑定this

```js
class Button extends React.Component {
  handleClick(){
    console.log('this is:', this);
  }
  render() {
    return (
      <button onClick={this.handleClick.bind(this)}>
        Click me
      </button>
    );
  }
}
```

### 3. 在调用的时候使用箭头函数绑定this

```js
class Button extends React.Component {
  handleClick(){
    console.log('this is:', this);
  }
  render() {
    return (
      <button onClick={()=>this.handleClick()}>
        Click me
      </button>
    );
  }
}
```

### 4. 使用属性初始化器语法绑定this(经常用)

```js
class Button extends React.Component {
  handleClick=()=>{
    console.log('this is:', this);
  }
  render() {
    return (
      <button onClick={this.handleClick}>
        Click me
      </button>
    );
  }
}
```

### 5.事件传参

```js
class Button extends React.Component {
  handleClick=(good)=>{
    console.log('this is:', this);
  }
  render() {
    return (
      <button onClick={()=>this.handleClick(good)}>
        Click me
      </button>
    );
  }
}
```



## 改变数据

```js
class MyComponent extends React.Component{
  constructor(props){
        super(props);
        this.state = {number:0};
  }

//ES7 的属性初始化语法，实际上也是使用了箭头函数
handleClick ＝ (event)=>{
    const number = ++this.state.number;
    this.setState({
       number:number
    });
}
  
   render(){
       return(
        <div>
             <div>{this.state.number}</div>
              <button onClick={this.handleClick} >
        </div>
       )
   }
}
```

### 事件对象

```js
class MyComponent extends React.Component{
  constructor(props){
        super(props);
        this.state = {number:0};
  }

run ＝ (event)=>{
        // alert(event.target);   /*获取执行事件的dom节点*/
        event.target.style.background='red';
        //获取dom的属性
        alert(event.target.getAttribute('aid'))
}
  
   render(){
       return(
        <div>
             <div>{this.state.number}</div>
              <button aid="123" onClick={this.run}>事件对象</button>
        </div>
       )
   }
}
```

### 表单事件

```js

import React from 'react';

class Home extends React.Component{
    // 子类必须在constructor方法中调用super方法，否则新建实例时会报错。这是因为子类没有自己的this对象，而是继承父类的this对象，然后对其进行加工。如果不调用super方法，子类就得不到this对象

    constructor(props){
        super(props);   //固定写法
        this.state={
            msg:'我是一个home组件',
            username:''
        }   
    }

    inputChange=(e)=>{
        // let val=this.refs.username.value;
        //获取表单的值
        console.log(e.target.value);
        this.setState({
            username:e.target.value
        })

    }

    getInput=()=>{

        alert(this.state.username);
    }

    render(){
        return(
            <div>              
               {this.state.msg}
                {/* 获取表单的值
                1、监听表单的改变事件                        onChange
                2、在改变的事件里面获取表单输入的值           事件对象
                3、把表单输入的值赋值给username              this.setState({})
                4、点击按钮的时候获取 state里面的username     this.state.username
                 */}
// <input ref="username" onChange={this.inputChange}/> <button onClick={this.getInput}>获取input的值</button>
        <input onChange={this.inputChange}/> <button onClick={this.getInput}>获取input的值</button>
            </div>
        )
    }    
}
export default Home;

```

### 实现双向数据绑定

```js
import React,{Component} from 'react';

class Todolist extends Component {
    constructor(props) {
        super(props);
        this.state = { 

            username:"111"
        };
    }
    inputChange=(e)=>{
        this.setState({

            username:e.target.value
        })

    }
    setUsername=()=>{
        this.setState({
            username:'李四'
        })
    }
    render() {
        return (
            <div>
                <h2>双休数据绑定</h2>
                {/* model改变影响View    view改变反过来影响model  */}
                <input  value={this.state.username} onChange={this.inputChange}/> 
               <p> {this.state.username}</p> 
               <button onClick={this.setUsername}>改变username的值</button>
            </div>
        );
    }
}
export default Todolist;
```

