## React应用CSS

### module.css

1.创建文件夹index.module.css

```css
.text{ //不能多个命名
  color:red;
}

```

2.组件中

```js
import React from 'react';
import logo from './logo.svg';
import './App.css';
import styles from "./index.module.css";
import Button from 'antd/lib/button'
import 'antd/dist/antd.css'
function App() {
  return (
    <div className="App">
      <p className={styles.text}>哈哈哈</p>
      <Button type="primary">按钮</Button>
    </div>
  );
}

export default App;
```

### Sass

1.首先安装node-sass

` npm install node-sass --sass_binary_site=https://npm.taobao.org/mirrors/node-sass/ `

2.index.scss

```css
$App:'App';
.#{$App}{
    color:blue
}

.btn-common {
    //border: 1px red solid;
    button {
      border: none;
      background: none;
      outline: none;
      border: 1px red solid;
    }
}
```

3.组件

```js
import React from 'react';
import logo from './logo.svg';
import './App.css';
import styles from "./index.module.css";
import './app.scss'
import Button from 'antd/lib/button'
import 'antd/dist/antd.css'
function App() {
  return (
    <div className="App">
      <p className={styles.app}>哈哈哈</p>
      <span className="App">22222</span>
      <Button type="primary">按钮</Button>
      <div className="btn-common">
              <button>这是btn组件</button>
            </div>
    </div>
  );
}

export default App;

```

###  创建style对象 

```css

class App extends React.Component {
 
//...
 
 const style1={    
      background:'#eee',
      width:'200px',
      height:'200px'
    }
 
  const style2={    
      color:'red',
      fontSize:'40px'
    }
 
  render() {
    return (
      <div style={style1}>
        <p style= {style2}>行内样式</p>
      </div>
    )
  }
}

```

### 动画

#### 新建一个组件

```jsx
import React, { Component } from 'react';
class Boss extends Component {
    constructor(props) {
        super(props);
        this.state = {  }
    }
    render() { 
        return (  );
    }
}

export default Boss;
```

然后我们先写一些JSX代码，让页面上有一段文字和一个按钮。代码如下：

```javascript
import React, { Component } from 'react';
class Boss extends Component {
    constructor(props) {
        super(props);
        this.state = {  }
    }
    render() { 
        return ( 
            <div>
                <div>BOSS级人物-孙悟空</div>
                <div><button>召唤Boss</button></div>
            </div>
          );
    }
}

export default Boss;
```

目前组件没有任何业务逻辑，只有一个UI，这是没办法实现动画效果的。业务逻辑是点击按钮的时候可以改变字的'

'显示隐藏。 要实现这个业务逻辑，先在`constructor``isShow`

```js
this.state = { 
    isShow:true
}
```

然后把“字”的''部分，增加`className``isShow`

```jsx
<div className={this.state.isShow ? 'show' : 'hide'}>BOSS级人物-孙悟空</div>
```

需要点击按钮时，有响应的事件，所以需要一个方法，我们编写一个`toToggole()`方法，代码如下：

```javascript
toToggole(){
    this.setState({
        isShow:this.state.isShow ? false : true
    })
}
```

意思就是当`isShow`为`true`时，我们赋值`false`;当`isShow`为`false时，我们赋值`true`.

有了方法后，可以给``加上`onClick`响应事件了，代码如下：

```html
<div><button onClick={this.toToggole}>召唤Boss</button></div>
```

写完这个事件，还是需要到`constructor`里绑定一下`this`。代码如下：

```js
constructor(props) {
    super(props);
    this.state = { 
        isShow:true
    }
    this.toToggole = this.toToggole.bind(this);
}
```

这样我们的基本业务逻辑就算写完了，可以把代码加入到`Xiaojiejie`组件中，看一下效果了。

#### 加入动画

在页面上看不出任何的效果，如果你打开浏览器控制台是可以看到每次点击按钮，class都会变化的。界面没变化，知识我们没有写CSS。现在可以在`style.css`里写样式，代码如下:

```css
.show{ opacity: 1; transition:all 1.5s ease-in;}
.hide{opacity: 0; transition:all 1.5s ease-in;}
```



### keyframes动画

```css
.show{ animation:show-item 2s ease-in forwards; }
.hide{ animation:hide-item 2s ease-in forwards; }

