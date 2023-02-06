# axios详细使用

### 1.json-server模拟后端

创建 json-server/db.json

复制以下json代码供于测试。

```json
{
  "posts": [
    {
      "id": 1,
      "title": "json-server",
      "author": "typicode"
    }
  ],
  "comments": [
    {
      "id": 1,
      "body": "some commont",
      "postId": 1
    }
  ],
  "profile": {
    "name": "typicode"
  }
}
```

全局安装json-server：

```shell
npm install -g json-server
```

启动server：

```shell
json-server --watch db.json
```

运行结果：

```shell

  \{^_^}/ hi!

  Loading db.json
  Done

  Resources
  http://localhost:3000/posts
  http://localhost:3000/comments
  http://localhost:3000/profile

  Home
  http://localhost:3000

```

### 2.axios的基本使用

cdn引入后

* GET，POST，PUT，DELETE 

```javascript
const btns = document.querySelectorAll('button')
        btns[0].onclick = function (){
            // 发送AJAX请求
            axios({
                // 请求资源
                method:'GET',
                // URL
                url:'http://localhost:3000/posts/2'
            }).then(response=>{
                console.log(response)
            })
        }
        btns[1].onclick = function (){
            // 发送AJAX请求
            axios({
                // 请求资源
                method:'POST',
                // URL
                url:'http://localhost:3000/posts',
                // 设置请求体对象
                // 没有写id
                data:{
                    title:"sunny day",
                    author:"me"
                }
            }).then(response=>{
                console.log(response)
            })
        }
        btns[2].onclick = function (){
            // 发送AJAX请求
            axios({
                // 请求资源
                method:'PUT',
                // URL
                url:'http://localhost:3000/posts/3',
                // 设置请求体对象
                data:{
                    title:"sunny day",
                    author:"you"
                }
            }).then(response=>{
                console.log(response)
            })
        }
        btns[3].onclick = function (){
            // 发送AJAX请求
            axios({
                // 请求资源
                method:'DELETE',
                // URL
                url:'http://localhost:3000/posts/3',
            }).then(response=>{
                console.log(response)
            })
        }
```

### 3.axios的其他使用方法

```javascript
const btns = document.querySelectorAll('button')
    btns[0].onclick = function () {
        // 发送AJAX请求
        axios.request({
            method: 'GET',
            url: 'http://localhost:3000/comments'
        }).then(response => {
            console.log(response)
        })
    }
    btns[1].onclick = function () {
        // 发送AJAX请求
        axios.post(
            'http://localhost:3000/comments',
            {
                "body": "123123",
                "postId": 2
            }
        ).then(response => {
            console.log(response)
        })
    }
```

### 4.axios返回成功的结果

```http
// 成功之后的返回结果 是一个response对象
{data: {…}, status: 201, statusText: 'Created', headers: i, config: {…}, …}
config: {transitional: {…}, adapter: Array(2), transformRequest: Array(1), transformResponse: Array(1), timeout: 0, …}
// data是一个对象
data: {body: '123123', postId: 2, id: 3}
// 头信息
headers: i {cache-control: 'no-cache', content-length: '48', content-type: 'application/json; charset=utf-8', expires: '-1', location: 'http://localhost:3000/comments/3', …}
// 原生AJAX请求对象
request: XMLHttpRequest {onreadystatechange: null, readyState: 4, timeout: 0, withCredentials: false, upload: XMLHttpRequestUpload, …}
// 响应状态码
status: 201
statusText: "Created"
[[Prototype]]: Object

```

### 5.axios默认配置

```javascript
axios.defaults.method='GET'
axios.defaults.baseURL='http://localhost:3000'
axios.defaults.headers = {'X-Requested-With':'XMLHttpRequest'}
axios.defaults.params = {id:100}
axios.defaults.timeout = 3000 //ms
btns[0].onclick = function () {
    // 发送AJAX请求
    axios({
        url: '/posts/1'
    }).then(response => {
        console.log(response)
    })
}
```

### 6.axios创建实例对象发送（有多个目标地址的时候）

```javascript
// 创建实例对象 与axios几乎一样
const obj = axios.create({
    baseURL: 'http://localhost:3000',
    timeout: 3000
})
obj({
    url: '/posts/1'
}).then(response => {
    console.log(response)
})
obj.get('/posts/1').then(response => {
    console.log(response)
})
```

### 7.拦截器

* 请求拦截器
* 响应拦截器

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
