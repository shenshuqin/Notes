## React Hooks

博客: https://jspang.com/detailed?id=50#toc313 

2018年底FaceBook的React小组推出Hooks以来，所有的React的开发者都对它大为赞赏。`React Hooks`就是用函数的形式代替原来的继承类的形式，并且使用预函数的形式管理`state`，有Hooks可以不再使用类的形式定义组件了。这时候你的认知也要发生变化了，原来把组件分为有状态组件和无状态组件，有状态组件用类的形式声明，无状态组件用函数的形式声明。那现在所有的组件都可以用函数来声明了。 

### 创建

```shell
D: // 进入D盘
mkdir ReactHooksDemo
cd ReactHooksDemo
create-react-app demo01
```

只留`/src/index.js`文件，然后把里边的代码删减成下面的样子:

```js
import React from 'react';
import ReactDOM from 'react-dom';

ReactDOM.render(<App />, document.getElementById('root'));
```

### React Hooks编写形式对比

原始写法:

```js
import React, { Component } from 'react';

class Example extends Component {
    constructor(props) {
        super(props);
        this.state = { count:0 }
    }
    render() { 
        return (
            <div>
                <p>You clicked {this.state.count} times</p>
                <button onClick={this.addCount.bind(this)}>Chlick me</button>
            </div>
        );
    }
    addCount(){
        this.setState({count:this.state.count+1})
    }
}

export default Example;
```

React Hooks 写法：

```js
import React, { useState } from 'react';
function Example(){
    const [ count , setCount ] = useState(0);
    return (
        <div>
            <p>You clicked {count} times</p>
            <button onClick={()=>{setCount(count+1)}}>click me</button>
        </div>
    )
}
export default Example;
```

从这两个程序的对比上可以看出Hooks本质上就是一类特殊的函数，他们可以为你的函数型组件（function component）注入一些特殊的功能。这听起来有点像以前React中的`Mixins`差不多哦。其实是由很多不同，hooks的目的就是让你不再写`class`，让`function`一统江湖。

### useState的介绍和多状态声明

#### useState的介绍

 `useState`是react自带的一个hook函数，它的作用是用来声明状态变量。 

那我们从三个方面来看`useState`的用法，分别是声明、读取、使用（修改）。这三个方面掌握了，你基本也就会使用`useState`了.

先来看一下声明的方式，上节课的代码如下：

```js
const [ count , setCount ] = useState(0);
```

这种方法是ES6语法中的数组解构，这样看起来代码变的简单易懂。现在ES6的语法已经在工作中频繁使用，所以如果你对ES6的语法还不熟悉，我觉的有必要拿出2天时间学习一下。 如果不写成数组解构，上边的语法要写成下面的三行:

```js
let _useState = userState(0)
let count = _useState[0]
let setCount = _useState[1]
```

`useState`这个函数接收的参数是状态的初始值(Initial state)，它返回一个数组，这个数组的第0位是当前的状态值，第1位是可以改变状态值的方法函数。 所以上面的代码的意思就是声明了一个状态变量为count，并把它的初始值设为0，同时提供了一个可以改变`count`的状态值的方法函数。

这时候你已经会声明一个状态了，接下来我们看看如何读取状态中的值。

```js
<p>You clicked {count} times</p>
```

你可以发现，我们读取是很简单的。只要使用`{count}`就可以，因为这时候的count就是JS里的一个变量，想在`JSX`中使用，值用加上`{}`就可以。

最后看看如果改变`State`中的值,看下面的代码:

```js
<button onClick={()=>{setCount(count+1)}}>click me</button>
```

直接调用setCount函数，这个函数接收的参数是修改过的新状态值。接下来的事情就交给`React`,他会重新渲染组件。`React`自动帮助我们记忆了组件的上一次状态值，但是这种记忆也给我们带来了一点小麻烦，但是这种麻烦你可以看成规则，只要准守规则，就可以愉快的进行编码。

#### 多状态声明的注意事项

