# Vue项目实战

### 1.初始化项目

**vue create app / vue init webpack app **

下载的是最基础的vue-cli脚手架

> node_modules文件夹：项目依赖文件

> public文件夹：一般放静态文件，会原封不动打包到dist文件夹中

> src文件夹：（程序员代码文件夹）
>
> > assets文件夹：一般也是静态资源（一般放多个组件共用的静态资源），需要注意，放在assets中的静态文件，在webpack打包的时候，会被当做一个模块，打包到JS文件里面
> >
> > components文件夹:一般放的是非路由组件(全局组件)
> >
> > pages/views文件夹：路由组件
> >
> > router：index.js 配置路由
> >
> > App.vue:唯一的根组件
> >
> > main.js:程序入口文件,也是最先执行的文件
>

>babel.config.js:配置文件（babel相关） ES6->ES5

>package.json：项目的身份证，记录了项目叫做什么，项目中有哪些依赖，项目怎么运行

>package-lock.json：下载时候的缓存文件

````j
// main.js中的内容
new Vue({
  el: '#app', // 这样写的意思是：实例化一个Vue，挂载到id为app的div里面，这个vue实例有个局部组件App
  router,
  components: { App },
  template: '<App/>' // template: '<App/>' 表示用<app></app>替换index.html里面的<div id="app"></div>
})
````

### 2.注册别的组件

```
import Header from './components/Header'

components:{
	Header
}
```

### 3.路由组件（vue-router）

main.js中

```javascript
import router from './router'

new Vue({
  el: '#app', // 这样写的意思是：实例化一个Vue，挂载到id为app的div里面，这个vue实例有个局部组件App
  router, // 注册路由信息，当这里写router的时候，组件身上都有$route和$router属性
  components: { App },
  template: '<App/>' // template: '<App/>' 表示用<app></app>替换index.html里面的<div id="app"></div>
})
```

router/index.js中

```javascript
import Vue from 'vue'
import R outer from 'vue-router'
import HelloWorld from '@/components/HelloWorld'

// 使用插件
Vue.use(Router)

export default new Router({
  routes: [
    {
      path: '/',
      name: 'HelloWorld',
      component: HelloWorld
    },
    {
      path: '*',
      redirect:'/home'
    }
  ]
})
```

$route：一般获取路由信息【路径,query,params等等】

$router:一般进行编程式导航进行路由跳转【push|replace】

#### **二级路由**

```javascript
export default new Router({
  routes: [
    {
      path: '/helloword',
      name: 'HelloWorld',
      component: HelloWorld,
      children:[
      	{
      		path:'myorder',
      		component:MyOrder
      	},
      	{
      		path:'groupOrder',
      		component:GroupOrder
      	}
      ]
    },
    {
      path: '*',
      redirect:'/home'
    }
  ]
})

```

**使用二级路由**

```
<router-link to='/helloword/myorder'></router-link>

// 还得在组件中显示
<router-view></router-view>
```



#### **路由的跳转**

**声明式导航**：router-link（只是跳，没有别的业务，必须有to）

```
<router-link to="/login" class=''>登录</router-link>
```

**编程式导航：**push|replace （可以做一些其他的逻辑）

```
@click = 'goSearch'

goSearch(){
	this.$router.push('/search')
}
```

#### 路由传参

**传参部分**

```javascript
//1.
goSearch(){
	this.$router.push('/search'+this.keyword+'?k='+this.keyword.toUpperCase()) // 传了params和query参数
}
//2.
goSearch(){
	this.$router.push('/search/${this.keyword}?k=${this.keyword.toUpperCase()}') // 传了params和query参数
}
//3.对象的方式 需要一个路由名字name，但不能使用path
getSearch(){
      this.$router.push({
        name:'search',
        params:{
          keyword:this.keyword
        },
        query:{
          k:this.keyword.toUpperCase()
        }
      })
    }
// 如果路由有要求要传parmas但是没有传，url就有问题
// 解决params传空串导致路由有问题：使用undefined
goSearch(){			this.$router.push('/search/${this.keyword||undefined}k=${this.keyword.toUpperCase()}') // 传了params和query参数
}
// 路由组件也可以传props
// 需要的条件：
routes: [
    {
        path: '/hello/:params?',  //如果指定parmas可传可不传？--->加一个"?"
        name: 'HelloWorld',
        component: HelloWorld,
        name:'helloworld',
        // 普通写法
        props:true，
        // 对象写法
        props:{a:1,b:2},
    	// 函数写法
    	props:($route)=>{
            return {keyword:$route.params.keyword,k:$route.query.k}
        }
    }
  ]
// 会将parmas视作props
// 接收：
name:'',
props:['keyword','k','a','b']
```

