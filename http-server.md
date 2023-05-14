#### 创建一个 HTTP 服务

```javascript
const http = require('http')
const server = http.createServer((req, res) => {
  res.end('hello')
})
server.listen(3000, (req, res) => {
  console.log('server runing port by 3000')
})
```

#### 处理 GET 请求的 query 参数

```javascript
const http = require('http')
const url = require('url')
const querystring = require('querystring')
// 获取query
function getQuery (query) {
  const params = {}
  const queryParts = query.split('&');
  for (let i = 0; i < queryParts.length; i++) {
    const queryParam = queryParts[i].split('=');
    params[queryParam[0]] = queryParam[1];
  } 
  return params
}
const server = http.createServer((req, res) => {
  const method = req.method // 请求类型
  const url = req.url // 请求的路径
  if (method === 'GET') {
    /*
    	queryfn.get(属性) 获取query的参数
        */
    const queryfn = new URLSearchParams(url.split('?')[1])
    /*
        	url.parse 被视为旧版
            query.(属性) 获取query的参数
        */
    const query = url.parse(url, true).query
    /** 
            querystring API 被视为旧版的。
            虽然它仍在维护，但是新的代码应该改为使用 <URLSearchParams> API。
            q.(属性) 获取query的参数
        */
    const q = querystring.parse(url.split('?')[1])
  }

  res.end('hello')
})
server.listen(3000, (req, res) => {
  console.log('server runing port by 3000')
})
```

#### 处理 POST 请求的 body 参数

```javascript
const http = require('http')
const url = require('url')
const querystring = require('querystring')
const server = http.createServer((req, res) => {
  const method = req.method // 请求类型
  const url = req.url // 请求的路径
  if (method === 'POST') {
    console.log(req.headers['Content-type'])
    // 接收数据
    let data = ''
    req.on('data', (chunk) => {
      data += chunk.toString()
    })
    req.on('end', () => {
      console.log('接收完成', data)
      res.end(data)
    })
  }
})
server.listen(3000, (req, res) => {
  console.log('server runing port by 3000')
})
```

#### 处理 HTTP 请求的综合示例

```javascript
const http = require('http')
const url = require('url')

const server = http.createServer((req, res) => {
    const method = req.method // 请求类型
    const url = req.url // 请求的路径
    const query = url.parse(url, true).query

    // 设置返回格式为JSON
    res.setHeader('Content-type'. 'application/json')
    if (method === 'GET') {
        res.end(JSON.stringify(query))
    }
    if (method === 'POST') {
        let data =''
        res.on('data', chunk => {
            dta += chunk.toString()
        })
        res.on('end', () => {
            res.end(data)
        })
    }


})
server.listen(3000, (req, res) => {
    console.log('server runing port by 3000')
})
```

#### HTTP-SERVER 代码结构

##### server.js

```javascript
const http = require('http')
const serverhandle = require('./app')

const server = http.createServer(serverhandle)
const PORT = 3001
console.log(process.env.NODE_ENV)
server.listen(PORT, () => {
  console.log('server runing:' + PORT)
})
```

##### app.js

```javascript
const handleBlogRouter = require('./src/router/blog')
const handleUserRouter = require('./src/router/user')
const url = require('url')

// 获取body数据
function getBodyData(req) {
  return new Promise((resolve, reject) => {
    if (req.headers['content-type'] !== 'application/json') {
      resolve({})
      return
    }
    let bodyData = ''
    req.on('data', (chunk) => {
      bodyData += chunk.toString()
    })
    req.on('end', () => {
      bodyData ? resolve(JSON.parse(bodyData)) : resolve({})
    })
  })
}

async function serverhandle(req, res) {
  // 设置返回数据格式：JSON
  res.setHeader('Content-type', 'application/json')
  // 设置path
  req.path = req.url.split('?')[0]
  // 解析设置query
  req.query = url.parse(req.url, true).query
  // 设置bodyData
  req.bodyData = await getBodyData(req)
  console.log(req.bodyData)
  // 处理路由
  const blogData = handleBlogRouter(req, res)
  if (blogData) {
    res.end(JSON.stringify(blogData))
    return
  }
  const userData = handleUserRouter(req, res)
  if (userData) {
    res.end(JSON.stringify(userData))
    return
  }
  // 404处理
  res.writeHead(404)
  res.end(
    JSON.stringify({
      error: '404 Not Found!'
    })
  )
}

module.exports = serverhandle
```