比如现在我们要声明多个状态，有年龄（age）、性别(sex)和工作(work)。代码可以这么写.

```js
import React, { useState } from 'react';
function Example2(){
    const [ age , setAge ] = useState(18)
    const [ sex , setSex ] = useState('男')
    const [ work , setWork ] = useState('前端程序员')
    return (
        <div>
            <p>JSPang 今年:{age}岁</p>
            <p>性别:{sex}</p>
            <p>工作是:{work}</p>

        </div>
    )
}
export default Example2;
```

其实细心的小伙伴一定可以发现，在使用`useState`的时候只赋了初始值，并没有绑定任何的`key`,那React是怎么保证这三个useState找到它自己对应的state呢？

**答案是：React是根据useState出现的顺序来确定的**

比如我们把代码改成下面的样子：

```js
import React, { useState } from 'react';

let showSex = true
function Example2(){
    const [ age , setAge ] = useState(18)
    if(showSex){
        const [ sex , setSex ] = useState('男')
        showSex=false
    }

    const [ work , setWork ] = useState('前端程序员')
    return (
        <div>
            <p>JSPang 今年:{age}岁</p>
            <p>性别:{sex}</p>
            <p>工作是:{work}</p>

        </div>
    )
}
export default Example2;
```

这时候控制台就会直接给我们报错，错误如下：

```shell
 React Hook "useState" is called conditionally. React Hooks must be called in the exact same order in every component render 
```

意思就是useState不能在`if...else...`这样的条件语句中进行调用，必须要按照相同的顺序进行渲染。如果你还是不理解，你可以记住这样一句话就可以了：**就是React Hooks不能出现在条件判断语句中，因为它必须有完全一样的渲染顺序**。

### useEffect代替常用的生命周期

#### 用class的方式为计数器增加生命周期函数

为了让你更好的理解`useEffect`的使用，先用原始的方式把计数器的Demo增加两个生命周期函数`componentDidMount`和`componentDidUpdate`。分别在组件第一次渲染后在浏览器控制台打印出计数器结果和在每次计数器状态发生变化后打印出结果。代码如下：

```js
import React, { Component } from 'react';

class Example3 extends Component {
    constructor(props) {
        super(props);
        this.state = { count:0 }
    }


    componentDidMount(){
        console.log(`ComponentDidMount=>You clicked ${this.state.count} times`)
    }
    componentDidUpdate(){
        console.log(`componentDidUpdate=>You clicked ${this.state.count} times`)
    }

    render() { 
        return (
            <div>
                <p>You clicked {this.state.count} times</p>
                <button onClick={this.addCount.bind(this)}>Chlick me</button>
            </div>
        );
    }
    addCount(){
        this.setState({count:this.state.count+1})
    }
}

export default Example3;
```

这就是在不使用Hooks情况下的写法，那如何用Hooks来代替这段代码，并产生一样的效果那。

#### 用useEffect的方式为计数器增加生命周期函数

在使用`React Hooks`的情况下，我们可以使用下面的代码来完成上边代码的生命周期效果，代码如下（修改了以前的diamond）： 记得要先引入`useEffect`后，才可以正常使用。

```js
import React, { useState , useEffect } from 'react';
function Example(){
    const [ count , setCount ] = useState(0);
    //---关键代码---------start-------
    useEffect(()=>{
        console.log(`useEffect=>You clicked ${count} times`)
    })
    //---关键代码---------end-------

    return (
        <div>
            <p>You clicked {count} times</p>
            <button onClick={()=>{setCount(count+1)}}>click me</button>
        </div>
    )
}
export default Example;
```

写完后，可以到浏览器中进行预览一下，可以看出跟`class`形式的生命周期函数是完全一样的，这代表第一次组件渲染和每次组件更新都会执行这个函数。 那这段代码逻辑是什么？我们梳理一下:首先，我们生命了一个状态变量`count`,将它的初始值设为0，然后我们告诉react，我们的这个组件有一个副作用。给`useEffecthook`传了一个匿名函数，这个匿名函数就是我们的副作用。在这里我们打印了一句话，当然你也可以手动的去修改一个`DOM`元素。当React要渲染组件时，它会记住用到的副作用，然后执行一次。等Reat更新了State状态时，它再一词执行定义的副作用函数。