**接收部分**

```javascript
{{$route.params.keyword}} // params
{{$route.query.k}}  // query
```

### 4.全局组件显示或者隐藏

**使用路由元信息**：

```javascript
 routes: [
    {
      path: '/',
      name: 'HelloWorld',
      component: HelloWorld
      meta:{
        show:true
   	  }
    },
 ]
```

要展示或隐藏的全局组件：

```
<Footer v-show="$route.meta.show"></Footer>
```

### 5.三级联动组件(全局组件)

（在哪都有用，就可以注册为全局组件，在任何地方使用）

先设定组件的名字：

```
name:'TypeNav'
```

在main.js中

```javascript
import TypeNav from '@/pages/Home/TypeNav'
// 注册过程
Vue.component(TypeNav.name,TypeNav)
```

使用（不需要引入了）

```
<TypeNav/>
```

### 6.aixos二次封装

为什么要进行二次封装：

请求拦截器，响应拦截器

```shell
npm install --save axios
```

创建目录：

```shell
src/api/request.js
```

对外暴露：

```java
import axios from 'axios'

// 1.利用axios对象的方法create，去配置一个axios实例
// 2.request就是axios，只不过配置一下
const requests = axios.create({
  // 基础路径,默认加api
  baseURL: '/{目的ip地址}/api',
  // 请求超时时间：5s
  timeout: 5000
})
// 请求拦截器:在请求嫁出去之前做一些事情
requests.interceptors.request.use((config) => {
  // config就是配置对象，里面有一个属性很重要 header请求头
  return config
})
// 响应拦截器:返回之后做一些事情
requests.interceptors.response.use((res) => {
  return res.data
}, (error) => {
  // 终止promise链
  return Promise.reject(new Error('fail'))
})


export default requests
```

### 7.接口统一管理

项目很小：完全可以在组件的生命周期去发送请求

项目大：

```
src/api/index.js
```

对所有的api接口进行统一管理

```javascript
// 对所有的api进行统一管理
import requests from "./request";

// 对外暴露函数
export const reqCategoryList = () => {
  //axios发请求的结果是Promise对象
  return requests({
    url: '/product/getBaseCategoryList',
    method: 'get'
  })
}

```

使用：

```javascript
import {reqCategoryList} from "./api";

reqCategoryList()
```

### 8.nprogress进度条插件

```shell
npm install --save nprogress
```

```
import nprogress from 'nprogress'
import "nprogress/nprogress.css"
// 引入进度条的样式
// 可以在请求拦截器和响应拦截器各加一个
nprogress.start()
nprogress.done()
```

### 9.vuex状态管理库

状态管理库，集中式管理项目中组件公用的数据（组件很多的时候）

不是所有的项目都需要vuex。

* state
* mutations
* actions
* getters
* modules

```shell
src/store/index.js
```

配置：

```javascript
import Vue from "vue";
import Vuex from "vuex"
// 需要使用插件一次
Vue.use(Vuex)
// state：仓库存储数据的地方
const state = {
  count: 1 // 数据
}
// mutations: 修改state的唯一手段
const mutations = {
  ADD(state) {
    state.count++
  }
}
// action：处理action，书写自己的业务逻辑，处理异步
const actions = {
  // 这里可以书写业务逻辑，但是不能修改state
  add({commit}) {
    commit('ADD')
  }
}
// getters：理解为计算属性，用于简化仓库数据，让组件获取仓库数据更加方便
// 服务器传过来的数据是对象中的对象...的数据所以可以在这里简化
const getters = {
    // 当前形参state，当前仓库中的state，并非大仓库中的那个state
    goodsList(state){
        return state.searchList.goodsList||[]
    }
}
export default new Vuex.Store({
  state,
  mutations,
  actions,
  getters
})
```

