## Redux

### 基础--Ant Design

是一套面向企业级开发的UI框架，视觉和动效作的很好。阿里开源的一套UI框架 

官网: https://ant.design/index-cn 

1.安装

 这里使用`npm`来进行安装，当然你有可以用`yarn`的方式进行安装. 

```shell
npm install antd --save
```

`yarn`的安装方式是:

```
yarn add antd
```

2.引入css

```js
import 'antd/dist/antd.css'
```

案列:TodoList整个代码:

```jsx
import React, { Component } from 'react';
import 'antd/dist/antd.css'
import { Input , Button , List } from 'antd'

const data=[
    '早8点开晨会，分配今天的开发工作',
    '早9点和项目经理作开发需求讨论会',
    '晚5:30对今日代码进行review'
]

class App extends Component {
    render() { 
        return ( 
            <div style={{margin:'10px'}}>
                <div>
                    <Input placeholder='write someting' style={{ width:'250px', marginRight:'10px'}}/>
                    <Button type="primary">增加</Button>
                </div>
                <div style={{margin:'10px',width:'300px'}}>
                    <List
                        bordered
                        dataSource={data}
                        renderItem={item=>(<List.Item>{item}</List.Item>)}
                    />    
                </div>
            </div>
         );
    }
}
export default App;
```

### 创建Redux的仓库---store和reducer

#### 创建store仓库

 在使用`Redux`之前，需要先用`npm install`来进行安装,打开终端，并进入到项目目录，然后输入。（如果你之前安装过了，就不用再次安装了） 

```shell
npm install --save redux
```

安装好`redux`之后，在`src`目录下创建一个`store`文件夹,然后在文件夹下创建一个`index.js`文件。

`index.js`就是整个项目的`store`文件，打开文件，编写下面的代码。

```js
import { createStore } from 'redux'  // 引入createStore方法
const store = createStore()          // 创建数据存储仓库
export default store                 //暴露出去
```

这样虽然已经建立好了仓库，但是这个仓库很混乱，这时候就需要一个有管理能力的模块出现，这就是`Reducers`。这两个一定要一起创建出来，这样仓库才不会出现互怼现象。在`store`文件夹下，新建一个文件`reducer.js`,然后写入下面的代码。

```js
const defaultState = {}  //默认数据
export default (state = defaultState,action)=>{  //就是一个方法函数
    return state
}
```

- `state`: 是整个项目中需要管理的数据信息,这里我们没有什么数据，所以用空对象来表示。

这样`reducer`就建立好了，把reducer引入到`store`中,再创建store时，以参数的形式传递给store。

```js
import { createStore } from 'redux'  //  引入createStore方法
import reducer from './reducer'    
const store = createStore(reducer) // 创建数据存储仓库
export default store   //暴露出去
```

#### 在store中为todoList初始化数据

仓库`store`和`reducer`都创建好了，可以初始化一下`todoList`中的数据了，在`reducer.js`文件的`defaultState`对象中，加入两个属性:`inputValue`和`list`。代码如下

```js
const defaultState = {
    inputValue : 'Write Something',
    list:[
        '早上4点起床，锻炼身体',
        '中午下班游泳一小时'
    ]
}
export default (state = defaultState,action)=>{
    return state
}
```

这就相当于你给`Store`里增加了两个新的数据。

#### 组件获得store中的数据

有了store仓库，也有了数据，那如何获得stroe中的数据那？你可以在要使用的组件中，先引入store。 我们todoList组件要使用store，就在`src/TodoList.js`文件夹中，进行引入。这时候可以删除以前写的data数据了。

```js
import store from './store/index'
```

当然你也可以简写成这样:

```js
import store from './store'
```

引入`store`后可以试着在构造方法里打印到控制台一下，看看是否真正获得了数据，如果一切正常，是完全可以获取数据的。

```js
constructor(props){
    super(props)
    console.log(store.getState())
}
```

这时候数据还不能在UI层让组件直接使用，我们可以直接复制给组件的`state`，代码如下(我这里为了方便学习，给出全部代码了).

```js
import React, { Component } from 'react';
import 'antd/dist/antd.css'
import { Input , Button , List } from 'antd'
import store from './store'



class TodoList extends Component {
constructor(props){
    super(props)
    //关键代码-----------start
    this.state=store.getState();
    //关键代码-----------end
    console.log(this.state)
}
    render() { 
        return ( 
            <div style={{margin:'10px'}}>
                <div>

                    <Input placeholder={this.state.inputValue} style={{ width:'250px', marginRight:'10px'}}/>
                    <Button type="primary">增加</Button>
                </div>
                <div style={{margin:'10px',width:'300px'}}>
                    <List
                        bordered
                        //关键代码-----------start
                        dataSource={this.state.list}
                        //关键代码-----------end
                        renderItem={item=>(<List.Item>{item}</List.Item>)}
                    />    
                </div>
            </div>
         );
    }
}
export default TodoList;
```

### Redux DevTools安装

配置:在store/index.js中

```jsx
import { createStore } from 'redux'  //  引入createStore方法
import reducer from './reducer'    
const store = createStore(reducer,
window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__()) // 创建数据存储仓库
export default store   //暴露出去
```

其实就是加了这样一句话.

```js
window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__()
```

### 通过Input体验Redux的流程

 如果想`Input`改变，`redux`也跟着改变，需要在`Input`组件上增加`onChange`响应事件， 打开`src`目录下的`ToDolist.js`文件，修改具体代码如下： 

