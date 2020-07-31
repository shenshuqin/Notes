## VUEX的使用
### 安装

```shell
npm install --save vuex
```

### 读取状态state

#### 理解

1.每一个vuex项目的核心就是store(仓库),store就是一个对象,它包含着项目中大部分的状态(state)

2.state是store对象中一个选项,是vuex管理的状态对象(共享的数据属性)

#### 实操

1.在src目录下创建store目录,store下创建index.js文件

```js
import Vue from 'vue'
import Vuex from 'vuex'

//引入Vuex插件
Vue.use(Vuex)

const store = new Vuex.Store({ //注意V,S都是大写
    //存放状态(共享属性)
    state:{
        count:1
    }
})
export defalut store
```

2.修改main.js,导入和注册store

```js
import Vue from "vue"; 
import App from "./App.vue"; 
import router from "./router"; 
// 导入的是 src/store/index.js 
import store from './store' 
Vue.config.productionTip = false; 
new Vue({ 
    router, 
    store, // 注册 
    render: h => h(App) 
}).$mount("#app");
```

3.在组件中读取state状态数据

```vue
<template> 
<div>
  count: {{ $store.state.count }}
 </div> 
</template> 
<script> </script>
```

### 改变状态值 mutation

#### 理解

1.在store的mutations选项中定义方法,才可以改变状态值

2.通过$store.commit('mutationName')触发状态值的改变

#### 实操

1.修改store/index.js,在store中添加mutations选项

```js
import Vue from 'vue'
import Vuex from 'vuex'

//引入Vuex插件
Vue.use(Vuex)

const store = new Vuex.Store({ //注意V,S都是大写
    //存放状态(共享属性)
    state:{
        count:1
    },
    //改变state状态
    mutations:{
        increment(state){
            state.count++
        },
        decrement(state){
            state.count--
        }
    }
})
export defalut store
```

2.在组件中调用increment.decrement

```vue
<template> 
<div>
    count: {{ $store.state.count }} 
    <button @click="addCount">加法</button> 
    <button @click="decrement">减法</button> 
</div> 
</template> 
<script> 
    export default {
    methods: { 
        addCount() { // 获取状态值
         console.log(this.$store.state.count) // 通过commit 调用 mutations 中的 increment 改变状态值 
          this.$store.commit('increment') 
        },
        decrement() { 
           this.$store.commit('decrement')   // 通过commit 调用 mutations 中的 decrement 改变状态值 
        }
},
}
</script>
```

#### 提交负载

可以向$store.commit传入参数,即mutation的载荷(payload)

1.修改src\store下的index.js

```js
mutations:{
    increment(state,n){//n为载荷
    state.count+=n;
    }
}
```

2.修改组件

```js
<script>
    export default{
	methods:{
        addCount(){
            this.$store.commit('increment',10S)
        }
    }
}
    </script>
```

### Action

#### 理解

Action类似于mutations,不同于:

+ Action 提交的是 mutation，而不是在组件中直接变更状态， 通过它间接更新 state。 

+ 在组件中通过 this.$store.dispatch('actionName') 触发状态值间接改变 
+ Action 也支持载荷 
+ Action 可以包含任意异步操作。 

#### 实操

1.修改store/index.js,增加actions选项

```js
import Vue from 'vue'
import Vuex from 'vuex'

//引入Vuex插件
Vue.use(Vuex)

const store = new Vuex.Store({ //注意V,S都是大写
    //存放状态(共享属性)
    state:{
        count:1
    },
    //改变state状态
    mutations:{
        increment(state){
            state.count++
        },
        decrement(state){
            state.count--
        }
    },
    actions:{
        add(context,n){
            //触发mutations中的increment改变state
            context.commit('increment',n)
        },
        decrement({commit,state}){
            commit('decrement')//按需传值
        }
    }
})
export defalut store
```

2.组件

```js
<script>
   export default{
	methods:{
        addCount(){
            //this.$store.commit('increment',10S)
          this.$store.dispatch('add',10)
        },
        decrement(){
            this.$store.dispatch('decrement')
        }
    }
}
</script>
```

### 派生属性getter

#### 理解

+ 有时候我们需要从 store 中的 state 中派生出一些状态。

+ 例如：基于上面代码，增加一个 desc 属性，当 count 值小于 50，则 desc 值为 吃饭 ; 大于等于 50 小于 

  100，则desc 值为 睡觉 ; 大于100 , 则 desc 值为 打豆豆 。这时我们就需要用到 getter 为我们解决。 

+ getter 其实就类似于计算属性(get)的对象

+ 组件中读取 $store.getters.xx

1.修改store/index.js,增加getters选项

```js
const store = new Vuex.Store({
    //派生属性
    getters:{
        desc(state){
            if(state.count<50){
                return '吃饭'
            }else if(state.count <100){
                return '睡觉'
            }else{
                return '打豆豆'
            }
        }
    }
})
```

2.组件

```js
<template> 
<div>
  count: {{ $store.getters.desc}}
 </div> 
</template> 
<script> </script>
```

### 模块化

### module