在main.js中引入vuex仓库：

```javascript
import store from "./store";
/* eslint-disable no-new */
new Vue({
  el: '#app',
  router,
  store, // 组件实例身上多一个属性，就是$store
  components: {App},
  template: '<App/>' 
})
```

使用数据：

```javascript
{{count}}

import {mapState} from 'vuex'
import {mapGetters} from 'vuex'
methods:{
    add(){
        this.$store.dispatch('add')
    }
},
computed:{
    ...mapState(['count'])
    // mapGetters传递的是数组
    ...mapGetters(['goodsList'])
}
```

vuex最好使用模块化管理 

将一个仓库分成多个小仓库

```javascript
const store = new Vuex.Store({
	modules:{
		a:moduleA,
		b:moduleB
	}
})
```

### 10.动态绑定

```javascript
:class="{showClass:show}" // 动态绑定类
:style="{display:show?'block':'none'}" // 动态样式
```

### 11.事件委派

子元素的事件委派给了父亲，可以和编程式导航结合

```html
<div @click="goSearch">
	<h2 class="all">全部商品分类</h2>
	<h3 class="sort">...</h3>
</div>
```

但是可能会出现一些问题

* 如何确定点的就是所想要的标签？
* 如何获取想要的标签的值（是哪一级的）？

```javascript
// 添加一个自定义的属性来区分 只有a这个标签有这个属性
<a :data-categoryName = "c1.categoryName" :data-category1ID = "c1.categoryId>
...
<a :data-categoryName = "c1.categoryName" :data-category2ID = "c2.categoryId>
...
<a :data-categoryName = "c1.categoryName" :data-category3ID = "c3.categoryId>
    
// event可以获取到当前这个事件的节点event.target
goSearch(event){
    let element = event.target;
    let {categoryname,datacategory1id,datacategory2id,datacategory3id} = element.dataset;
    if(categoryname){
        
    }
}
```



### 12.函数的防抖与节流

```
npm install lodash
```

**节流**：在规定的间隔事件内不会重复出发事件回调，只有大于这个事件间隔才会触发回调，把频繁触发变成少量触发。

```javascript
input.oninput= _.throttle(function(){ 
	console.log('ajax发请求')
},1000)
```

**防抖**：前面的所有的触发都被取消，最后一次执行在规定的时间之后才会触发，也就是如果连续快速的触发，只会执行一次.

```javascript
input.oninput= _.debounce(function(){
	console.log('ajax发请求')
},1000)
```

### 13.过渡动画

前提组件：元素必须要友v-if|v-show指令才可以进行过渡动画

css中实现动画有两种方式：`transition`过渡动画、 `animation`自定义动画

### 14.如何让mounted中的axios请求不在切换路由时反复发送

方法就是在根组件 Vue.app中请求一次数据，这样其他组件切换的时候就不会反复请求了

### 15.mock.js

mock.js可以让ajax请求不会发送到后端，随机生成假数据

```shell
npm install --save mockjs
```

```
src/mock/mockServe.js
```

```javascript
// 先引入mock.js模块
import Mock from 'mockjs'
// 再引入json数据 webpack中默认暴露的：图片，JSON格式
import banner from './banner.json'

//mock数据：第一个参数请求地址，第二个数据：请求数据
Mock.mock("/mock/banner",{code:200,data:banner})
```

main.js引入并执行

```
import '@/mock/mockServe'
```

### 16.swiper

```shell
npm install --save swiper
```

swiper就是轮播图，可以百度查看如何使用，有专门的官网

主要就是现有DOM结构，然后通过Swiper实例去操作dom

```javascript
// 引包
import Swiper from 'swiper'
import 'swiper/css/swiper.css'
```

```javascript
// 在dom中添加图片等等
```

在组件的mounted当中添加实例

**！！如果是动态的数据，直接在mounted中创建实例，可能还没有获取数据就创建了！**

如何解决这个问题：

### 17.watch（监听已有数据的变化）+ $nextTick