#### useEffect注意点

1. React首次渲染和之后的每次渲染都会调用一遍`useEffect`函数，而之前我们要用两个生命周期函数分别表示首次渲染(componentDidMonut)和更新导致的重新渲染(componentDidUpdate)。
2. useEffect中定义的函数的执行不会阻碍浏览器更新视图，也就是说这些函数时异步执行的，而`componentDidMonut`和`componentDidUpdate`中的代码都是同步执行的。个人认为这个有好处也有坏处吧，比如我们要根据页面的大小，然后绘制当前弹出窗口的大小，如果时异步的就不好操作了。

### useEffect实现componentWillUnmount生命周期

 在写React应用的时候，在组件中经常用到`componentWillUnmount`生命周期函数（组件将要被卸载时执行）。比如我们的定时器要清空，避免发生内存泄漏;比如登录状态要取消掉，避免下次进入信息出错。所以这个生命周期函数也是必不可少的，这节课就来用`useEffect`来实现这个生命周期函数,并讲解一下`useEffect`容易踩的坑。 

#### useEffect解绑副作用

学习`React Hooks` 时，我们要改掉生命周期函数的概念（人往往有先入为主的毛病，所以很难改掉），因为`Hooks`叫它副作用，所以`componentWillUnmount`也可以理解成解绑副作用。这里为了演示用`useEffect`来实现类似`componentWillUnmount`效果，先安装`React-Router`路由,进入项目根本录，使用`npm`进行安装。

```shell
npm install --save react-router-dom
```

然后打开`Example.js`文件，进行改写代码，先引入对应的`React-Router`组件。

```js
import { BrowserRouter as Router, Route, Link } from "react-router-dom"
```

在文件中编写两个新组件，因为这两个组件都非常的简单，所以就不单独建立一个新的文件来写了。

```js
function Index() {
    return <h2>JSPang.com</h2>;
}

function List() {
    return <h2>List-Page</h2>;
}
```

有了这两个组件后，接下来可以编写路由配置，在以前的计数器代码中直接增加就可以了。

```js
return (
    <div>
        <p>You clicked {count} times</p>
        <button onClick={()=>{setCount(count+1)}}>click me</button>

        <Router>
            <ul>
                <li> <Link to="/">首页</Link> </li>
                <li><Link to="/list/">列表</Link> </li>
            </ul>
            <Route path="/" exact component={Index} />
            <Route path="/list/" component={List} />
        </Router>
    </div>
)
```

然后到浏览器中查看一下，看看组件和路由是否可用。如果可用，我们现在可以调整`useEffect`了。在两个新组件中分别加入`useEffect()`函数:

```js
function Index() {
    useEffect(()=>{
        console.log('useEffect=>老弟，你来了！Index页面')
        )
    return <h2>JSPang.com</h2>;
}

function List() {
    useEffect(()=>{
        console.log('useEffect=>老弟，你来了！List页面')
    })

    return <h2>List-Page</h2>;
}
```

这时候我们点击`Link`进入任何一个组件，在浏览器中都会打印出对应的一段话。这时候可以用**返回一个函数的形式进行解绑**，代码如下：

```js
function Index() {
    useEffect(()=>{
        console.log('useEffect=>老弟你来了！Index页面')
        return ()=>{
            console.log('老弟，你走了!Index页面')
        }
    })
    return <h2>JSPang.com</h2>;
  }
```

这时候你在浏览器中预览，我们仿佛实现了`componentWillUnmount`方法。但这只是好像实现了，当点击计数器按钮时，你会发现`老弟，你走了!Index页面`，也出现了。这到底是怎么回事那？其实每次状态发生变化，`useEffect`都进行了解绑。

