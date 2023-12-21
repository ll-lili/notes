### 安装

```shell
 npm install express --save
```

### 基本使用

```js
const express = require('express')

const app = express()
app.get('/test', (req, res) => {
  // res.redirect('https://www.baidu.com')
})
const port = 3000
app.listen(port, () => {
  console.log(`server listen on: ${port}`)
})
```

### express中间件

#### 匹配（命中）到请求后

1. 交给第一个处理函数处理
2. 函数中需要手动交给后续中间件处理（next()）

#### 中间件处理的细节

如果后续已经没有了中间件：

- express发现响应没有结束，express会响应404

如果中间件发生了错误：

- 不会停止服务器
- 相当于调用了next(错误对象)
- 寻找后续错误处理中间件：
  - 如果没有，则响应500
  - 

#### 处理错误的中间件

```js
module.exports = (err, req, res, next) => {
    if (err) {
        const msg = err instanceof Error ? err.masage : err
        res.status(500).send({
            code: 500,
            msg
        })
    } else {
        next()
    }
}
```



```js
const express = require('express')
const handleError = require('./middleware/handleError')
const app = express()
 
app.get(
  '/test',
  (req, res, next) => {
    console.log('handle1')
    throw new Error('错误')
    next()
  },
  (req, res, next) => {
    next()
    console.log('handle2')
  }
)
app.use(handleError)
const port = 3000
app.listen(port, () => {
  console.log(`server listen on: ${port}`)
})
```

### 常用中间件

#### express.static()

```js
const express = require('express')
const app = express()
const path = require('path')

/*
	请求时，会根据请求路径，从指定目录中寻找是否存在该文件；
	如果存在，直接响应文件内容，而不再移交给后续处理函数。
*/
const staticRoot = path.reoslve(__dirname, '../public')
app.use('/static',express.static(staticRoot)) // 第一个参数，基路径可选
```

#### express.urlencoded()

解析`content-type = application/x-www-form-urlencoded`的body

这种数据格式，是`form`表单的默认格式。

```js
app.use(express.urlencoded(extended: true))
/*
	The extended option allows to choose between parsing the URL-encoded data with the querystring library (when false) or the qs library (when true).
*/
```

#### express.json()

解析`content-type = application/json`的body

```js
app.use(express.json())
```

#### cookie-parser

```shell
npm install cookie-parser
```

```js
const cookieParser = require('cookie-parser')
app.use(cookieParser())
// 会在req对象中注入cookies属性，用于获取所有请求传递的cookie
// 会在res对象注入cookie方法设置cookie
app.post('/', (req, res) => {
    res.cookie('token', token,{
        path: '/',
        domain: 'localhost',
        maxAge: 3600 * 1000 // 毫秒
    })
})
```



### express路由

```js
// router/routes/student.js
const express = require('express')
const router = express.Router()
router.post('/test', (req, res) => {
  console.log(req.body)
})
router.post('/', (req, res) => {
  console.log(req.body)
})
module.exports = router

```



```js
// router/index.js
const express = require('express')
const student = require('./routes/student')

const router = express.Router()
router.use('/student', student)

module.exports = router

```



```js
// server.js
const router = require('./router')
app.use('/api/v1', router)
```

### cookie的基本概念

#### cookie的组成

每个cookie记录的信息：

- key：键
- value：值
- domian：域，表示这个cookie属于哪个网站
- path：路径，表示这个cookie是属于该网站的哪个基路径，比如/news,表示cookie属性这个路径的
- secure：是否使用安全传输
- expire：过期时间

当浏览器向服务器发送一个请求时，会携带满足条件的cookie：

- cookie没有过期
- cookie中的域可以匹配请求的服务器的域
  - 如cookie中的域是`baidu.com`,则可以匹配：`baidu.com`,`www.baidu.com`,`xx.baidu.com`
  - 如cookie中的域是`ww.baidu.com`,则只能匹配：`ww.baidu.com`
  - cookie是不在乎端口的，只要域匹配即可
- cookie中的path和这次请求的path是匹配的
  - 如果cookie的path是/，则可以匹配所有的路径

- 验证cookie的安全传输
  - 如果cookie的secure属性是true，则请求协议必须是https，否则不会发送该cookie

#### 设置cookie

由于cookie是保存在浏览器端的，同时，很多令牌证件又是服务器颁发的

所以，cookie的设置又两种方式：

- 服务器响应：这种模式非常普遍，当服务器决定给客户端颁发一个证件时，它会在响应的消息中包含cookie，浏览器会自动把cookie保存；
- 客户端端自行设置：这种模式较为少见。比如用户关闭某个广告并选择以后不在弹出，此时，就可以把这个信息直接通过js代码保存到cookie。后续请求服务器时，服务器看到客户端不想再次弹出广告的cookie，于是就不在发送广告。

##### 服务器设置cookie

服务器可以通过设置响应头，告诉浏览器应该如何设置cookie

响应头格式是设置：

```http
set-cookie: cookie1
set-cookie: cookie2
set-cookie: cookie3
```

通过这种模式，可以在一次响应中设置多个cookie

其中每cookie的格式：

```
键=值; path=?;domain=?;expire=?;max-age=?;secure;httponly
```

每个cookie处理键值是必须设置的，其他属性可选，顺序不限

当这样的响应头到达客户端后，浏览器会自动将cookie保存，如果已经存在一模一样的cookie（key,path,domain）,会自动覆盖之前的cookie。

属性说明：

- path：设置cookie路径。如果不设置，浏览器自动设置当前请求的路径。

- domian：设置cookie域。如果不设置，浏览器自动设置当前请求域。

  - 如果服务器相遇了一个无效的域，浏览器是不认的。
  - 无效域就是响应的域连根域都不一样。

- expire：设置cookie的过期时间，这里必须是一个有效的GMT时间，即格林威治时间的字符串。

  ```js
  new Data().toGMTString() // 格林威治时间的字符串
  ```

  

- max-age：设置cookie的相对有效期（单位秒）。expire和max-age通常设置一个即可。

  - 如果不设置expire，max-age，则表示当前会话有效，会话关闭后过期。
  - 对于大部分浏览器而言，关闭所有浏览器窗口意味会话结束。

- secure：设置cookie是否安全连接，如果设置了该值，则把表示后续只能随着https请求发送。

- httponly：设置cookie是否仅用于传输。如果设置该值，表示cookie仅能用于传输，而不允许在客户端通过JS获取，这对防止跨站脚本攻击（XSS）会很有用。

### crypto

node内置模块

```js
const crypto = require('crypto')

// 输出所有加密算法
console.log(crypto.getCiphers()) 
/*
[
  'aes-128-cbc',
  'aes-128-cbc-hmac-sha1',
  'aes-128-cbc-hmac-sha256',
  'aes-128-ccm',
  'aes-128-cfb',
  ... more items
]
*/
```

### 断点调试

node --inspect启动模块，node进程会监听一个端口

### 跨域之JSONP

1. 浏览器端生成一个script元素，访问数据接口
2. 服务器响应一段JS代码，调用某个函数，并把响应数据传入

```html
<script>
  function callback(data) {
      console.log(data) // jsonp data
   }
</script>
<script src="http://127.0.0.1:3001/api/v1/student/test"></script>
```

```js
router.get('/test', (req, res) => {
  const script = `callback('jsonp data')`
  res.set('content-type', 'application/javascript')
  res.send(script)
})
```