**$nextTick：**在下次Dom更新循环（所有节点都更新完了）结束之后执行延迟回调，在修改数据之后立马使用这方法，获取更新之后的dom。

$nextTick不是处理异步的，而是处理更新的数据使dom发生变化之后的动作的。

```javascript
watch:{
	// 监听bannerList数据的变化 但这个时候不能马上就初始化实例 因为只是监听到了数据的变化，dom还不一定有
    // 主要原因是因为 数据是在组件内部发送请求得到的
	bannerList:{
		handler(newValue,oldValue){
			// 通过watch监听bannerList属性的属性值变化
			// 如果执行hanlder方法，代表一定有了这个属性的数据
            immediate:true // 立即监听，不管有没有数据变化，上来先监听一次
			this.$nextTick(()=>{
                // 保证数据已经回来，v-for等dom已经更新完毕
                初始化Swiper实例
            })
		}
	}
}
```

$nextTick和很多插件可以一起使用

```javascript
// watch与路由结合
watch:{
	$route(newValue,oldValue){
		Object.assgin(this.searchParmas,this.$route.query,this.$route.params)
	}
}
```



### 18.ref获取dom节点

先打上ref的标签，可以取代id

```
<div ref="mySwiper">
```

```javascript
// 获取到dom
this.$refs.mySwiper
```

### 19.组件通信的所有方式

* **props** 用于父子组件通信
* **@on @emit** 可以实现子给父传
* **全局事件总线$bus** 全能
* **pubsub.js** vue中几乎不用 全能
* **插槽**
* **vuex**

### 20.合并参数

合并参数是ES6新增的语法，可以让响应式数据的更新的编写更加方便，而不用一个一个重新赋值。

```javascript
let searchParmas:{
	category1Id = '',
	category2Id = '',
	category3Id = '',
	categoryName = '',
	keyword:'',
	order:'',
	pageNo:1,
	pageSize:3,
	props:[],
}
let query = {category1ID:'110',categoryName='手机'}
let params = {keyword:'华为'}
console.log(Object.assign(searchParmas,query,params))
```

### 21.Vue的滚动行为

在创建router路由实例的时候添加

```javascript
const router = new VueRouter({
  mode: 'history',
  base: process.env.BASE_URL,
  routes,
  scrollBehavior: (to, from, savePosition) => {
    // 常规的一种做法，直接禁止滚动行为，每个页面切换时自动回到顶部
    // return {x: 0, y:0}
    console.log('savePosition',savePosition);
    // 针对特殊页面，独立保存它们的滚动位置
    if(to.fullPath === "/"){
        let yy = savePosition && savePosition.y
        return {x: 0, y: yy}
    }
    // 其他页面路径
    return {x: 0, y: 0}
  }
  //scrollBehavior 方法接收 to 和 from 路由对象。第三个参数 savedPosition 
  //当且仅当 popstate 导航 (通过浏览器的 前进/后退 按钮触发) 时才可用。
  // scrollBehavior: (to, from, savePosition) => {
  //   // return 期望滚动到哪个的位置
  //   //  to：跳转到哪个页面
  //   // from：来自哪个页面
  //   //savePosition:位置
  //   // 常规的一种做法，直接禁止滚动行为，每个页面切换时自动回到顶部
  //   return { x: 0, y: 0 }
  // }
 
})
```

```javascript
// window中如何直接滑动
 window.scrollTo({
        'top':showElement.offsetTop, // number
        'behavior':'smooth'
      })
```

### 22.本地存储和会话存储（localStorage和sessionStorage）

HTML5的Web存储功能是让网页在用户计算机上保存一些信息。Web存储又分为两种：

(1)**本地存储**，对应localStorage对象。用于长期保存网站的数据，并且站内任何页面都可以访问该数据。

(2)**会话存储**，对应 sessionStorage对象。用于临时保存针对一个窗口(或标签页)的数据。在访客关闭窗口或者标签页之前，这些数据是存在的，而关闭之后就会被浏览器删除。

**两者的异同**：

* 本地存储和会话存储的操作代码完全相同，它们的区别仅在于数据的寿命。
* 本地存储主要用来保存访客将来还能看到的数据。
* 会话存储则用于保存那些需要从一个页面传递给下一个页面的数据。

