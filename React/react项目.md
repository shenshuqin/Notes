####  构建React项目

`create-react-app test` 初始化一个项目;

`npm run start`启动项目

`npm run build` 构建项目

##### 在项目中使用dva

+ 在项目中src目录下
- assets (img,js,css等静态文件);
- components(组件模块);
- config(域名,端口);
- models(数据层);
- routers(路由配置 index.js);
- utils(封装的函数)

+ 首先注释掉index.js中的    ReactDOM.render(<App />, document.getElementById('root'));

+ 安装history包,使用history模式  `npm install history dva -S`

+ 在index.js中
  
   ```js
   import React from 'react';
   import ReactDOM from 'react-dom';
   import './index.css';
   import App from './App';
   import * as serviceWorker from './serviceWorker';
   
   
   import router from './routers';  // 默认查找index.js
   import modelIndex from './models'; //从models中引入模版
   import teacherModel from './models/teacher.js'; 
   import createHistory from 'history/createBrowserHistory'
   
   import 'bootstrap/dist/css/bootstrap.min.css';
   import 'font-awesome/css/font-awesome.css';
   import './assets/css/index.css';
   
   
   
   // 1: 引入dva
   import dva from 'dva';
   // 2: 创建dva的实例 app对象
   let app = new dva({
       history:createHistory()
   });
   // 3: 配置路由 app.router(fn);
   app.router(router);
   // 4: app.use();  //安装插件
   // 5: 注册模块app.model({});  {state,effects,reducers}
   app.model(modelIndex);
   app.model(teacherModel);
   // 5.5: 卸载模块 app.unmodel();
   // 6: app.start('#root')  
   app.start('#root');
   
   
   
   
   // ReactDOM.render(<App />, document.getElementById('root'));
   
   // If you want your app to work offline and load faster, you can change
   // unregister() to register() below. Note this comes with some pitfalls.
   // Learn more about service workers: http://bit.ly/CRA-PWA
   serviceWorker.unregister();
   
   ```
   
   小案列
   
   + 组件home
```js
import React,{Component} from 'react';

import { connect } from 'dva';

class Home extends Component {
    test(){
        console.log('点击执行了')
        this.props.dispatch({type:'index/changeNum',payload:{num:8} });
    }
    render() {
        console.log(this.props)
        return  (
            <React.Fragment>
                数量是{this.props.num}
                <button onClick={e=> this.test() }>变更num</button>
            </React.Fragment>)
    }
}

export default connect(state=>{
    return {
        num:state.index.num
    }
})(Home);

```
   - models-index.js
```js
import request from '../utils/request.js';
let index = {
    namespace:'index',
    state:{ num:0 },
    effects:{
        // 异步action
        *changeNum({payload},{select,put,call }) {   
            //{payload:{num:5}} //payload是一个action对象,put调用reducers,select取state所有数据
            let res = yield call(request('teachers/search')); //call是调用异步函数
            console.log(res);
            // 一会发一个请求
            console.log('changeNum')
            yield put({ type:'addNum',payload })
        }
        
    },
    reducers:{
        addNum(state,{payload}) {
            return {  // 返回一个新值,值不可变性
                num: state.num + payload.num
            }
        }
    }
}





export default index;
```
- routers -index.js
```js
import React from 'react';
// 配置路由规则
import { Router,Route,NavLink,Switch,Redirect } from 'dva/router';
import Home from '../components/Home.js';
import Signin from '../components/Signin.js';

let fn = function ({ history,app }) {
    return (
        <Router history={history}>
               <Switch>
                    <Route path="/home" component={Home}/>
                    <Route path="/signin" exact component={Signin}/>
               </Switch>
        </Router>
    )
}

export default fn;
```
- config -index.js
```js
export const serverHost = 'http://www.sinya.online';
export const port = 3002;

```
- utils -request.js
```js
// 封装一个axios的实例
import Axios from 'axios';
import { serverHost,port } from '../config';
// 创建一个新的实例
let r = Axios.create({
    baseURL:`${serverHost}:${port}/`
});

let request = function (url='',options={}) {
   return function(){
        if (url === '') return Promise.reject('必须传递url');
        // 将promise对象返回去
       return r({
            url,
            method:'get', // method先给一个get请求
            ...options, // options中有method就会覆盖
        });
   }
}

export default request;
```