```js
const moduleA = { 
    state: { ... },
     mutations: { ... }, 
     actions: { ... }, 
     getters: { ... } 
}
const moduleB = { 
      state: { ... },
      mutations: { ... }, 
      actions: { ... } 
}
const store = new Vuex.Store({
   modules: { 
    a: moduleA,
    b: moduleB 
}
 })
store.state.a // -> moduleA 的状态 
store.state.b // -> moduleB 的状态
```

### 实例

1.修改store/index.js

```js
import Vue from 'vue'
import Vuex from 'vuex'

//引入Vuex插件
Vue.use(Vuex)

const hone = {
       //存放状态(共享属性)
    state:{
        count:1
    },
    //改变state状态
    mutations:{
        increment(state,n){
            state.count+=n
        },
        decrement(state){
            state.count--
        }
    },
    actions:{
        add(context){
            //触发mutations中的increment改变state
            context.commit('increment',10)
        },
        decrement({commit,state}){
            commit('decrement')//按需传值
        }
    }
}
const store = new Vuex.Store({ //注意V,S都是大写
   modules:{
       home //相当于home:home
   }
})
export defalut store
```

2.组件

```js
<template> 
<div>
    count: {{ $store.state.hone.count }} 
    <button @click="addCount">加法</button> 
    <button @click="decrement">减法</button> 
     派生属性:{{$store.getters.desc}}
</div> 
</template> 
<script> 
    export default {
    methods: { 
        addCount() { // 获取状态值
         console.log(this.$store.state.home.count) // 通过commit 调用 mutations 中的 increment 改变状态值 
          this.$store.dispatch('add',10) 
        },
        decrement() { 
           this.$store.dispatch('decrement')   // 通过commit 调用 mutations 中的 decrement 改变状态值 
        }
},
}
</script>
```

### demo ----计数器

#### store.js

```js
/*** vuex 的 store 对象模块 */
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)

/*state 对象 类似于 data */
const state = { 
    count: 0 // 初始化状态数据 
}

/*mutations 对象
包含个方法: 能直接更新 state 
一个方法就是一个 mutation 
mutation 只能包含更新 state 的同步代码, 也不会有逻辑 
mutation 由 action 触发调用: commit('mutationName') */
const mutations = { 
    INCREMENT(state) { 
        state.count++ 
    },
    DECREMENT (state) { // ctrl + shift + x 
        state.count-- 
    } 
}

/*actions 对象 
包含个方法: 触发 mutation 调用, 间接更新 state 
一个方法就是一个 action 
action 中可以有逻辑代码和异步代码 
action 由组件来触发调用: this.$store.dispatch('actionName') */ 
const actions = {
    increment ({commit}) { 
        commit('INCREMENT') 
    },
    decrement ({commit}) { 
        commit('DECREMENT') 
    },
    incrementIfOdd ({commit, state}) { 
        if(state.count%2===1) { 
            commit('INCREMENT') 
        } 
    },
    incrementAsync ({commit}) { 
        setTimeout(() => { 
            commit('INCREMENT') 
        }, 1000) 
    } 
}

/*getters 对象 
包含多个 get 计算计算属性方法 */
const getters = { 
    oddOrEven (state) { 
        return state.count%2===0 ? '偶数' : '奇数' 
    },
    count (state) { 
        return state.count 
    } 
}// 向外暴露 store 实例对象 
export default new Vuex.Store({ 
    state, 
    mutations, 
    actions, 
    getters 
})
```

#### main.js

```js
import Vue from 'vue' 
import app from './app1.vue' 
// import app from './app.vue' 
import store from './store' 
new Vue({ 
    el: '#app', 
    render: h => h(app), 
    store // 所有组件都多个一个属性: $store })
```

### app.vue

```vue
<template>
<div> 
    <p>clicked: {{$store.state.count}} times, count is {{oddOrEven}}</p> 
    <button @click="increment">+</button> <button @click="decrement">-</button> 
    <button @click="incrementIfOdd">increment if odd</button> 		<button @click="incrementAsync">increment async</button> 
    </div> 
</template>

<script> 
    export default { 
        computed: { 
            oddOrEven () { 
                return this.$store.getters.oddOrEven 
            } 
        },
        methods: { 
            increment () { this.$store.dispatch('increment') },
            decrement () { this.$store.dispatch('decrement') },
            incrementIfOdd () { this.$store.dispatch('incrementIfOdd') },
            incrementAsync () { this.$store.dispatch('incrementAsync') } 
        } 
    } 
</script>
```

#### 优化

app.vue

```vue
<template> 
<div> 
    <p>clicked: {{count}} times, count is {{oddOrEven2}}</p> <button @click="increment">+</button> 
    <button @click="decrement">-</button> 
    <button @click="incrementIfOdd">increment if odd</button> <button @click="incrementAsync">increment async</button> 
 </div> 
</template> 
<script> 
    import {mapState, mapGetters, mapActions} from 'vuex' 
    export default { 
       /* computed:{
            ...mapSate(['count']),
            ...mapGetters(['evenOrOdd'])
        }*/
        computed: mapGetters({ // 名称不一样 
            oddOrEven2: 'oddOrEven', 
            count: 'count' }),
        methods: mapActions(
            ['increment', 'decrement', 'incrementIfOdd', 'incrementAsync']
        ) // 名称一样 } 
</script>
```

