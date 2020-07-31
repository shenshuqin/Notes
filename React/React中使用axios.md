## 在React中使用Axios

### 安装

```shell
npm install -save axios
```



```js
import React, { Component } from 'react';
import axios from 'axios';
class Axios extends Component {
    constructor(props) {
        super(props);
        this.state = { 

            list:[]
         };
    }
    getData=()=>{
        //通过axios获取服务器数据
        var api='http://www.phonegap100.com/appapi.php?a=getPortalList&catid=20';   //接口后台允许了跨域
        axios.get(api)
        .then((response)=> {
            console.log(response.data.result);
            //用到this要注意this指向
            this.setState({
                list:response.data.result
            })
        })
        .catch(function (error) {
            console.log(error);
        });
    }
    render() {
        return (
            <div>
                <h2>axios获取服务器数据</h2>
                <button onClick={this.getData}>获取服务器api接口数据</button>
                <hr />
                <ul>
                     {
                        this.state.list.map((value,key)=>{
                            return <li key={key}>{value.title}</li>
                        })
                    }   
                </ul>
            </div>
        );
    }
}

export default Axios;
```





### 解析二维数组

```js
import React, { Component } from 'react';

import {Link } from "react-router-dom";

const axios = require('axios');

class Home extends Component {
    constructor(props) {
        super(props);
        this.state = { 

            list:[],
            domain:'http://a.itying.com/'
         };
    }
    requestData=()=>{

        var api=this.state.domain+'api/productlist';

        axios.get(api)
        .then((response)=>{         
             console.log(response);

            this.setState({

                list:response.data.result
            })
        })
        .catch(function (error) {         
          console.log(error);
        })
        
    }
    componentDidMount(){
        this.requestData();

    }
    render() {
        return (
            <div className="home">
                    <div className="list">
                        {
                        this.state.list.map((value,key)=>{
                        return(
                            <div className="item" key={key}>                          
                            <h3 className="item_cate">{value.title}</h3>
       					 <ul className="item_list">
                        {
                        value.list.map((v,k)=>{
                            return(
                          <li key={k}>	
                                <Link to={`/pcontent/${v._id}`}>
                                <div className="inner">
                                <img src={`${this.state.domain}${v.img_url}`} />
                                <p className="title">{v.title}</p>						
                                <p className="price">{v.price}元</p>
                             	</div>
        					</Link>		
						</li> 
                        )
                       })
                      }
                </ul>                            
               </div>
                )
              })
            }
          </div>
       </div>
        );
    }
}

export default Home;

```