```javascript
<Input 
    placeholder={this.state.inputValue} 
    style={{ width:'250px', marginRight:'10px'}}
    //---------关键代码----start
    onChange={this.changeInputValue}
    //---------关键代码----end
/>
```

写完这一步，还要记得在`constructor`进行`this`的绑定，修改`this`的指向。

```javascript
constructor(props){
    super(props)
    this.state=store.getState();
    this.changeInputValue= this.changeInputValue.bind(this)
}
```

 这里给出目前的全部代码: 

```javascript
import React, { Component } from 'react';
import 'antd/dist/antd.css'
import { Input , Button , List } from 'antd'
import store from './store'



class TodoList extends Component {
    constructor(props){
        super(props)
        this.state=store.getState();
        this.changeInputValue= this.changeInputValue.bind(this)
    }
    render() { 
        return ( 
            <div style={{margin:'10px'}}>
                <div>
                    <Input 
                        placeholder={this.state.inputValue} 
                        style={{ width:'250px', marginRight:'10px'}}
                        onChange={this.changeInputValue}
                    />
                    <Button type="primary">增加</Button>
                </div>
                <div style={{margin:'10px',width:'300px'}}>
                    <List
                        bordered
                        dataSource={this.state.list}
                        renderItem={item=>(<List.Item>{item}</List.Item>)}
                    />    
                </div>
            </div>
         );
    }

    changeInputValue(e){
        console.log(e.target.value)
    }
}
export default TodoList;
```

 下面需要作的事就是改变`Redux`里的值了，我们继续向下学习。 

#### 创建Action

想改变`Redux`里边`State`的值就要创建`Action`了。Action就是一个对象，这个对象一般有两个属性，第一个是对`Action`的描述，第二个是要改变的值。

```js
changeInputValue(e){
    const action ={
        type:'change_input_value',
        value:e.target.value
    }
}
```

action就创建好了，但是要通过`dispatch()`方法传递给`store`。我们在action下面再加入一句代码。

```js
changeInputValue(e){
    const action ={
        type:'changeInput',
        value:e.target.value
    }
    store.dispatch(action)
}
```

这是`Action`就已经完全创建完成了，也和`store`有了联系。

#### store的自动推送

前面的课程，我已经说了`store`只是一个仓库，它并没有管理能力，它会把接收到的`action`自动转发给`Reducer`。我们现在先直接在`Reducer`中打印出结果看一下。打开`store`文件夹下面的`reducer.js`文件，修改代码。

```javascript
export default (state = defaultState,action)=>{
    console.log(state,action)
    return state
}
```

讲到这里，就可以解释一下两个参数了：

- **state**: 指的是原始仓库里的状态。
- **action**: 指的是action新传递的状态。

通过打印你可以知道，`Reducer`已经拿到了原来的数据和新传递过来的数据，现在要作的就是改变store里的值。我们先判断`type`是不是正确的，如果正确，我们需要从新声明一个变量`newState`。（**记住：Reducer里只能接收state，不能改变state。**）,所以我们声明了一个新变量，然后再次用`return`返回回去。

```js
export default (state = defaultState,action)=>{
    if(action.type === 'changeInput'){
        let newState = JSON.parse(JSON.stringify(state)) //深度拷贝state
        newState.inputValue = action.value
        return newState
    }
    return state
}
```

#### 让组件发送更新

现在store里的数据已经更新了，但是组件还没有进行更新，我们需要打开组件文件`TodoList.js`，在`constructor`，写入下面的代码。

```js
constructor(props){
    super(props)
    this.state=store.getState();
    this.changeInputValue= this.changeInputValue.bind(this)
    //----------关键代码-----------start
    this.storeChange = this.storeChange.bind(this)  //转变this指向
    store.subscribe(this.storeChange) //订阅Redux的状态
    //----------关键代码-----------end
}
```

当然我们现在还没有这个`storeChange`方法，只要写一下这个方法，并且重新`setState`一次就可以实现组件也是变化的。在代码的最下方，编写`storeChange`方法。

```js
 storeChange(){
     this.setState(store.getState())
 }
```

#### 增加功能

先来编写按钮点击后的响应事件，打开`TodoList.js`文件，然后在按钮的地方加入`onClick`事件，记得要进行绑定哦。

```js
<Button 
    type="primary"
    onClick={this.clickBtn}
>增加</Button>
```

然后在`constructor`里进行绑定，代码如下:

```js
constructor(props){
    super(props)
    this.state=store.getState();
    this.changeInputValue= this.changeInputValue.bind(this)
    this.storeChange = this.storeChange.bind(this)
    //关键代码------------start----------
    this.clickBtn = this.clickBtn.bind(this)
    //关键代码------------end----------
    store.subscribe(this.storeChange) //订阅Redux的状态
}
```

绑定之后就可以编写`clickBtn()`方法了，这里先用一个打印语句代替业务内容。

```js
clickBtn(){
    console.log('jspang.com')
}
```

这时候预览一下，点击"增加按钮"，在控制台就可以输出`jspang.com`了。说明我们的事件添加成功了。

#### 创建Action并用dispatch()传递给store

在`clickBtn`方法里增加Action，然后用`dispatch()`方法传递给store，代码如下:

```js
clickBtn(){
   const action = { type:'addItem'}
   store.dispatch(action)
}
```

这时候已经把action传递给了`store`，然后去`Reducer`里编写业务逻辑就可以了。

#### 编写Reducer的业务逻辑

