## React生命周期函数

  组件加载之前，组件加载完成，以及组件更新数据，组件销毁。

  触发的一系列的方法 ，这就是组件的生命周期函数

### 组件加载

组件加载的时候触发的函数： 

  constructor 、componentWillMount、 render 、componentDidMount

### 组件更新

组件数据更新的时候触发的生命周期函数：

  shouldComponentUpdate、componentWillUpdate、render、componentDidUpdate

### 改变props传值时触发

你在父组件里面改变props传值的时候触发的：

  componentWillReceiveProps

### 组件消毁

组件销毁的时候触发的：

  componentWillUnmount

### 必须记住的生命周期函数：

  加载的时候：componentWillMount、 render 、componentDidMount（dom操作）

  更新的时候：componentWillUpdate、render、componentDidUpdate

  销毁的时候： componentWillUnmount

```js
class Lifecycle extends Component {
    constructor(props) {

        console.log('01构造函数');
        super(props);
        this.state = { 

            msg:'我是一个msg'
         };
    }  

    //组件将要挂载的时候触发的生命周期函数
    componentWillMount(){

        console.log('02组件将要挂载');
    }
    //组件挂载完成的时候触发的生命周期函数
    componentDidMount(){

        //dom操作放在这个里面    请求数据也放在这个里面

        console.log('04组件将要挂载');
    }


    //是否要更新数据  如果返回true才会执行更新数据的操作
    shouldComponentUpdate(nextProps, nextState){
        console.log('01是否要更新数据');

        console.log(nextProps);

        console.log(nextState);

        return true;
    }

    //将要更新数据的时候触发

    componentWillUpdate(){
        console.log('02组件将要更新');
    }
    //组件更新完成
    componentDidUpdate(){
        console.log('04组件数据更新完成');
    }

    // 你在父组件里面改变props传值的时候触发的

    componentWillReceiveProps(){

        console.log('父子组件传值，父组件里面改变了props的值触发的方法')
    }

    setMsg=()=>{

        this.setState({

            msg:'我是改变后的msg的数据'
        })
    }

    //组件销毁的时候触发的生命周期函数   用在组件销毁的时候执行操作
    componentWillUnmount(){

            console.log('组件销毁了');
    }
    render() {
        console.log('03数据渲染render');
       
        return (
            <div>

                生命周期函数演示--- {this.state.msg}-----{this.props.title}

                <br />
                <br />

                <button onClick={this.setMsg}>更新msg的数据</button>
            </div>
        );
    }
}

export default Lifecycle;
```

### 生命周期改善程序性能

你在浏览器的文本框中输入一下内容，你可以清楚的看到子组件也发生了重新`render`的情况。

有很多程序员会忽略这样的性能损耗，认为没有什么大不了的，但是软件的卡顿是一点点产生的，所以必须要减少性能损耗。

####  shouldComponentUpdate 解决

shouldComponentUpdate有两个参数：

- nextProps:变化后的属性;
- nextState:变化后的状态;

```javascript
shouldComponentUpdate(nextProps,nextState){
    if(nextProps.content !== this.props.content){
        return true
    }else{
        return false
    }

}
```

现在的代码就优雅一些了，也不那么暴力了。这就算是完美解决了子组件的渲染性能问题，你写的代码质量也得到了提高。其实在面试React让写TODOList应用的，都是看这个来区分等级的，能写出来的，这算普通程序员;能写出来并作性能优化的，这算有经验的程序员。