##### src/router/blog.js

```javascript
const controller = require('../controller/blog')
const { SuccessModel, ErrorModel } = require('../model/resModel')

function handleBlogRouter(req, res) {
  const method = req.method
  // 列表
  if (method === 'GET' && req.path === '/api/blog/list') {
    const data = controller.list(req, res)
    return new SuccessModel(data)
  }
  // 详细
  if (method === 'GET' && req.path === '/api/blog/detail') {
    return {
      msg: '/api/blog/detail'
    }
  }
  // 新增
  if (method === 'POST' && req.path === '/api/blog/new') {
    return {
      msg: '/api/blog/new'
    }
  }
  // 修改
  if (method === 'POST' && req.path === '/api/blog/update') {
    return {
      msg: '/api/blog/update'
    }
  }
  // 删除
  if (method === 'POST' && path === '/api/blog/del') {
    return {
      msg: '/api/blog/del'
    }
  }
}

module.exports = handleBlogRouter
```

##### src/model/resModel.js

```javascript
class BaseModel {
  constructor(data, message) {
    if (typeof data === 'string') {
      this.message = data
      return
    }
    if (data) {
      this.data = data
    }
    if (message) {
      this.message = message
    }
  }
}

class SuccessModel extends BaseModel {
  constructor(data, message) {
    super(data, message)
    this.errno = 0
  }
}

class ErrorModel extends BaseModel {
  constructor(data, message) {
    super(data, message)
    this.errno = -1
  }
}

module.exports = { SuccessModel, ErrorModel }

```

##### src/controller/blog.js

```javascript
exports.list = (req, res) => {
  return [
    {
      title: '标题1',
      createtime: new Date()
    },
    {
      title: '标题2',
      createtime: new Date()
    }
  ]
}
```

##### src/conf/db.js

```javascript
const env = process.env.NODE_ENV // 环境变量

let MYSQL_CONF
let REDIS_CONF

if (env === 'development') {
  MYSQL_CONF = {
    host: 'localhost',
    user: 'root',
    port: '3306',
    password: '123456',
    database: 'myblog',
    waitForConnections: true
  }
  REDIS_CONF = {
    host: 'localhost',
    user: '',
    port: '6379',
    password: ''
  }
}
if (env === 'production') {
  MYSQL_CONF = {
    host: 'localhost',
    user: 'root',
    port: '3306',
    password: '123456',
    database: 'myblog'
  }
  REDIS_CONF = {
    host: 'localhost',
    user: '',
    port: '6379',
    password: ''
  }
}

module.exports = {
  MYSQL_CONF,
  REDIS_CONF
}

```

##### src/db/mysql.js

```javascript
const mysql = require('mysql')
const { MYSQL_CONF } = require('../conf/db')

// 创建链接对象
const connection = mysql.createConnection(MYSQL_CONF)

// 开始链接
connection.connect()

// 统一执行sql的函数
function exec (sql) {
  return new Promise((resolve, reject) => {
    connection.query(sql, (error, res) => {
      if (error) {
        return reject(error)
      }
      resolve(res)
    })
  })
}

module.export = { exec }
```

##### src/db/redis.js

```javascript
// redis4.6.6
const { createClient } = require('redis')
const { REDIS_CONF } = require('../conf/db')
const { user, password, host, port } = REDIS_CONF

const PASSWORD = password ? `:${password}@` : ''
const PORT = port ? `:${port}` : ''
// 创建客户端
const redisClient = createClient({
  url: `redis://${user}${PASSWORD}${host}${PORT}`
})

// 监听错误
redisClient.on('error', (err) => console.log('Redis Client Error', err))

// 建立链接
redisClient.connect().then(() => {
  console.log('redis connect!')
})
module.exports = redisClient