打开`reducer.js`文件，先编写代码判断`type`是不是`addItem`，如果向redux的list中插入新值。

```js
export default (state = defaultState,action)=>{
    if(action.type === 'changeInput'){
        let newState = JSON.parse(JSON.stringify(state)) //深度拷贝state
        newState.inputValue = action.value
        return newState
    }
    //关键代码------------------start----------
    //state值只能传递，不能使用
    if(action.type === 'addItem' ){ //根据type值，编写业务逻辑
        let newState = JSON.parse(JSON.stringify(state)) 
        newState.list.push(newState.inputValue)  //push新的内容到列表中去
        newState.inputValue = ''
        return newState
    }
     //关键代码------------------end----------
    return state
}
```

之前已经编写了订阅了, 所以到这里就可以打开浏览器进行预览了 

### 用Redux实现ToDoList的删除功能

#### 绑定子项响应事件

打开src目录下的`TodoList.js`文件，然后找到`List`组件的`renderItem`属性，编写代码如下:

```jsx
<div style={{margin:'10px',width:'300px'}}>
    <List
        bordered
        dataSource={this.state.list}
        renderItem={(item,index)=>(<List.Item onClick={this.deleteItem.bind(this,index)}>{item}</List.Item>)}
    />    
</div>
```

然后编写这个`deleteItem()`方法，记得它需要接收一个`index`参数。

```js
deleteItem(index){
    console.log(index)
}
```

这时候我们到浏览器预览一下，按F12打开控制台，可以看到点击按钮时可以看到控制台输出了对应的数组下标。

#### 在方法编写Redux的Acton

写完绑定方法就可以写`action`了，在编写时，我们要传递index过去，代码如下：

```js
deleteItem(index){
    const action = {
        type:'deleteItem',
        index
    }
    store.dispatch(action)
}
```

#### reducer业务逻辑的实现

编写和传递完`action`就可以到`reducer.js`来编写相关的业务逻辑了。其实要作的就是删除数组下标的对应值。

```js
if(action.type === 'deleteItem' ){ 
    let newState = JSON.parse(JSON.stringify(state)) 
    newState.list.splice(action.index,1)  //删除数组中对应的值
    return newState
}
```

这时候就做完了这个TodoList组件的基本功能，当然，这个案例还是有很多不足的，我们需要学习更多的知识来完善它，这节课先到这里，下节课继续学习了。

### 工作中的Redux技巧1

#### 把Action Types单独写入一个文件

写`Redux Action`的时候，我们写了很多Action的派发，产生了很多`Action Types`，如果需要`Action`的地方我们就自己命名一个`Type`,会出现两个基本问题：

- 这些Types如果不统一管理，不利于大型项目的服用，设置会长生冗余代码。
- 因为`Action`里的`Type`，一定要和`Reducer`里的`type`一一对应在，所以这部分代码或字母写错后，浏览器里并没有明确的报错，这给调试带来了极大的困难。

那我司中会把`Action Type`单独拆分出一个文件。在`src/store`文件夹下面，新建立一个`actionTypes.js`文件，然后把Type集中放到文件中进行管理。

```js
export const  CHANGE_INPUT = 'changeInput'
export const  ADD_ITEM = 'addItem'
export const  DELETE_ITEM = 'deleteItem'
```

#### 引入Action中并使用

写好了`ationType.js`文件，可以引入到`TodoList.js`组件当中，引入代码如下：

```js
import { CHANGE_INPUT , ADD_ITEM , DELETE_ITEM } from './store/actionTypes'
```

引入后可以在下面的代码中进行使用这些常量代替原来的`Type`值了.

```js
changeInputValue(e){
    const action ={
        type:CHANGE_INPUT,
        value:e.target.value
    }
    store.dispatch(action)
}
clickBtn(){
    const action = { type:ADD_ITEM }
    store.dispatch(action)
}
deleteItem(index){
    const action = {  type:DELETE_ITEM, index}
    store.dispatch(action)
}
```

#### 引入Reducer并进行更改

也是先引入`actionType.js`文件，然后把对应的字符串换成常量，整个代码如下：

```js
import {CHANGE_INPUT,ADD_ITEM,DELETE_ITEM} from './actionTypes'

const defaultState = {
    inputValue : 'Write Something',
    list:[
        '早上4点起床，锻炼身体',
        '中午下班游泳一小时'
    ]
}
export default (state = defaultState,action)=>{
    if(action.type === CHANGE_INPUT){
        let newState = JSON.parse(JSON.stringify(state)) //深度拷贝state
        newState.inputValue = action.value
        return newState
    }
    //state值只能传递，不能使用
    if(action.type === ADD_ITEM ){ //根据type值，编写业务逻辑
        let newState = JSON.parse(JSON.stringify(state)) 
        newState.list.push(newState.inputValue)  //push新的内容到列表中去
        newState.inputValue = ''
        return newState
    }
    if(action.type === DELETE_ITEM ){ //根据type值，编写业务逻辑
        let newState = JSON.parse(JSON.stringify(state)) 
        newState.list.splice(action.index,1)  //push新的内容到列表中去
        return newState
    }
    return state
}
```

这样就完成了分离

### 工作中的Redux技巧2

#### 编写actionCreators.js

在`/src/store`文件夹下面，建立一个心的文件`actionCreators.js`，先在文件中引入上节课编写`actionTypes.js`文件。

```js
import {CHANGE_INPUT}  from './actionTypes'
```