```javascript
// 本地存储
localStorage.setItem('键', '值');  //本地存储键值对
//typeof 为object类型
let Name = localStorage.getItem('键') //在本地存储中获取键值对
console.log(Name)  //输出值
//如果从不存在的本地存储中获取内容，则结果为 null 

let name = ['a', 'b', 'c']
localStorage.setItem('name1',JSON.stringify(name));
 //将name以数组形式存储在name1中
```

```javascript
// 会话存储
// 存储键值对
sessionStorage.setItem('键', '值');
 
// 获取
sessionStorage.getItem('键');
 
// 存储数组
let name = ['a', 'b', 'c']
sessionStorage.setItem('name1',JSON.stringify(name));
 
// 获取数组
console.log(JSON.parse(sessionStorage.getItem('name1')));
 
// 清除会话存储
sessionStorage.clear()
 
// 清除特定键值对
sessionStorage.removeItem('键');
```

### 23.JSON.parse()和JSON.stringify()

JSON 通常用于与服务端交换数据。

在接收服务器数据时一般是字符串。

* 可以使用 **JSON.parse()** 方法将数据转换为 JavaScript 对象。

假如收到了如下数据：

```	Json
{ "name":"runoob", "alexa":10000, "site":"www.runoob.com" }
```

转为对象：

```javascript
var obj = JSON.parse('{ "name":"runoob", "alexa":10000, "site":"www.runoob.com" }');
```

* 可以使用 **JSON.stringify(**) 方法将 JavaScript 对象转换为字符串。

假如像发送以下数据：

```javascript
var obj = { "name":"runoob", "alexa":10000, "site":"www.runoob.com"};
```

转换为字符串：

```javascript
var myJSON = JSON.stringify(obj);
```

### 24.使用uuid生成唯一身份

安装：

```shell
npm install uuid
```

在js中使用并生成唯一id

```javascript
import {v4 as uuidv4} from "uuid";
export const getUUID = ()=>{
  // 先从本地存储获取uuid（看一下本地存储里面是否有）
  let uuid_token = localStorage.getItem('UUIDTOKEN')
  // 如果没有
  if (!uuid_token){
    // 生成游客临时身份
    uuid_token = uuidv4();
    localStorage.setItem('UUIDTOKEN',uuid_token)
  }
  return uuid_token
}
```

### 25.登录与注册

* 登录与注册的静态组件
* assets文件夹——放置全部组件的共用静态文件，css文件中也可以用@

**1.先生成静态组件**

```html
<form action="">
      <div>
        <label for="phone">手机号：</label>
        <input type="text" id="phone" placeholder="请输入你的手机号" v-model="phone">
        <span style="color: #9C1A1C">错误信息提示</span>
      </div>
      <div>
        <label for="yzm">验证码：</label>
        <input type="text" id="yzm" placeholder="请输入你的手机号" v-model="yzm">
        <span style="color: #9C1A1C">错误信息提示</span>
      </div>
      <div>
        <label for="password">手机号：</label>
        <input type="password" id="password" placeholder="请输入你的密码" v-model="password">
        <span style="color: #9C1A1C">错误信息提示</span>
      </div>
      <div>
        <label for="repassword">手机号：</label>
        <input type="password" id="repassword" placeholder="请重复输入你的密码" v-model="repassword">
        <span style="color: #9C1A1C">错误信息提示</span>
      </div>
      <button @click="submit">提交</button>
    </form>

```

**2.编写逻辑，如果成功就跳转到登录页面**

**3.登录逻辑，记得要阻止默认行为**

```ht
@click.prevent="userlogin"
```

* prevent是**preventDefault**,阻止标签默认行为，有些标签有默认行为，例如a标签的跳转链接属性href等。

* submit点击默认行为是提交表单，这里并不需要它提交，只需要执行register方法，故阻止为妙。

* stop是**stopPropagation**，阻止事件冒泡，点击哪个元素，就只响应这个元素，父级就不会响应了。

**4.token**

*一般登录成功服务器会下发token，前台持久化存储token，再带着token去服务器要用户信息进行展示*

vuex存数据不是持久化

**loken持久化存储**：localStorage

