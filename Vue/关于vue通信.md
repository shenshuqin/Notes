### 一.父组件像子组件传数据

#### 传递数据注意:

1.props只用于父组件像子组件传值

2.如果要像非子组件传递数据,必须逐层传递

3兄弟组件传值需借助父组件

#### 1.1传值
1.在父组件进行绑定
```vue
//父组件
//引入的child组件
//使用 v-bind 的缩写语法通常更简单：
<child :msg-val="msg"> //这里必须要用 - 代替驼峰
// HTML 特性是不区分大小写的。所以，当使用的不是字符串模板，camelCased (驼峰式) 命名的 prop 需要转换为相对应的 kebab-case (短横线隔开式) 命名，当你使用的是字符串模板的时候，则没有这些限制 
</child>
data(){
    return {
        msg: [1,2,3]
    };
}
```
2.子组件props接受
```vue
//子组件通过props来接收数据：
//方式1：
props: ['msgVal']   //在menthod中可以this.magVal调用,相当于在data中定义的全局变量
//方式2 :
props: {
    msgVal: Array //这样可以指定传入的类型，如果类型不对，会警告
}
//方式3：
props: {
    msgVal: {
        type: Array, //指定传入的类型
        //type 也可以是一个自定义构造器函数，使用 instanceof 检测。
        default: [0,0,0] //这样可以指定默认的值
    }
}
//注意 props 会在组件实例创建之前进行校验，所以在 default 或 validator 函数里，诸如 data、computed 或 methods 等实例属性还无法使用
```
#### 1.2传递函数
1.在父组件中声明的函数绑定到子组件
```vue
<Child :deletd="delted" :index="index"></Child>
methods:{
	deleted(index){
	  this.arr.splice(index,1);
	}
```
2.在子组件中接受
```vue
<a  @click="deletedTemp">删除</a>
props:['deletd'],
methods:{
	deletedTemp(){
		this.deleted(this.index)
	}
}
```
方法二:自定义事件(子组可以向父组件传值)

在父组件中定义事件监听函数,并引用子组件v-on绑定事件监听

```vue
<Child @deleted_hobby="deletdHobby"></Child> //触发deleted_hobby来调用deletedHobby
methods:{
	deletdHobby(index){
	  this.arr.splice(index,1);
	}
```

在子组件中触发监听事件函数执行

```vue
<a  @click="deletedTemp(index)">删除</a>
props:['deletd'],
methods:{
	deletedTemp(index){
		//触发父组件deleted_hobby
		this.$emit('deleted_hobby',index)
	}
}

```



#### 1.3父组件调用子组件的方法

1.子组件
```vue
<template>
  <div>
    child
  </div>
</template>
 
<script>
  export default {
    name: "child",
    props: "someprops",
    methods: {
      parentHandleclick(e) {
        console.log(e)
      }
    }
  }
</script>
```
2.父组件
```vue
<template>
  <div>
    <button @click="clickParent">点击</button>
    <child ref="mychild"></child>
  </div>
</template>
 
<script>
  import Child from './child';
  export default {
    name: "parent",
    components: {
      child: Child
    },
    methods: {
      clickParent() {
        this.$refs.mychild.parentHandleclick("嘿嘿嘿");
      }
    }
  }
</script>
```
#### 1.4 slot插槽(主要用于父组件向子组件传递标签+数据)
1.在子组件定义插槽
```vue
<slot name="dashboard"></slot>
```
2.父组件中使用
```vue
<child> 
<!--slot属性值对应子组件中的插槽的name属性值--> 
   <h1 slot="dashboard" class="page-header">{{title}}</h1>
</child>
```
### 二.子组件向父组件传值
1.子组件通过this.$emit()的方式将值传递给父组件
```vue
<template>
    <div class="app">
       <input @click="sendMsg" type="button" value="给父组件传递值">
    </div>
</template>
<script>
export default {
 
    data () {
        return {
            //将msg传递给父组件
            msg: "我是子组件的msg",
        }
    },
     methods:{
         sendMsg(){
             //func: 是父组件指定的传数据绑定的函数，this.msg:子组件给父组件传递的数据
             this.$emit('func',this.msg)
         }
     }
}
</script>
```
2.父组件, 这里的func是父组件中绑定的函数名 
```vue
<template>
    <div class="app">
        <child @func="getMsgFormSon"></child>
    </div>
</template>
<script>
import child from './child.vue'
export default {
    data () {
        return {
            msgFormSon: "this is msg"
        }
    },
    components:{
        child,
    },
    methods:{
            getMsgFormSon(data){
                this.msgFormSon = data
                console.log(this.msgFormSon)
            }
    }
}
</script>
```

### 三.兄弟组件传值
第三方插件 pubsubjs 发布-订阅
下载:`npm i pubsub-js -S`
在需要使用的组件中引用`import PubSub from 'pubsub-js'`
pub-sub库的使用。

发布:

```js
PubSub.publish("事件名",data);
```

订阅:

```js
PubSub.subscribe('事件名',function(event,data){  //event 接受的是消息名称 data是传递的数据
    //事件回调处理
})
```



案列:

项目中我们头部header需要向后台发送关键字，后台根据得到的关键字进行相应的操作，返回项目需要的数据。main主体区域中需要利用后台返回的数据，进行页面的渲染，main区域中必定会利用header中提供的关键字发送ajax请求，所以这就牵扯到组件之间的通信问题，pubsub-js就是用来实现组件之间通信的。兄弟组件之间通信如果利用props属性，需要借助父组件来实现，pubsub跨越组件之间的关系阶层进行通信。pubsub-js也就是我们所说的订阅消息和发布消息，订阅消息可以理解为事件的监听，发布消息可以理解为触发事件。

我们在header中点击搜索会通知main区域向后台发送Ajax请求，所以我们在header中发布消息，main中订阅消息
header中:

```vue
import PubSub from 'pubsub-js'
export default{
	name:'header',
	data(){
		return{
		 searchName:'',
		}
	},
	methods:{
		search(){
			//发布消息
			const searchName = this.searchName.trim();
			if(serachName){
				PubSub.publish('search',searchName);
			}
		}
	}
}
```
main.vue
```vue
	mounted(){
		PubSub.subscribe('search',(event,searchName)=>{
		   console.log(searchName);
			......
		})
	}
```