引入后可以用`const`声明一个`changeInputAction`变量，变量是一个箭头函数，代码如下：

```js
import {CHANGE_INPUT}  from './actionTypes'

export const changeInputAction = (value)=>({
    type:CHANGE_INPUT,
    value
})
```

#### 修改Todolist中的代码

有了文件，就可以把`actionCreatores.js`引入到`TodoLisit`中。

```js
import {changeInputAction} from './store/actionCreatores'
```

引入后，可以把`changeInputValue()`方法，修改为下面的样子。

```js
changeInputValue(e){
        const action = changeInputAction(e.target.value)
        store.dispatch(action)
    }
```

然后再浏览器中打开程序，进行测试，也是完全正常的。

### Axios异步获取数据和Redux结合

在组件的声明周期函数里`componentDidMount`获取远程接口数据。

```js
componentDidMount(){
    axios.get('https://www.easy-mock.com/mock/5cfcce489dc7c36bd6da2c99/xiaojiejie/getList').then((res)=>{
        console.log(res)
    })
}
```

#### 获取数据后结合Redux

接下来就可以把得到的数据放到`Redux`的`store`中了，这部分和以前的知识都一样，我就尽量给出代码，少讲理论了。 先创建一个函数，打开以前写的`store/actionCreatores.js`函数，然后写一个新的函数，代码如下：

```js
export const getListAction  = (data)=>({
    type:GET_LIST,
    data
})
```

这时候保存会显示找不到`GET_LIST`,我们再到`actionTypes.js`文件中加入一个常量，然后引入到`actionCreatores.js`中

```js
export const  GET_LIST = 'getList'
```

引入到`actionCreatores.js`中

```js
import {CHANGE_INPUT , ADD_ITEM , DELETE_ITEM , GET_LIST}  from './actionTypes'
```

这步完成后，回到`TodoList.js`文件，继续编写`axios`中的回调内容，在写之前，记得先把`getListAction`进行引入。

```js
import {changeInputAction , addItemAction ,deleteItemAction,getListAction} from './store/actionCreatores'
componentDidMount(){
    axios.get('https://www.easy-mock.com/mock/5cfcce489dc7c36bd6da2c99/xiaojiejie/getList').then((res)=>{    
        const data = res.data
        const action = getListAction(data)
        store.dispatch(action)
    })
}
```

现在数据已经通过`dispatch`传递给`store`了，接下来就需要`reducer`处理业务逻辑了。打开`reducer.js`代码如下(详细步骤在代码中作了注释)：

```js
//----关键代码--------start --------引入GET_LIST
import {CHANGE_INPUT,ADD_ITEM,DELETE_ITEM,GET_LIST} from './actionTypes'
//----关键代码--------end 
const defaultState = {
    inputValue : 'Write Something',
    //----关键代码--------start --------删除原来的初始化代码，减少冗余
    list:[]
}
export default (state = defaultState,action)=>{
    if(action.type === CHANGE_INPUT){
        let newState = JSON.parse(JSON.stringify(state)) //深度拷贝state
        newState.inputValue = action.value
        return newState
    }
    if(action.type === ADD_ITEM ){ 
        let newState = JSON.parse(JSON.stringify(state)) 
        newState.list.push(newState.inputValue)  //push新的内容到列表中去
        newState.inputValue = ''
        return newState
    }
    if(action.type === DELETE_ITEM ){ //根据type值，编写业务逻辑
        let newState = JSON.parse(JSON.stringify(state)) 
        newState.list.splice(action.index,1)  //push新的内容到列表中去
        return newState
    }
    //----关键代码--------start --------
    if(action.type === GET_LIST ){ //根据type值，编写业务逻辑
        let newState = JSON.parse(JSON.stringify(state)) 
        newState.list = action.data.data.list //复制性的List数组进去
        return newState
    }
    //----关键代码--------en'd --------

    return state
}
```

这样就完成了一次从后台请求数据，然后和Redux结合的过程

## Redux中间件

### Redux-thunk

#### 1.安装

`Redux-thunk`并不在`Redux`基础组件中，也就是说需要进行新安装。安装使用`npm`就可以了。

```
npm install --save redux-thunk
```

### 2.配置

安装作起来很容易，但是配置就要稍微注意一下了，这里边还是有几个小坑的，如果你完全按照官方文档是配置不成功的。 需要在创建store的地方引入`redux-thunk`，对于我们的目录来说，就是`/store/index.js`文件。

1.引入`applyMiddleware`,如果你要使用中间件，就必须在redux中引入`applyMiddleware`.

```js
import { createStore , applyMiddleware } from 'redux' 
```

2.引入`redux-thunk`库

```js
import thunk from 'redux-thunk'
```

如果你按照官方文档来写，你直接把thunk放到`createStore`里的第二个参数就可以了，但以前我们配置了`Redux Dev Tools`，已经占用了第二个参数。

官方文档给的方法:

```js
const store = createStore(
    reducer,
    applyMiddleware(thunk)
) // 创建数据存储仓库
```

这样写是完全没有问题的，但是我们的`Redux Dev Tools`插件就不能使用了，如果想两个同时使用，需要使用**增强函数**。使用增加函数前需要先引入`compose`。

```js
import { createStore , applyMiddleware ,compose } from 'redux' 
```

然后利用`compose`创造一个增强函数，就相当于建立了一个链式函数，代码如下:

```js
const composeEnhancers =   window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ ?
    window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__({}):compose
```

有了增强函数后，就可以把`thunk`加入进来了，这样两个函数就都会执行了。