**获取用户信息，一般都是要header携带token，访问专门的getUserInfo接口**

在请求拦截器中：

```javascript
// 拦截器举例
// http request 拦截器
axios.interceptors.request.use(
    config => {
        if (store.state.token) {
            // 判断是否存在 token, 如果存在的话, 则每个 http header 都加上 token
            config.headers.Authorization = `token ${store.state.token}`;
        }
        return config;
    },
    err => {
        return Promise.reject(err);
    });
// http response 拦截器
axios.interceptors.response.use(
    response => {
        return response;
    },
    error => {
        if (error.response) {
            switch (error.response.status) {
                case 401:
                    // 返回 401 清除 token 信息并跳转到登录页面
                    store.commit(types.LOGOUT);
                    router.replace({
                        path: 'login',
                        query: {redirect: router.currentRoute.fullPath}
                    })
            }
        }
        return Promise.reject(error.response.data)
        // 返回接口返回的错误信息
    });
```

**5.navbar栏显示登录或者用户信息**

```html
<p v-if="!userName">
    <router-link to="/login">登录</router-link>
    <router-link to="/register">免费注册</router-link>
</p>
<p v-else>
    <a>{{userName}}</a>
    <a>退出登录</a>
</p>
```

**6.退出登录**

退出登录其实就是通知服务器清除掉token，再清楚前台localStorage，使访问后台接口的时候没有携带token

****

**7.导航守卫**

**导航**：标识路由正在发生改变，进行路由跳转

**守卫**：标识路由正在发生

* 全局守卫：只要发生路由的变化
* 路由独享守卫
* 组件内守卫

#### 全局路由

例子：用户已经登陆，是否还应该在login界面？

```javascript
router.beforeEach((to, from, next) => {
  // to:可以获取到要跳转到哪个路由信息
  // from：可以获取到从哪里路由来
  // next：放行函数
  // next() // 表示直接放行
  // next('/login') // 放行到指定路由
  // next(false)
  console.log(next)
  let token = store.state.user.token
  let name = store.state.user.userInfo.name
  if (token) {
    // 用户已经登录 就停留在首页
    if (to.path == 'login') {
      next('/home')
    } else {
      if (name) {
        next()
      } else {
        // 派发请求获取用户信息
      }
    }
  }
})
```

#### 路由独享与路由内守卫（就算是登录也有限制）

这种情况是只有一条或几条路由可以到指定页面

```javascript
{
      path: '/register',
      component: Register,
      name: 'register',
      beforeEnter: (to, from, next) => {
        if (from.path === '/') {
          next()
        } else {
          next(false) // 回到from对应的地址
        }
      }
    },
```

同样的还有：

* beforeRouteUpdate(to, from, next)

* beforeRouteLeave(to, from, next)

### 26.路由懒加载

```javascript
components: {
  // 异步组件的作用是不一开始就把所有组件下载，后来需要的时候去下载，优化性能
  Test: () => import('./Test')
}
```

### 27.打包上线

#### 1.build操作

1.修改请求静态资源的路径
打开config文件夹下的index.js文件，修改assetsPublicPath的值，从"/“改为”./"。即从根路径改为相对路径。

```
assetsPublicPath：'./'
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/41b29d6618a244d89636df8ab1345804.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA54ix5ZCD5qOJ6Iqx57OWIw==,size_18,color_FFFFFF,t_70,g_se,x_16#pic_center)

2、修改本地图片的路径
打开build下的utils.js文件，增加

```
publicPath:'../../'
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/655a5132af7c4703b0d79cbd330d51ed.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA54ix5ZCD5qOJ6Iqx57OWIw==,size_17,color_FFFFFF,t_70,g_se,x_16#pic_center)

```shell
npm run build
```

项目打包后，代码都是经过压缩加密的，如果运行时出错，输出的错误信息无法准确得知时哪里的代码错误，有了map就可以像未加密的代码一样，准确的输出是哪一行哪一列有错。

所以该文件如果项目不需要是可以去掉的

vue.config.js 配置中

productionSourceMap:false

#### 2.购买云服务器

#### 3.安全组（让服务器的某些端口对外开放） 

#### 4.利用Xshell登陆服务器

