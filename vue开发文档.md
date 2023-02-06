# vue开发文档1.vue-router

### 1.路由部分

前端路由：**KV键值对**

key：URL(地址栏中的路径)

value：相应的对应路由

注意：项目的上中下结构

**注意分类**

路由组件：

Home首页组件路由，Search组件路由、Login登录路由

非路由组件：

Header、Footer【在首页和搜索页】，但是在登录页面没有

在App.vue中加入非路由组件 ,Header是一个components目录下的目录，里面有一个index.vue文件

``` javascript
import Header from './components/Header'
export default{
    name:'',
    components:{
        Header
    }
}
```

然后就可以直接用<Header/>了

+ components文件夹：放全局组件

+ pages|views文件夹：放路由组件

需要一个routers文件夹，里面写入index.js文件

对于该文件，需要：

``` javascript
import Vue from 'vue'
import VueRouter from 'vue-router'

vue.use(VueRouter)

// 引入路由
import Home from './pages/Home'

// 配置路由
export default new VueRouter({
    // 配置路由
    routes:[
        {
            path:"/home"
            component:Home
        }
    ]
})
```

于是在App.vue中就可以是

``` vue
<Header></Header>
<router-view></router-view>
<Footer></Footer>
```

在main.js中就可以引入路由对象了

``` javascript
import router from './router'
new Vue({
    render:h=>h(App)
    router
}).$mount('#app')
```

对于router和route的区别：

$route:一般获取路由信息（路径、query、params等等）

$router：一般进行编程式导航进行理由跳转（push|replace）

**不管路由组件，还是非路由组件，身上都有$route和$router属性**

**声明式导航：(一定要有to属性)，可以在某个组件中插入别的非路由组件**

``` vue
<router-link to = '/login'></router-link>
```

**编程式导航：**

``` javascript
methods:{
    goSearch(){
        this.$router.push('/search')
    }
}
```

Footer组件的显示与隐藏

显示或者隐藏：**v-if|v-show （需要一个bool值）**

可以根据组件身上的$route获取当前的路由信息，通过路由路径判断Footer的显示与隐藏

利用路由元信息，配置对象，必须写在**meta**里面：

```vue
routes:[
        {
            path:"/home"
            component:Home
			meta:{show:true}
        }
    ]
```

路由的显示就可以是

``` vue
<Footer v-show="$route.meta.show"></Footer>
```

**路由传参**

路由传参中的几种写法

**params参数**：属于路径当中的一部分，需要注意，在配置路由的时候，需要占位

**query参数**：不属于路径当中一部分

假如Search组件要接受跳转过来时，传过来的参数：

``` vue
// Search组件中的显示部分
<h1>params参数---{{$route.params.keyword}}</h1>
<h1>query参数---{{$route.query.k}}</h1>
```

最好的是对象的方式

在路由中需要这么写：

``` javascript
 // 配置路由
    routes:[
        {
            path:"/search"
            component:Home
            meta:{show:true}
            name:"search"
        }
    ]
```

传参的部分：

``` javascript
methods:{
    goSearch(){
        this.$router.push({
            name:"search",
            params:{
                keyword:this.keyword
            },
            query:{
                k:this.keyword.toUpperCase()
            }
        })
    }
}
```

解决vue项目中搜索框多次点击报错的情况：重写push和replace

**vue-router.esm.js?ac56:2065 Uncaught (in promise) NavigationDuplicated: Avoided redundant navigation to current location: “/search?k=ASD”.**

在路由index.js中重写push或者replace方法：

``` javascript
//为解决多次点击搜索按钮产生的报错需要重写push或replace方法
// 一、把VueRouter原型对象的push，先保存一份
let originPush = VueRouter.prototype.push;
// 二、重写push|replace
// 第一个参数告诉原来push方法往哪里跳转
// 第二个参数成功的回调
// 第三个参数失败的回调
VueRouter.prototype.push = function (location, resolve, reject) {
    if (resolve && reject) {
        // call和apply的区别:相同点，都可以调用函数一次，都可以篡改函数的上下文一次
                //不同点：call与apply传递参数：call传递参数用逗号隔开，apply是传递包含多个参数的数组
        originPush.call(this, location, resolve, reject);   
    } else {
        originPush.call(this,location,()=>{},()=>{})
    }
}

```

**三级联动组件完成**

由于三级联动在Home，Search，Detail中都出现了，可以把三级联动注册为全局组件

好处：只用注册一次，就可以在任何地方使用

在Home文件夹创建新的文件夹TypeNav，放入样式和文件资源



首先该组件TypeNav中已经给自己export了名字

``` javascript
export default ({
    name:"TypeNav"
})
```

在**main.js**中注册

``` javascript
// 三级联动组件---全局组件
import TypeNav from './pages/Home/TypeNav'
// 第一个参数：全局组件名字 第二个参数：哪一个组件
Vue.component(TypeNav.name,TypeNav)
```

在Home中直接使用（已经注册为全局了）

``` vue
<template>
	<div>
        <TypeNav></TypeNav>
    </div>
</template>
```

Home中引入其余的组件

``` javascript
import ListContainer from './pages/Home/ListContainer'
export default{
    name:'',
    components:{
        ListContainer
    }
}
```

然后使用

``` vue
<template>
	<div>
        <TypeNav></TypeNav>
        <ListContainer></ListContainer>
    </div>
</template>
```

### 2.axios二次封装

在src下创建api，创建request.js文件

``` javascript
// 对于axios进行二次封装
import axios from "axios"
import nprogress from 'nprogress'
import "nprogress/nprogress.css"

// 利用axios对象的方法create，创建实例 

const requests = axios.create({
    //基础配置，请求的路径中有api
    baseURL:"/api"
    timeout:5000
})

// 请求拦截器，在发送前可以检测到
requests.interceptors.request.use((config)=>{
    // config 是配置对象，里面有一个属性很重要，headers请求头
    nprogress.start();
    return config;
})

// 响应拦截器
requests.interceptors.response.use((res)=>{
    // 成功的情况
    nprogress.done();
    return res.data
},(error)=>{
    // 相应失败的函数
    return Promise.reject(new Error('fail'))
})

export default requests
```

如何当组件一挂载完毕，就发送请求

``` javascript
export default{
    name:"TypeNav",
    // 组件挂载完毕，就可以发送请求
    mounted(){
        // 这里写一个函数
    }
}
```