#### useEffect的第二个参数

那到底要如何实现类似`componentWillUnmount`的效果那?这就需要请出`useEffect`的第二个参数，它是一个数组，数组中可以写入很多状态对应的变量，意思是当状态值发生变化时，我们才进行解绑。但是当传空数组`[]`时，就是当组件将被销毁时才进行解绑，这也就实现了`componentWillUnmount`的生命周期函数。

```js
function Index() {
    useEffect(()=>{
        console.log('useEffect=>老弟你来了！Index页面')
        return ()=>{
            console.log('老弟，你走了!Index页面')
        }
    },[])
    return <h2>JSPang.com</h2>;
}
```

为了更加深入了解第二个参数的作用，把计数器的代码也加上`useEffect`和解绑方法，并加入第二个参数为空数组。代码如下：

```js
function Example(){
    const [ count , setCount ] = useState(0);

    useEffect(()=>{
        console.log(`useEffect=>You clicked ${count} times`)

        return ()=>{
            console.log('====================')
        }
    },[])

    return (
        <div>
            <p>You clicked {count} times</p>
            <button onClick={()=>{setCount(count+1)}}>click me</button>

            <Router>
                <ul>
                    <li> <Link to="/">首页</Link> </li>
                    <li><Link to="/list/">列表</Link> </li>
                </ul>
                <Route path="/" exact component={Index} />
                <Route path="/list/" component={List} />
            </Router>
        </div>
    )
}
```

这时候的代码是不能执行解绑副作用函数的。但是如果我们想每次`count`发生变化，我们都进行解绑，只需要在第二个参数的数组里加入`count`变量就可以了。代码如下：

```js
function Example(){
    const [ count , setCount ] = useState(0);

    useEffect(()=>{
        console.log(`useEffect=>You clicked ${count} times`)

        return ()=>{
            console.log('====================')
        }
    },[count])

    return (
        <div>
            <p>You clicked {count} times</p>
            <button onClick={()=>{setCount(count+1)}}>click me</button>

            <Router>
                <ul>
                    <li> <Link to="/">首页</Link> </li>
                    <li><Link to="/list/">列表</Link> </li>
                </ul>
                <Route path="/" exact component={Index} />
                <Route path="/list/" component={List} />
            </Router>
        </div>
    )
}
```

这时候只要`count`状态发生变化，都会执行解绑副作用函数，浏览器的控制台也就打印出了一串`=================`。

### useContext父子组件传值

#### 1.createContext函数创建context

直接在`src`目录下新建一个文件`Example4.js`,然后拷贝`Example.js`里的代码，并进行修改，删除路由部分和副作用的代码，只留计数器的核心代码就可以了。

```js
import React, { useState , useEffect } from 'react';

function Example4(){
    const [ count , setCount ] = useState(0);
    return (
        <div>
            <p>You clicked {count} times</p>
            <button onClick={()=>{setCount(count+1)}}>click me</button>
        </div>
    )
}
export default Example4;
```

然后修改一下`index.js`让它渲染这个`Example4.js`组件，修改的代码如下。

```js
import React from 'react';
import ReactDOM from 'react-dom';
import Example from './Example4'
ReactDOM.render(<Example />, document.getElementById('root'));
```

之后在`Example4.js`中引入`createContext`函数，并使用得到一个组件，然后在`return`方法中进行使用。先看代码，然后我再解释。

```js
import React, { useState , createContext } from 'react';
//===关键代码
const CountContext = createContext()

function Example4(){
    const [ count , setCount ] = useState(0);

    return (
        <div>
            <p>You clicked {count} times</p>
            <button onClick={()=>{setCount(count+1)}}>click me</button>
            {/*======关键代码 */}
            <CountContext.Provider value={count}>
            </CountContext.Provider>

        </div>
    )
}
export default Example4;
```