```



#### cookie

* 存在在浏览器的一段字符串（最大5kb）
* 跨域不共享
* 格式：k1=v1;k2=v2;k3=v3;因此可以存储结构化数据
* 每次发送http请求，会将请求域的cookie一起发送给server
* server可以修改cookie并返回给浏览器
* 浏览器中也可以通过JavaScript修改cookie（有限制httpOnly）
  * document.cookie = "k1 = v1" 添加cookie

#### server端nodejs操作cookie

```javascript
// 设置cookie过期时间
const getCookieExpires = () => {
    const d = new Date()
    d.setTime(d.getTime() + (1000 * 60 * 60 * 24))
    return d.toGMTString()
}
const serverHandle = (req, res) => {
    // 解析cookie
    req.cookie = {}
    const cookieStr = req.headers.cookie || ''
    if (!cookieStr.trim()) return
    cookieStr.split(';').forEach(item => {
        const arr = item.trim().split('=')
        req.cookie[arr[0]] = arr[1]
    })
    // 操作cookie
    res.setHeader('Set-Cookie', `username=xxx;path=/;httpOnly; expires=${getCookieExpires()}`)
}
```

#### session原理

1. 当用户第一次访问网站时,服务器会创建一个 Session 对象,生成一个 Session ID 发送给客户端。
2. 客户端在以后的每次请求中都发送这个 Session ID.
3. 服务器根据接收到的 Session ID 查找对应的 Session 对象,并获取其中的数据。
4. 当 Session 过期或被销毁时,服务器就会删除对应的 Session 对象。

```javascript
const SESSION_DATA = {} // 存储session对象

const serverHandle = (req, res) => {
    // 解析session
    let needSetCookie = false
    let ssid = req.cookie.ssid
    if (ssid && !SESSION_DATA[ssid]) {
        SESSION_DATA[ssid] = {}
    } else {
        ssid = `${Date.now()}_${Math.random()}`
        SESSION_DATA[ssid] = {}
        needSetCookie = true
    }
    req.session = SESSION_DATA[ssid]
    
    if (needSetCookie) {
        res.setHeader('Set-Cookie', `ssid=${ssid};path=/;httpOnly; expires=${getCookieExpires()}`)
    }
    // 登录成功设置 req.session

}
```

#### Redis(windows安装)

* https://github.com/tporadowski/redis/releases
* 打开一个 **cmd** 窗口 使用 cd 命令切换目录到 **C:\redis** 运行：redis-server.exe redis.windows.conf
* 另启一个 cmd 窗口： redis-cli.exe -h 127.0.0.1 -p 6379

#### Redis(mac安装)

```shell
# 安装redis https://redis.io/docs/getting-started/installation/install-redis-on-mac-os/
brew install redis

# 启动redis, control + c 关闭
redis-server 

# 启动redis(后台启动)
brew services start redis

# 关闭redis
brew services stop redis

# 查看reids状态
brew services info redis

# 链接redis
redis-cli
redis-cli -h 127.0.0.1 -p 6379 -a 密码

# 链接后查看redis server信息
info server

# 查看是否需要密码
CONFIG GET requirepass
# 设置密码
CONFIG SET requirepass 123456
```

#### Linux下修改redis配置文件

```shell
# 安装vim
sudo apt-get install vim