```js
const enhancer = composeEnhancers(applyMiddleware(thunk))
```

这时候直接在`createStore`函数中的第二个参数，使用这个`enhancer`变量就可以了，相当于两个函数都执行了。

```js
const store = createStore( reducer, enhancer) // 创建数据存储仓库
```

也许你对增加函数还不能完全理解，其实你完全把这个想成固定代码，直接使用就好，我在这里给出全部代码，方便你以后学习使用。

```js
import { createStore , applyMiddleware ,compose } from 'redux'  //  引入createStore方法
import reducer from './reducer'    
import thunk from 'redux-thunk'

const composeEnhancers =   window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ ?
    window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__({}):compose

const enhancer = composeEnhancers(applyMiddleware(thunk))

const store = createStore( reducer, enhancer) // 创建数据存储仓库
export default store   //暴露出去
```

这样就算把`Redux`的中间件配置好了，可以运行项目，到浏览器看一下结果和看一下`Redux Dev Tools`插件了。

### Redux-thunk的使用方法

把向后台请求数据的程序放到中间件中，这样就形成了一套完整的Redux流程，所有逻辑都是在Redux的内部完成的，这样看起来更完美，而且这样作自动化测试也会变动简单很多，所以工作中你还是要尽量按照这种写法来写 

#### actionCreators.js里编写业务逻辑

以前`actionCreators.js`都是定义好的action，根本没办法写业务逻辑，有了`Redux-thunk`之后，可以把`TodoList.js`中的`componentDidMount`业务逻辑放到这里来编写。也就是把向后台请求数据的代码放到`actionCreators.js`文件里。那我们需要引入`axios`,并写一个新的函数方法。（以前的action是对象，现在的action可以是函数了，这就是`redux-thunk`带来的好处）

```js
import axios from 'axios'
...something...
export const getTodoList = () =>{
    return ()=>{
        axios.get('https://www.easy-mock.com/mock/5cfcce489dc7c36bd6da2c99/xiaojiejie/getList').then((res)=>{
            const data = res.data
            console.log(data)
        })
    }
}
```

现在我们需要执行这个方法，并在控制台查看结果，这时候可以修改`TodoList.js`文件中的`componentDidMount`代码。

```js
//先引入getTodoList
import {getTodoList , changeInputAction , addItemAction ,deleteItemAction,getListAction} from './store/actionCreatores'
---something---
componentDidMount(){
    const action = getTodoList()
    store.dispatch(action)
}
```

然后我们到浏览器的控制台中查看一下，看看是不是已经得到了后端传给我们的数据，如果一切正常，应该是可以得到。得到之后，我们继续走以前的Redux流程就可以了。

```js
export const getTodoList = () =>{
    return (dispatch)=>{
        axios.get('https://www.easy-mock.com/mock/5cfcce489dc7c36bd6da2c99/xiaojiejie/getList').then((res)=>{
            const data = res.data
            const action = getListAction(data)
            dispatch(action)
        })
    }
}
```

## redux-saga

### 1.安装

```
npm install --save redux-saga
```

### 2.创建Redux-saga

安装好后，就可以使用了，直接在`/src/store/index.js`里引入`redux-saga`,并创建一个`sagaMiddleware`，代码如下:

```js
import createSagaMiddleware from 'redux-saga'   //引入saga
const sagaMiddleware = createSagaMiddleware();   //创建saga中间件
```

创建好后，还是用Redux的增强函数进行传递。也就是把原来的`Redux-thunk`替换成`saga`中间件（注意去掉原来不用的`redux-thunk`引入）。

```js
import { createStore , applyMiddleware ,compose } from 'redux'  //  引入createStore方法
import reducer from './reducer'   
//------关键代码----start----------- 
import createSagaMiddleware from 'redux-saga' 
const sagaMiddleware = createSagaMiddleware();
//------关键代码----end-----------

const composeEnhancers =   window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ ?
    window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__({}):compose
//------关键代码----start-----------
const enhancer = composeEnhancers(applyMiddleware(sagaMiddleware))
//------关键代码----end-----------

const store = createStore( reducer, enhancer) // 创建数据存储仓库
export default store   //暴露出去
```

这步完成后，就把原来的`redux-thunk`替换成`redux-saga`了，当然，现在我们还不能使用，我们还需要继续配置`sagas.js`文件。

### 创建sagas.js文件

`redux-saga`希望你把业务逻辑单独写一个文件，这里我们在`/src/store/`文件夹下建立一个`sagas.js`文件。

```js
function* mySaga() {} 
export default mySaga;
```

创建好以后直接引入到`index.js`里。

```js
import mySagas from './sagas' 
```

然后执行run方法，让`saga`运行起来。

```js
sagaMiddleware.run(mySagas)
```

为了方便你学习，这里给出`/src/store/index.js`的全部内容。

```js
import { createStore , applyMiddleware ,compose } from 'redux'  //  引入createStore方法
import reducer from './reducer'   
import createSagaMiddleware from 'redux-saga' 
import mySagas from './sagas' 

const sagaMiddleware = createSagaMiddleware();

const composeEnhancers =   window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ ?
    window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__({}):compose
const enhancer = composeEnhancers(applyMiddleware(sagaMiddleware))
const store = createStore( reducer, enhancer) 
sagaMiddleware.run(mySagas)


export default store  
```