这段代码就相当于把`count`变量允许跨层级实现传递和使用了（也就是实现了上下文），当父组件的`count`变量发生变化时，子组件也会发生变化。接下来我们就看看一个`React Hooks`的组件如何接收到这个变量。

#### 2.useContext接收上下文变量

已经有了上下文变量，剩下的就时如何接收了，接收这个直接使用useContext就可以，但是在使用前需要新进行引入`useContext`（不引入是没办法使用的）。

```js
import React, { useState , createContext , useContext } from 'react';
```

引入后写一个`Counter`组件，只是显示上下文中的`count`变量代码如下：

```js
function Counter(){
    const count = useContext(CountContext)  //一句话就可以得到count
    return (<h2>{count}</h2>)
}
```

得到后就可以显示出来了，但是要记得在``的闭合标签中,代码如下。

```js
<CountContext.Provider value={count}>
    <Counter />
</CountContext.Provider>
```

### useReducer的使用----类似于Redux

了解reducer的含义后，就可以讲useReducer了，它也是React hooks提供的函数，可以增强我们的`Reducer`，实现类似Redux的功能。我们新建一个`Example5.js`的文件，然后用useReducer实现计数器的加减双向操作。（此部分代码的介绍可以看视频来学习）

```js
import React, { useReducer } from 'react';

function ReducerDemo(){
    const [ count , dispatch ] =useReducer((state,action)=>{//第一个参数为返回值
        switch(action){
            case 'add':
                return state+1
            case 'sub':
                return state-1
            default:
                return state
        }
    },0)
    return (
       <div>
           <h2>现在的分数是{count}</h2>
           <button onClick={()=>dispatch('add')}>Increment</button>
           <button onClick={()=>dispatch('sub')}>Decrement</button>
       </div>
    )

}

export default ReducerDemo
```

这段代码是useReducer的最简单实现了，这时候可以在浏览器中实现了计数器的增加减少。

修改`index.js`文件，让`ReducerDemo`组件起作用。

```js
import React from 'react';
import ReactDOM from 'react-dom';
import Example from './Example5'


ReactDOM.render(<Example />, document.getElementById('root'));
```

### useRedux代替Redux

#### 编写UI组件

既然是一个实例，就需要有些界面的东西，小伙伴们不要觉的烦。在`/src`目录下新建一个文件夹`Example6`，有了文件夹后，在文件夹下面建立一个`showArea.js`文件。代码如下：

```js
import React from 'react';
function ShowArea(){

    return (<div style={{color:'blue'}}>字体颜色为blue</div>)

}
export default ShowArea
```

显示区域写完后，新建一个`Buttons.js`文件，用来编写按钮，这个是两个按钮，一个红色一个黄色。先不写其他任何业务逻辑。

```js
import React from 'react';

function Buttons(){
    return (
        <div>
            <button>红色</button>
            <button>黄色</button>
        </div>
    )
}

export default Buttons
```

然后再编写一个组合他们的`Example6.js`组件，引入两个新编写的组件`ShowArea`和`Buttons`，并用``标签给包裹起来。

```js
import React, { useReducer } from 'react';
import ShowArea from './ShowArea';
import Buttons from './Buttons';


function Example6(){
    return (
        <div>
                <ShowArea />
                <Buttons />
        </div>
    )
}

export default Example6
```

这步做完，需要到`/src`目录下的`index.js`中引入一下`Example6.js`文件，引入后React才能正确渲染出刚写的UI组件。

```js
import React from 'react';
import ReactDOM from 'react-dom';
import Example from './Example6/Example6'


ReactDOM.render(<Example />, document.getElementById('root'));
```

做完这步可以简单的预览一下UI效果，虽然很丑，但是只要能满足学习需求就可以了。我们虽然都是前端，但是在学习时没必要追求漂亮的页面，关键时把知识点弄明白。我们写这么多文件，也就是要为接下来的知识点服务，其实这些组件都是陪衬罢了。

#### 编写颜色共享组件color.js