@keyframes hide-item{
    0% {
        opacity:1;
        color:yellow;
    }
    50%{
        opacity: 0.5 ;
        color:red;
    }
    100%{
        opacity:0;
        color: green;
    }
}

@keyframes show-item{
    0% {
        opacity:0;
        color:yellow;
    }
    50%{
        opacity: 0.5 ;
        color:red;
    }
    100%{
        opacity:1;
        color: green;
    }
}
```

#### react-transition-group

1.安装

```shell
npm install react-transition-group --save
```

2.引入

```js
import { CSSTransition } from 'react-transition-group'
```

引入后便可以使用了，使用的方法就和使用自定义组件一样,直接写``，而且不再需要管理`className`了，这部分由`CSSTransition`进行管理。修改上节课写的`Boss.js`文件里的`render`区域。

3.使用

```jsx
render() { 
    return ( 
        <div>
            <CSSTransition 
                in={this.state.isShow}   //用于判断是否出现的状态
                timeout={2000}           //动画持续时间
                classNames="boss-text"   //className值，防止重复
                unmountOnExit
            >
                <div>BOSS级人物-孙悟空</div>
            </CSSTransition>
            <div><button onClick={this.toToggole}>召唤Boss</button></div>
        </div>
        );
}
```



- xxx-enter: 进入（入场）前的CSS样式；
- xxx-enter-active:进入动画直到完成时之前的CSS样式;
- xxx-enter-done:进入完成时的CSS样式;
- xxx-exit:退出（出场）前的CSS样式;
- xxx-exit-active:退出动画知道完成时之前的的CSS样式。
- xxx-exit-done:退出完成时的CSS样式。

知道了这些要设置的CSS，就可以删除原来写的CSS了，把下面的代码写上：

```css
.input {border:3px solid #ae7000}

.boss-text-enter{
    opacity: 0;
}
.boss-text-enter-active{
    opacity: 1;
    transition: opacity 2000ms;

}
.boss-text-enter-done{
    opacity: 1;
}
.boss-text-exit{
    opacity: 1;
}
.boss-text-exit-active{
    opacity: 0;
    transition: opacity 2000ms;

}
.boss-text-exit-done{
    opacity: 0;
}
```

#### 多DOM动画制作和编写

#### 使用TransitionGroup

```js
import {CSSTransition , TransitionGroup} from 'react-transition-group'
```

引入之后，就可以使用这个组件了，方法是在外层增加``标签。

```jsx
<ul ref={(ul)=>{this.ul=ul}}>
    <TransitionGroup>
    {
        this.state.list.map((item,index)=>{
            return (
                <XiaojiejieItem 
                key={index+item}  
                content={item}
                index={index}
                deleteItem={this.deleteItem.bind(this)}
                />
            )
        })
    }
    </TransitionGroup>
</ul> 
```

这个需要放在循环的外边，这样才能形成一个组动画,但是只有这个``是不够的，你还是需要加入``,来定义动画。

#### 加入CSSTransition动画

```jsx
<ul ref={(ul)=>{this.ul=ul}}>
    <TransitionGroup>
    {
        this.state.list.map((item,index)=>{
            return (
                <CSSTransition
                    timeout={1000}
                    classNames='boss-text'
                    unmountOnExit
                    appear={true}
                    key={index+item}  
                >
                    <XiaojiejieItem 
                    content={item}
                    index={index}
                    deleteItem={this.deleteItem.bind(this)}
                    />
                </CSSTransition>
            )
        })
    }
    </TransitionGroup>
</ul>  
<Boss />
</Fragment>
```