现在已经完全替换成`redux-saga`了，所以以前在`TodoList.js`中的代码需要删除，不删除就会报错。主要删除`componentDidMount`声明周期函数里的代码。 这样`redux-saga`的安装和配置就算完成了，之后我们就可以编写中间件了。其实这个配置一般在项目中也只需要作一次，你完全可以收藏网页，然后需要时回来看一下就可以了。

#### 用Redux-saga改写Todolist

我们先来改造`TodoList.js`文件，现在`componentDidMount`里边是空的，所以我们要进行redux的基本操作，这个流程我不再多作介绍了，已经练习了10几遍了。

当然可以先引入一个`action`，当然这个action还没有，我们一会再进行编写，给它起名叫做`getMyListAction`(你可以起任何名字，记住就好，因为下面我们要不断使用)

```js
import {getMyListAction, changeInputAction , addItemAction ,deleteItemAction} from './store/actionCreatores'
```

然后顺势在`actionCreators.js`文件里把这个action创建出来。

```js
export const getMyListAction = ()=>({
    type:GET_MY_LIST
})
```

写完你会发现`GET_MY_LIST`也没有，需要先引入，再到`actionTypes.js`里进行定义

```js
import {GET_MY_LIST,CHANGE_INPUT , ADD_ITEM,DELETE_ITEM,GET_LIST}  from './actionTypes'
```

actionTypes.js文件定义`GET_MY_LIST`

```js
export const  GET_MY_LIST = 'getMyList'
```

之后就可以回到`TodoList.js`文件，编写`componentDidMount`里的内容了。

```js
componentDidMount(){
    const action =getMyListAction()
    store.dispatch(action)
}
```

测试完成

#### 编写sagas.js

 这里给出`sagas.js`的所有内容 

```js
import { takeEvery ,put } from 'redux-saga/effects'  
import {GET_MY_LIST} from './actionTypes'
import {getListAction} from './actionCreatores'
import axios from 'axios'

//generator函数
function* mySaga() {
    //等待捕获action
    yield takeEvery(GET_MY_LIST, getList)//监听GET_MY_LIST,就执行getList
}

function* getList(){
    //这段代码我就不删除了。
    // axios.get('https://www.easy-mock.com/mock/5cfcce489dc7c36bd6da2c99/xiaojiejie/getList').then((res)=>{
    //     const data = res.data
    //     const action = getListAction(data)
    //     put(action)

    // })
    const res = yield axios.get('https://www.easy-mock.com/mock/5cfcce489dc7c36bd6da2c99/xiaojiejie/getList')
    const action = getListAction(res.data)
    yield put(action)
}

export default mySaga;
```

### React-Redux

项目初始化好后，直接使用`npm`在命令行安装`React-redux`，这个网络的不同安装时间也有所不同。

```js
npm install --save react-redux
```

需要建立一个`TodoList.js`的组件。项目代码如下:

```js
import React, { Component } from 'react';
class TodoList extends Component {
    render() { 
        return ( <div>JSPang</div> );
    }
}
export default TodoList;
```

有了`TodoList.js`后，我们引入到`index.js`文件下，然后修改代码如下:

```js
import React from 'react';
import ReactDOM from 'react-dom';
import TodoList from './TodoList'
ReactDOM.render(<TodoList />, document.getElementById('root'));
```

这时候再在浏览器中预览，就会只输出一个`JSPang`的字样。虽然很丑，但是项目已经跑起来了。接下来我们编写一下`render`函数中的JSX页面（为了节省大家的时间，就不再使用`antd`了）。

```js
render() { 
    return (
        <div>
            <div><input /><button>提交</button></div>
            <ul>
                <li>JSPang</li>
            </ul>
        </div>
        );
}
```

这时候界面应该发生了一点变化,这样基本的项目我们就算初始化完成了，接下来我们按原来的`Redux`方式作一个`store`出来。

#### Redux的安装和使用(React-redux需要在redux的基础上)

先在终端中安装`Redux`包，因为是一个新项目，所以需要重新安装。

```
npm install --save redux
```

首先创建一个`store`文件夹，在`/store`下创建一个`index.js`文件,并写入下面代码：

```js
import {createStore} from 'redux'
import reducer from './reducer'

const store = createStore(reducer)

export default store
```

目前我们还没有`reducer`，所以我们要创建`reducer.js`文件，代码如下:

```js
const defalutState = {
    inputValue : 'jspang',
    list :[]
}

export default (state = defalutState,action) =>{
    return state
}
```

然后再`TodoList.js`中的构造函数`constructor`中使用。

```js
import React, { Component } from 'react';
//-----关键代码--------start
import store from './store'
//-----关键代码--------end
class TodoList extends Component {
    //-----关键代码--------start
    constructor(props){
        super(props)
        this.state = store.getState()
    }
    //-----关键代码--------end
    render() { 
        return (
            <div>
                <div>
                    //-----关键代码--------start
                    <input value={this.state.inputValue} />
                    //-----关键代码--------end
                    <button>提交</button>
                </div>
                <ul>
                    <li>JSPang</li>
                </ul>
            </div>
            );
    }
}

export default TodoList;
```

#### React-redux中的Provider和connect

##### Provider提供器

``是一个提供器，只要使用了这个组件，组件里边的其它所有组件都可以使用`store`了，这也是`React-redux`的核心组件了。有了``就可以把`/src/index.js`改写成下面的代码样式，具体解释在视频中介绍了。