有了UI组件后，就可以写一些业务逻辑了，这节课我们先实现状态共享，这个就是利用`useContext`。建立一个`color.js`文件，然后写入下面的代码。

```js
import React, { createContext } from 'react';

export const ColorContext = createContext({})

export const Color = props=>{
    return (
        <ColorContext.Provider value={{color:"blue"}}>
            {props.children}
        </ColorContext.Provider>
    )
}
```

代码中引入了`createContext`用来创建共享上下文`ColorContext`组件，然后我们要用`{props.children}`来显示对应的子组件。详细解释我在视频中讲解吧。

有了这个组件后，我们就可以把`Example6.js`进行改写，让她可以共享状态。

```js
import React, { useReducer } from 'react';
import ShowArea from './ShowArea';
import Buttons from './Buttons';
import { Color } from './color';   //引入Color组件

function Example6(){
    return (
        <div>
            <Color>
                <ShowArea />
                <Buttons />
            </Color>

        </div>
    )
}

export default Example6
```

然后再改写`showArea.js`文件，我们会引入`useContext`和在`color.js`中声明的`ColorContext`，让组件可以接收全局变量。

```js
import React , { useContext } from 'react';
import { ColorContext } from './color';

function ShowArea(){
    const {color} = useContext(ColorContext)
    return (<div style={{color:color}}>字体颜色为{color}</div>)

}

export default ShowArea
```

这时候就通过`useContext`实现了状态的共享，可以到浏览器中看一下效果。然后我们下节课再实现复杂逻辑状态的变化。

#### 在color.js中添加Reducer

颜色（state）管理的代码我们都放在了`color.js`中，所以在文件里添加一个reducer，用于处理颜色更新的逻辑。先声明一个reducer的函数，它就是JavaScript中的普通函数，在讲`useReducer`的时候已经详细讲过了。有了reducer后，在Color组件里使用`useReducer`,这样Color组件就有了那个共享状态和处理业务逻辑的能力，跟以前使用的`Redux`几乎一样了。之后修改一下共享状态。我们来看代码：

```js
import React, { createContext,useReducer } from 'react';

export const ColorContext = createContext({})

export const UPDATE_COLOR = "UPDATE_COLOR"

const reducer= (state,action)=>{
    switch(action.type){
        case UPDATE_COLOR:
            return action.color
        default:
            return state
    }
}


export const Color = props=>{
    const [color,dispatch]=useReducer(reducer,'blue')
    return (
        <ColorContext.Provider value={{color,dispatch}}>
            {props.children}
        </ColorContext.Provider>
    )
}
```

注意，这时候我们共享出去的状态变成了color和dispatch,如果不共享出去dispatch，你是没办法完成按钮的相应事件的。

#### 通过Dispatch修改状态

目前程序已经有了处理共享状态的业务逻辑能力，接下来就可以在`buttons.js`使用`dispatch`来完成按钮的相应操作了。先引入`useContext`、`ColorContext`和`UPDATE_COLOR`，然后写`onClick`事件就可以了。代码如下:

```js
import React ,{useContext} from 'react';
import {ColorContext,UPDATE_COLOR} from './color'

function Buttons(){
    const { dispatch } = useContext(ColorContext)
    return (
        <div>
            <button onClick={()=>{dispatch({type:UPDATE_COLOR,color:"red"})}}>红色</button>
            <button onClick={()=>{dispatch({type:UPDATE_COLOR,color:"yellow"})}}>黄色</button>
        </div>
    )
}

export default Buttons
```

这样代码就编写完成了，用`useContext`和`useReducer`实现了Redux的效果，这个代码编写过程比Redux要简单，但是也是有一定难度的。希望第一次接触的小伙伴能自己动手写5遍以上，把这种模式掌握好。

## useMemo

下面我们通过一个实例，来理解下 useMemo的用法。

- 父组件

