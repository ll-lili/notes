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