```js
import React from 'react';
import ReactDOM from 'react-dom';
import TodoList from './TodoList'
//---------关键代码--------start
import { Provider } from 'react-redux'
import store from './store'
//声明一个App组件，然后这个组件用Provider进行包裹。
const App = (
    <Provider store={store}>
        <TodoList />
    </Provider>
)
//---------关键代码--------end
ReactDOM.render(App, document.getElementById('root'));
```

##### connect连接器

现在如何简单的获取`store`中数据那？先打开`TodoList.js`文件，引入`connect`，它是一个连接器（其实它就是一个方法），有了这个连接器就可以很容易的获得数据了。

```js
 import {connect} from 'react-redux'  //引入连接器
```

这时候暴露出去的就变成了`connect`了，代码如下。

```js
export default connect(xxx,null)(TodoList);
```

映射关系就是把原来的state映射成组件中的`props`属性，比如我们想映射`inputValue`就可以写成如下代码。

```js
const stateToProps = (state)=>{
    return {
        inputValue : state.inputValue
    }
}
```

这时候再把`xxx`改为`stateToProps`

```js
export default connect(stateToProps,null)(TodoList)
```

然后把``里的`state`标签，改为`props`,代码如下:

```js
 <input value={this.props.inputValue} />
```

为了方便你学习，我这里给出所有的`TodoList.js`的所有代码。

```js
import React, { Component } from 'react';
import {connect} from 'react-redux'

class TodoList extends Component {
    constructor(props){
        super(props)
    }
    render() { 
        return (
            <div>
                <div>
                    <input value={this.props.inputValue} />
                    <button>提交</button>
                </div>
                <ul>
                    <li>JSPang</li>
                </ul>
            </div>
            );
    }
}

const stateToProps = (state)=>{
    return {
        inputValue : state.inputValue
    }
}

export default connect(stateToProps,null)(TodoList);
```

### React-redux的数据修改

#### 编写onChange响应事件

打开`TodoList.js`文件，然后在``上注册`onChange`事件,这里我就偷懒直接绑定`this`了。

```jsx
 <input value={this.props.inputValue} onChange={this.inputChange.bind(this)} />
```

有了事件需要编写对应的方法,这里先写一个最简单的`inputChange`方法。

```js
inputChange(e){
    console.log(e.target.value)
}
```

#### 编写DispatchToProps

要使用`react-redux`，我们可以编写另一个映射`DispatchToProps`,先看下面这段代码，你会发现有两个参数，第二个参数我们用的是`null`。

```js
export default connect(stateToProps,null)(TodoList);
```

`DispatchToProps`就是要传递的第二个参数，通过这个参数才能改变`store`中的值。

```js
const dispatchToProps = (dispatch) =>{
    return {
        inputChange(e){
            console.log(e.target.value)
        }
    }
}
```

有了这个参数之后可以把响应事件改成下面的代码.

```js
 <input value={this.props.inputValue} onChange={this.props.inputChange} />
```

然后把connect第二个参数传递过去。

```js
export default connect(stateToProps,dispatchToProps)(TodoList);
```

这时候原来的`inputChange`方法就没用了，可以删除掉。 目前整体的代码就改为下面的样子了，我们在浏览器中预览也是可以看到效果的。此步骤成功说明映射关系支持成功。

```js
import React, { Component } from 'react';
import store from './store'
import {connect} from 'react-redux'

class TodoList extends Component {
    constructor(props){
        super(props)
        this.state = store.getState()
    }
    render() { 
        return (
            <div>
                <div>
                    <input value={this.props.inputValue} onChange={this.props.inputChange} />
                    <button>提交</button>
                </div>
                <ul>
                    <li>JSPang</li>
                </ul>
            </div>
            );
    }
}
const stateToProps = (state)=>{
    return {
        inputValue : state.inputValue
    }
}

const dispatchToProps = (dispatch) =>{
    return {
        inputChange(e){
            console.log(e.target.value)
        }
    }
}

export default connect(stateToProps,dispatchToProps)(TodoList);
```

#### 派发action到store中

映射关系已经做好了，接下来只要进行`action`的派发和`reducer`对业务逻辑的编写就可以了。派发action和以前的流程一样，我就直接给出代码了。

```js
const dispatchToProps = (dispatch) =>{
    return {
        inputChange(e){
            let action = {
                type:'change_input',
                value:e.target.value
            }
            dispatch(action)
        }
    }
}
```

派发后就需求在`reducer`里边，编写对应的业务逻辑了。

```js
const defalutState = {
    inputValue : 'jspang',
    list :[]
}
export default (state = defalutState,action) =>{
    if(action.type === 'change_input'){
        let newState = JSON.parse(JSON.stringify(state))
        newState.inputValue = action.value
        return newState
    }
    return state
}
```

### React-redux增加List数据

直接在`/src/TodoList.js`里的`Button`增加一个`onClick`事件，代码如下:

```js
<button onClick={this.props.clickButton}>提交</button>
```

注意这里依然使用的`props`，也就是说还需要把方法写在`dispatchToProps`里。我们这里先写一个测试，看看是否绑定上了。

```js
const dispatchToProps = (dispatch) =>{
    return {
        inputChange(e){
            let action = {
                type:'change_input',
                value:e.target.value
            }
            dispatch(action)
        },
        clickButton(){
            console.log('111111111')
        }
    }
}
```

写完`clickButton`方法后，在浏览器中预览，打开浏览器的控制台看一下结果，应该在点击时，可以看到显示`111111111`。 这步完成，就是用`dispatch`派发`action`了。

```js
clickButton(){
    let action = { type:'add_item' }
    dispatch(action)
}
```