```
function App() {
  const [name, setName] = useState('名称')
  const [content,setContent] = useState('内容')
  return (
      <>
        <button onClick={() => setName(new Date().getTime())}>name</button>
        <button onClick={() => setContent(new Date().getTime())}>content</button>
        <Button name={name}>{content}</Button>
      </>
  )
}
```

- 子组件

```
function Button({ name, children }) {
  function changeName(name) {
    console.log('11')
    return name + '改变name的方法'
  }

  const otherName =  changeName(name)
  return (
      <>
        <div>{otherName}</div>
        <div>{children}</div>
      </>

  )
}
```

熟悉react的同学可以很显然的看出，当我们点击父组件的按钮的时候，子组件的name和children都会发生变化。

> 注意我们打印console.log的方法。

不管我们是改变name或者content的值，我们发现 changeName的方法都会被调用。
是不是意味着 我们本来只想修改content的值，但是由于name并没有发生变化，所以无需执行对应的changeName方法。但是发现他却执行了。 这是不是就意味着性能的损耗，做了无用功。

下面我们使用useMemo进行优化

- 优化之后的子组件

```
function Button({ name, children }) {
  function changeName(name) {
    console.log('11')
    return name + '改变name的方法'
  }

const otherName =  useMemo(()=>changeName(name),[name])
  return (
      <>
        <div>{otherName}</div>
        <div>{children}</div>
      </>

  )
}

export default Button
```

这个时候我们点击 改变content值的按钮，发现changeName 并没有被调用。
但是点击改变name值按钮的时候，changeName被调用了。
所以我们可以使用useMemo方法 避免无用方法的调用，当然一般我们changName里面可能会使用useState来改变state的值，那是不是就避免了组件的二次渲染。
达到了优化性能的目的

### useRef获取DOM和保存变量

`useRef`在工作中虽然用的不多，但是也不能缺少。它有两个主要的作用:

- 用`useRef`获取React JSX中的DOM元素，获取后你就可以控制DOM的任何东西了。但是一般不建议这样来作，React界面的变化可以通过状态来控制。
- 用`useRef`来保存变量，这个在工作中也很少能用到，我们有了`useContext`这样的保存其实意义不大，但是这是学习，也要把这个特性讲一下。

#### 获取dom

在`/src`文件夹下新建一个`Example8.js`文件，然后先引入useRef，编写业务逻辑代码如下:

```js
import React, { useRef} from 'react';
function Example8(){
    const inputEl = useRef(null)
    const onButtonClick=()=>{ 
        inputEl.current.value="Hello ,JSPang"
        console.log(inputEl) //输出获取到的DOM节点
    }
    return (
        <>
            {/*保存input的ref到inputEl */}
            <input ref={inputEl} type="text"/>
            <button onClick = {onButtonClick}>在input上展示文字</button>
        </>
    )
}
export default Example8
```

当点击按钮时，你可以看到在浏览器中的控制台完整的打印出了DOM的所有东西，并且界面上的``框的value值也输出了我们写好的`Hello ,JSPang`。这一切说明我们可以使用useRef获取DOM元素，并且可以通过useRefu控制DOM的属性和值。

#### 保存普通变量

先用`useState`声明了一个`text`状态和`setText`函数。然后编写界面，界面就是一个文本框。然后输入的时候不断变化。

```js
import React, { useRef ,useState,useEffect } from 'react';



function Example8(){
    const inputEl = useRef(null)
    const onButtonClick=()=>{ 
        inputEl.current.value="Hello ,useRef"
        console.log(inputEl)
    }
    const [text, setText] = useState('jspang')
    return (
        <>
            {/*保存input的ref到inputEl */}
            <input ref={inputEl} type="text"/>
            <button onClick = {onButtonClick}>在input上展示文字</button>
            <br/>
            <br/>
            <input value={text} onChange={(e)=>{setText(e.target.value)}} />

        </>
    )
}

export default Example8
```

这时想每次`text`发生状态改变，保存到一个变量中或者说是`useRef`中，这时候就可以使用`useRef`了。先声明一个`textRef`变量，他其实就是`useRef`函数。然后使用`useEffect`函数实现每次状态变化都进行变量修改，并打印。最后的全部代码如下。