#修改配置文件
sudo vim /etc/redis/reids.conf
# 添加 bind * 让所有客户端链接redis 
# protectd-mode yes 开启安全模式
# port 6379 端口 
# requirepass 123456 开启密码
```



#### Redis客户端操作

```shell
# 查看存储的key值
127.0.0.1:6379> keys *
(empty array)
# set key value 添加键值对, redis默认有16个数据库0-15，无法修改数据库名，默认是0号数据库
127.0.0.1:6379> set k1 v1
OK
# 切换数据库 select index
127.0.0.1:6379> select 2
OK
127.0.0.1:6379[2]>
# move key db 移动数据到其他数据库
127.0.0.1:6379> move k1 2
(integer) 1
127.0.0.1:6379> select 2
OK
127.0.0.1:6379[2]> keys *
1) "k1"
127.0.0.1:6379[2]> get k1
"v1"
# flushall 清空所有数据库的数据
flushall
# flushdb 清空当前数据库的数据
flushdb
```

#### Redis数据类型操作

* 字符串strings

  ```shell
  # 设置单个值
  SET key value
  # 设置多个值
  MSET k1 v1 k2 v2
  # 设置并返回之前的值
  GETSET k1 v11
  # 获取多个值
  MGET k1 k2
  # 获取某个key的长度
  STRLEN k1
  # 获取存储值的类型
  TYPE k1
  # 删除key
  DEL k1 k2
  # 重命名
  RENAME k1 kk1
  ```

* 散列hashes

  ```shell
  # 设置hash
  HSET user username lisi age 20
  # 查看hash的key
  HKEYS user
  # 获取hash的长度
  HLEN user
  # 获取hash的值
  HGET user username
  # 获取hash多个值
  HMGET user username age
  # 获取整个hash keuy value
  HGETALL user
  # 删除hash的key
  HDEL user username
  # 删除整个hash
  DEL user
  ```

* 列表lists

  ```shell
  # 向列表添加元素:将一个新元素添加到列表的头部(RPUSH 添加到尾部)
  LPUSH list 12 23 34
  # 获取指定范围的元素
  LRANGE 0 -1
  # 向指定元素之前插入元素
  LINSERT list before 12 22
  # 获取指定位置的元素
  LINDEX list 2
  # 获取列表的长度
  LLEN list
  # 获取并移除第一个元素
  LPOP list
  # 获取并移除最后一个元素
  RPOP list
  ```

* 集合sets

  ```shell
  # 向集合中添加成员
  SADD s1 11 22 33
  # 查看集合中成员
  SMEMBERS s1
  # 获取集合中成员数
  SCARD s1
  # 随机获取一个或多个成员成员
  SRANDMEMBER s1
  # 删除某个成员
  SREM s1 33 22
  # 随机删除某个成员
  SPOP s1 
  ```

* 有序集合sorted sets

  ```shell
  # 添加一个有序集合
  ZADD z1 60 u1 80 u2 59 u3 90 u4 76 u5 99 u6
  # 返回给定范围内的排序成员，低到高,ZREVRANGE反向排序
  ZRANGE 3 6 # 第7到第4名的排序
  # 获取指定元素的排名,低到高从0开始,ZREVRANK反向排序
  ZTANK z1 u6
  # 获取成员数
  ZCARD z1
  # 修改成员的值，在原来基础上增加指定增量
  ZINCRBY z1 2 u1
  # 删除集合中成员
  ZREM z1 u3
  ```

  



#### nodejs链接redis

```javascript
// 安装redis依赖 npm install redis （"redis": "^2.8.0"）
const redis = require('redis')

// 创建客户端
const redisClient = redis.createClient(6379, '127.0.0.1')

redisClient.on('error', err => {
    console.log(err)
})

// 测试
redisClient.set('username', 'zhangsan', redis.print)
redisClient.get('username', (err, val) => {
    if (err) {
        console.log(err)
        return
    }
    console.log(val)
    // 退出
    redisClient.quit()
})

// "redis": "^4.6.6"
;(async function () {
  const redis = require('redis')

  const redisClient = redis.createClient({
    url: 'redis://127.0.0.1:6379' // redis[s]://[[username][:password]@][host][:port][/db-number]
  })

  redisClient.on('error', (err) => {
    console.log(err)
  })
  await redisClient.connect()

  await redisClient.set('username', 'lisi')
  const val = await redisClient.get('username')
  console.log(val)
  await redisClient.disconnect()
})()

// https://github.com/luin/ioredis npm install ioredis

const Redis = require("ioredis")

const redis = new Redis()
redis.set("mykey", "value")

redis.get("mykey", (err, result) => {
  if (err) {
    console.error(err);
  } else {
    console.log(result); // Prints "value"
  }
});

redis.get("mykey").then((result) => {
  console.log(result); // Prints "value"
})
```