#### 编写Reducer的业务逻辑

派发完成后,到`Reducer`编写业务逻辑，这一步和一起的操作基本一样。

```js
const defalutState = {
    inputValue : 'jspang',
    list :[]
}

export default (state = defalutState,action) =>{
    if(action.type === 'change_input'){
        let newState = JSON.parse(JSON.stringify(state))
        newState.inputValue = action.value
        return newState
    }
    //----关键代码------start---------
    if(action.type === 'add_item'){
        let newState = JSON.parse(JSON.stringify(state))
        newState.list.push(newState.inputValue)
        newState.inputValue = ''
        return newState
    }
    //----关键代码------end---------
    return state
}
```

#### 页面UI

这步完成后，我们到`TodoList.js`中进行`JSX`部分的编写，编写前需要先把`stateToProps`的映射关系做好。

```js
const stateToProps = (state)=>{
    return {
        inputValue : state.inputValue,
        list:state.list
    }
}
```

有了映射关系，就可以再界面中用属性的方式，进行显示，代码如下：

```js
<ul>
    {
        this.props.list.map((item,index)=>{
            return (<li key={index}>{item}</li>)
        })
    }
</ul>
```

这样就实现了增加`TodoList`的列表项，这里给出`TodoList.js`的代码，方便学习使用.

```js
import React, { Component } from 'react';
import store from './store'
import {connect} from 'react-redux'

class TodoList extends Component {
    constructor(props){
        super(props)
        this.state = store.getState()
    }
    render() { 
        return (
            <div>
                <div>
                    <input value={this.props.inputValue} onChange={this.props.inputChange} />
                    <button onClick={this.props.clickButton}>提交</button>
                </div>
                <ul>
                    {
                        this.props.list.map((item,index)=>{
                            return (<li key={index}>{item}</li>)
                        })
                    }
                </ul>
            </div>
        );
    }
}
const stateToProps = (state)=>{
    return {
        inputValue : state.inputValue,
        list:state.list
    }
}

const dispatchToProps = (dispatch) =>{
    return {
        inputChange(e){
            let action = {
                type:'change_input',
                value:e.target.value
            }
            dispatch(action)
        },
        clickButton(){
            let action = {
                type:'add_item'
            }
            dispatch(action)
        }
    }
}
export default connect(stateToProps,dispatchToProps)(TodoList);
```

### 程序优化

##### 删除重复代码

现在代码中有好几处`this.props`都是重复的，这时候就可以用`javascript`的解构赋值方法，来精简代码。修改`TodoList.js`中的`Render`函数，把原来带代码修改为下面的代码:

```jsx
    render() { 
        let {inputValue ,inputChange,clickButton,list} = this.props;
        return (
            <div>
                <div>
                    <input value={inputValue} onChange={inputChange} />
                    <button onClick={clickButton}>提交</button>
                </div>
                <ul>
                    {
                        list.map((item,index)=>{
                            return (<li key={index}>{item}</li>)
                        })
                    }
                </ul>
            </div>
        );
    }
```

#### 将ToDoList改为UI组件

可以看到，现在的`TodoList`组件里没有任何的业务逻辑，只有一个`Render`方法，这时候就可以把它改为UI组件(无状态组件)，UI组件就是一个方法，减少很多冗余操作，从而提高程序运行性能。这时候重新声明一个`TodoList`的变量，然后把render函数里的东西复制过来，只要稍加修改，就可以得到下面的代码：

```js
const TodoList =(props)=>{
    let {inputValue ,inputChange,clickButton,list} = props; // 粘贴过来后，此处要进行修改
    return (
        <div>
            <div>
                <input value={inputValue} onChange={inputChange} />
                <button onClick={clickButton}>提交</button>
            </div>
            <ul>
                {
                    list.map((item,index)=>{
                        return (<li key={index}>{item}</li>)
                    })
                }
            </ul>
        </div>
    );
}
```

代码写完后，我们删除一些不用的引入，然后就可以到浏览器中进行预览了。

```js
import React from 'react';
import {connect} from 'react-redux'
```

为了更好的学习，我在这里给出目前`TodoList.js`的所有代码。

```js
import React from 'react';
import {connect} from 'react-redux'


const TodoList =(props)=>{
    let {inputValue ,inputChange,clickButton,list} = props; // 粘贴过来后，此处要进行修改
    return (
        <div>
            <div>
                <input value={inputValue} onChange={inputChange} />
                <button onClick={clickButton}>提交</button>
            </div>
            <ul>
                {
                    list.map((item,index)=>{
                        return (<li key={index}>{item}</li>)
                    })
                }
            </ul>
        </div>
    );
}



const stateToProps = (state)=>{
    return {
        inputValue : state.inputValue,
        list:state.list
    }
}

const dispatchToProps = (dispatch) =>{
    return {
        inputChange(e){
            let action = {
                type:'change_input',
                value:e.target.value
            }
            dispatch(action)
        },
        clickButton(){
            let action = {
                type:'add_item'
            }
            dispatch(action)
        }
    }
}
export default connect(stateToProps,dispatchToProps)(TodoList);
```

那我们反过来，再来理解一下最后一句话代码的意思。

```js
export default connect(stateToProps,dispatchToProps)(TodoList);
```

`connect`的作用是把UI组件（无状态组件）和业务逻辑代码的分开，然后通过connect再链接到一起，让代码更加清晰和易于维护。这也是`React-Redux`最大的有点。