```js
import React, { useRef ,useState,useEffect } from 'react';
function Example8(){
    const inputEl = useRef(null)
    const onButtonClick=()=>{ 
        inputEl.current.value="Hello ,useRef"
        console.log(inputEl)
    }
    //-----------关键代码--------start
    const [text, setText] = useState('jspang')
    const textRef = useRef()

    useEffect(()=>{
        textRef.current = text;
        console.log('textRef.current:', textRef.current)
    })
    //----------关键代码--------------end
    return (
        <>
            {/*保存input的ref到inputEl */}
            <input ref={inputEl} type="text"/>
            <button onClick = {onButtonClick}>在input上展示文字</button>
            <br/>
            <br/>
            <input value={text} onChange={(e)=>{setText(e.target.value)}} />
        </>
    )
}

export default Example8
```

这时候就可以实现每次状态修改，同时保存到`useRef`中了。也就是我们说的保存变量的功能。那`useRef`的主要功能就是获得DOM和变量保存，我们都已经讲过了。你的编码能力有增加了一些，让我们一起加油。

### 自定义hooks函数

#### 编写自定义函数

先用`useState`声明了一个`text`状态和`setText`函数。然后编写界面，界面就是一个文本框。然后输入的时候不断变化。

```js
import React, { useRef ,useState,useEffect } from 'react';



function Example8(){
    const inputEl = useRef(null)
    const onButtonClick=()=>{ 
        inputEl.current.value="Hello ,useRef"
        console.log(inputEl)
    }
    const [text, setText] = useState('jspang')
    return (
        <>
            {/*保存input的ref到inputEl */}
            <input ref={inputEl} type="text"/>
            <button onClick = {onButtonClick}>在input上展示文字</button>
            <br/>
            <br/>
            <input value={text} onChange={(e)=>{setText(e.target.value)}} />

        </>
    )
}

export default Example8
```

这时想每次`text`发生状态改变，保存到一个变量中或者说是`useRef`中，这时候就可以使用`useRef`了。先声明一个`textRef`变量，他其实就是`useRef`函数。然后使用`useEffect`函数实现每次状态变化都进行变量修改，并打印。最后的全部代码如下。

```js
import React, { useRef ,useState,useEffect } from 'react';
function Example8(){
    const inputEl = useRef(null)
    const onButtonClick=()=>{ 
        inputEl.current.value="Hello ,useRef"
        console.log(inputEl)
    }
    //-----------关键代码--------start
    const [text, setText] = useState('jspang')
    const textRef = useRef()

    useEffect(()=>{
        textRef.current = text;
        console.log('textRef.current:', textRef.current)
    })
    //----------关键代码--------------end
    return (
        <>
            {/*保存input的ref到inputEl */}
            <input ref={inputEl} type="text"/>
            <button onClick = {onButtonClick}>在input上展示文字</button>
            <br/>
            <br/>
            <input value={text} onChange={(e)=>{setText(e.target.value)}} />
        </>
    )
}

export default Example8
```

这时候就可以实现每次状态修改，同时保存到`useRef`中了。也就是我们说的保存变量的功能。那`useRef`的主要功能就是获得DOM和变量保存，我们都已经讲过了。你的编码能力有增加了一些，让我们一起加油。

#### 编写组件并使用自定义函数

自定义`Hooks`函数已经写好了，可以直接进行使用，用法和`JavaScript`的普通函数用起来是一样的。直接在`Example9`组件使用`useWinSize`并把结果实时展示在页面上。

```js
function Example9(){

    const size = useWinSize()
    return (
        <div>页面Size:{size.width}x{size.height}</div>
    )
}

export default Example9 
```

之后就可以在浏览器中预览一下结果，可以看到当我们放大缩小浏览器窗口时，页面上的结果都会跟着进行变化。说明自定义的函数起到了作用。