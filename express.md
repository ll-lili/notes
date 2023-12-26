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
// res对象自有cookie方法设置cookie
app.post('/', (req, res) => {
    res.cookie('token', token,{
        path: '/',
        domain: 'localhost',
        maxAge: 3600 * 1000 // 毫秒
    })
})
```

#### [connect-history-api-fallback 中间件](https://github.com/bripkens/connect-history-api-fallback)

```shell
npm install --save connect-history-api-fallback
```



```js
var history = require('connect-history-api-fallback');
var express = require('express');

var app = express();
app.use(history());
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

### cookie

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
### session

1. 当用户第一次访问网站时,服务器会创建一个 Session 对象,生成一个 Session ID 发送给客户端(保存在cookie)。
2. 客户端在以后的每次请求中都发送这个 Session ID.
3. 服务器根据接收到的 Session ID 查找对应的 Session 对象,并获取其中的数据。
4. 当 Session 过期或被销毁时,服务器就会删除对应的 Session 对象。

#### express-session中间件

```shell
npm install express-session
```

```js
const session = require('express-session')
app.use(session())
```
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

### 跨域之CROS

`CORS`是基于`http1.1`的一种跨域解决方案，它的全称是**C**ross-**O**rigin **R**esource **S**haring，跨域资源共享。

它的总体思路是：**如果浏览器要跨域访问服务器的资源，需要获得服务器的允许**

[![image-20200421152122793](https://camo.githubusercontent.com/bc2c64a42e8ccbb50d33ef6dc0a35d287f68496998d06eddbe3e2dbd15acb213/687474703a2f2f6d6472732e7975616e6a696e2e746563682f696d672f696d6167652d32303230303432313135323132323739332e706e67)](https://camo.githubusercontent.com/bc2c64a42e8ccbb50d33ef6dc0a35d287f68496998d06eddbe3e2dbd15acb213/687474703a2f2f6d6472732e7975616e6a696e2e746563682f696d672f696d6167652d32303230303432313135323132323739332e706e67)

而要知道，一个请求可以附带很多信息，从而会对服务器造成不同程度的影响

比如有的请求只是获取一些新闻，有的请求会改动服务器的数据

针对不同的请求，CORS规定了三种不同的交互模式，分别是：

- **简单请求**
- **需要预检的请求**
- **附带身份凭证的请求**

这三种模式从上到下层层递进，请求可以做的事越来越多，要求也越来越严格。

```js
const allowOrigins = ["http://127.0.0.1:5500", "null"];

module.exports = function (req, res, next) {
  //处理预检请求
  if (req.method === "OPTIONS") {
    res.header(
      `Access-Control-Allow-Methods`,
      req.headers["access-control-request-method"]
    );
    res.header(
      `Access-Control-Allow-Headers`,
      req.headers["access-control-request-headers"]
    );
  }
  res.header("Access-Control-Allow-Credentials", true)
  //处理简单请求
  if ("origin" in req.headers && allowOrigins.includes(req.headers.origin)) {
    res.header("access-control-allow-origin", req.headers.origin);
  }
  next();
};
```



下面分别说明三种请求模式的具体规范。

#### 简单请求

 当浏览器端运行了一段ajax代码（无论是使用XMLHttpRequest还是fetch api），浏览器会首先判断它属于哪一种请求模式

##### 简单请求的判定

当请求**同时满足**以下条件时，浏览器会认为它是一个简单请求：

1. **请求方法属于下面的一种：**
   - get
   - post
   - head
2. **请求头仅包含安全的字段，常见的安全字段如下：**
   - `Accept`
   - `Accept-Language`
   - `Content-Language`
   - `Content-Type`
   - `DPR`
   - `Downlink`
   - `Save-Data`
   - `Viewport-Width`
   - `Width`
3. **请求头如果包含`Content-Type`，仅限下面的值之一：**
   - `text/plain`
   - `multipart/form-data`
   - `application/x-www-form-urlencoded`

如果以上三个条件同时满足，浏览器判定为简单请求。

下面是一些例子：

```js
// 简单请求
fetch("http://crossdomain.com/api/news");

// 请求方法不满足要求，不是简单请求
fetch("http://crossdomain.com/api/news", {
  method:"PUT"
})

// 加入了额外的请求头，不是简单请求
fetch("http://crossdomain.com/api/news", {
  headers:{
    a: 1
  }
})

// 简单请求
fetch("http://crossdomain.com/api/news", {
  method: "post"
})

// content-type不满足要求，不是简单请求
fetch("http://crossdomain.com/api/news", {
  method: "post",
  headers: {
    "content-type": "application/json"
  }
})
```

##### 简单请求的交互规范

当浏览器判定某个**ajax跨域请求**是**简单请求**时，会发生以下的事情

1. **请求头中会自动添加`Origin`字段**

比如，在页面`http://my.com/index.html`中有以下代码造成了跨域

```
// 简单请求
fetch("http://crossdomain.com/api/news");
```



请求发出后，请求头会是下面的格式：

```
GET /api/news/ HTTP/1.1
Host: crossdomain.com
Connection: keep-alive
...
Referer: http://my.com/index.html
Origin: http://my.com
```



看到最后一行没，`Origin`字段会告诉服务器，是哪个源地址在跨域请求

1. **服务器响应头中应包含`Access-Control-Allow-Origin`**

当服务器收到请求后，如果允许该请求跨域访问，需要在响应头中添加`Access-Control-Allow-Origin`字段

该字段的值可以是：

- *：表示我很开放，什么人我都允许访问
- 具体的源：比如`http://my.com`，表示我就允许你访问

> 实际上，这两个值对于客户端`http://my.com`而言，都一样，因为客户端才不会管其他源服务器允不允许，就关心自己是否被允许
>
> 当然，服务器也可以维护一个可被允许的源列表，如果请求的`Origin`命中该列表，才响应`*`或具体的源
>
> **为了避免后续的麻烦，强烈推荐响应具体的源**

假设服务器做出了以下的响应：

```
HTTP/1.1 200 OK
Date: Tue, 21 Apr 2020 08:03:35 GMT
...
Access-Control-Allow-Origin: http://my.com
...

消息体中的数据
```



当浏览器看到服务器允许自己访问后，高兴的像一个两百斤的孩子，于是，它就把响应顺利的交给js，以完成后续的操作

下图简述了整个交互过程

[![image-20200421162846480](https://camo.githubusercontent.com/8e2a93c0bb762c0f189f245ef9780dc1db4bce108b836fd8a090525f9938d8f0/687474703a2f2f6d6472732e7975616e6a696e2e746563682f696d672f696d6167652d32303230303432313136323834363438302e706e67)](https://camo.githubusercontent.com/8e2a93c0bb762c0f189f245ef9780dc1db4bce108b836fd8a090525f9938d8f0/687474703a2f2f6d6472732e7975616e6a696e2e746563682f696d672f696d6167652d32303230303432313136323834363438302e706e67)

#### 需要预检的请求

简单的请求对服务器的威胁不大，所以允许使用上述的简单交互即可完成。

但是，如果浏览器不认为这是一种简单请求，就会按照下面的流程进行：

1. **浏览器发送预检请求，询问服务器是否允许**
2. **服务器允许**
3. **浏览器发送真实请求**
4. **服务器完成真实的响应**

比如，在页面`http://my.com/index.html`中有以下代码造成了跨域

```
// 需要预检的请求
fetch("http://crossdomain.com/api/user", {
  method:"POST", // post 请求
  headers:{  // 设置请求头
    a: 1,
    b: 2,
    "content-type": "application/json"
  },
  body: JSON.stringify({ name: "袁小进", age: 18 }) // 设置请求体
});
```



浏览器发现它不是一个简单请求，则会按照下面的流程与服务器交互

1. **浏览器发送预检请求，询问服务器是否允许**

```
OPTIONS /api/user HTTP/1.1
Host: crossdomain.com
...
Origin: http://my.com
Access-Control-Request-Method: POST
Access-Control-Request-Headers: a, b, content-type
```



可以看出，这并非我们想要发出的真实请求，请求中不包含我们的响应头，也没有消息体。

这是一个预检请求，它的目的是询问服务器，是否允许后续的真实请求。

预检请求**没有请求体**，它包含了后续真实请求要做的事情

预检请求有以下特征：

- 请求方法为`OPTIONS`
- 没有请求体
- 请求头中包含
  - `Origin`：请求的源，和简单请求的含义一致
  - `Access-Control-Request-Method`：后续的真实请求将使用的请求方法
  - `Access-Control-Request-Headers`：后续的真实请求会改动的请求头

1. **服务器允许**

服务器收到预检请求后，可以检查预检请求中包含的信息，如果允许这样的请求，需要响应下面的消息格式

```
HTTP/1.1 200 OK
Date: Tue, 21 Apr 2020 08:03:35 GMT
...
Access-Control-Allow-Origin: http://my.com
Access-Control-Allow-Methods: POST
Access-Control-Allow-Headers: a, b, content-type
Access-Control-Max-Age: 86400
...
```



对于预检请求，不需要响应任何的消息体，只需要在响应头中添加：

- `Access-Control-Allow-Origin`：和简单请求一样，表示允许的源
- `Access-Control-Allow-Methods`：表示允许的后续真实的请求方法
- `Access-Control-Allow-Headers`：表示允许改动的请求头
- `Access-Control-Max-Age`：告诉浏览器，多少秒内，对于同样的请求源、方法、头，都不需要再发送预检请求了

1. **浏览器发送真实请求**

预检被服务器允许后，浏览器就会发送真实请求了，上面的代码会发生下面的请求数据

```
POST /api/user HTTP/1.1
Host: crossdomain.com
Connection: keep-alive
...
Referer: http://my.com/index.html
Origin: http://my.com

{"name": "袁小进", "age": 18 }
```



1. **服务器响应真实请求**

```
HTTP/1.1 200 OK
Date: Tue, 21 Apr 2020 08:03:35 GMT
...
Access-Control-Allow-Origin: http://my.com
...

添加用户成功
```



可以看出，当完成预检之后，后续的处理与简单请求相同

下图简述了整个交互过程

[![image-20200421165913320](https://camo.githubusercontent.com/9eceedaf5416e08e169a7a19c2d1567bba2f483ba8857b4625904b08620793e8/687474703a2f2f6d6472732e7975616e6a696e2e746563682f696d672f696d6167652d32303230303432313136353931333332302e706e67)](https://camo.githubusercontent.com/9eceedaf5416e08e169a7a19c2d1567bba2f483ba8857b4625904b08620793e8/687474703a2f2f6d6472732e7975616e6a696e2e746563682f696d672f696d6167652d32303230303432313136353931333332302e706e67)

#### 附带身份凭证的请求

默认情况下，ajax的跨域请求并不会附带cookie，这样一来，某些需要权限的操作就无法进行

不过可以通过简单的配置就可以实现附带cookie

```js
// xhr
var xhr = new XMLHttpRequest();
xhr.withCredentials = true;

// fetch api
fetch(url, {
  credentials: "include"
})
```



这样一来，该跨域的ajax请求就是一个*附带身份凭证的请求*

当一个请求需要附带cookie时，无论它是简单请求，还是预检请求，都会在请求头中添加`cookie`字段

而服务器响应时，需要明确告知客户端：服务器允许这样的凭据

告知的方式也非常的简单，只需要在响应头中添加：`Access-Control-Allow-Credentials: true`即可

对于一个附带身份凭证的请求，若服务器没有明确告知，浏览器仍然视为跨域被拒绝。

另外要特别注意的是：**对于附带身份凭证的请求，服务器不得设置 `Access-Control-Allow-Origin 的值为*`**。这就是为什么不推荐使用*的原因





在跨域访问时，JS只能拿到一些最基本的响应头，如：Cache-Control、Content-Language、Content-Type、Expires、Last-Modified、Pragma，如果要访问其他头，则需要服务器设置本响应头。

`Access-Control-Expose-Headers`头让服务器把允许浏览器访问的头放入白名单，例如：

```
Access-Control-Expose-Headers: authorization, a, b
```



这样JS就能够访问指定的响应头了。

### CORS中间件

```shell
npm install cors
```

```js
app.use(cros())
```

### JWT

`json web token`

当登录成功后，服务器可以给客户端响应一个jwt：

```
HTTP/1.1 200 OK
...
set-cookie:token=jwt令牌
authorization:jwt令牌
...

{..., token:jwt令牌}
```

可以看到，jwt令牌可以出现在响应的任何一个地方，客户端和服务器自行约定即可。

当客户端拿到令牌后，它要做的只有一件事：存储它。

你可以存储到任何位置，比如手机文件、PC文件、localstorage、cookie

当后续请求发生时，你只需要将它作为请求的一部分发送到服务器即可。

虽然jwt没有明确要求应该如何附带到请求中，但通常我们会使用如下的格式：

```js
GET /api/resources HTTP/1.1
...
authorization: bearer jwt令牌
...
```

#### 令牌的组成

为了保证令牌的安全性，jwt令牌由三个部分组成，分别是：

1. header：令牌头部，记录了整个令牌的类型和签名算法
2. payload：令牌负荷，记录了保存的主体信息，比如你要保存的用户信息就可以放到这里
3. signature：令牌签名，按照头部固定的签名算法对整个令牌进行签名，该签名的作用是：保证令牌不被伪造和篡改

它们组合而成的完整格式是：`header.payload.signature`

##### header

它是令牌头部，记录了整个令牌的类型和签名算法

它的格式是一个`json`对象，如下：

```
{
  "alg":"HS256",
  "typ":"JWT"
}
```



该对象记录了：

- alg：signature部分使用的签名算法，通常可以取两个值
  - HS256：一种对称加密算法，使用同一个秘钥对signature加密解密
  - RS256：一种非对称加密算法，使用私钥加密，公钥解密
- typ：整个令牌的类型，固定写`JWT`即可

设置好了`header`之后，就可以生成`header`部分了

具体的生成方式及其简单，就是把`header`部分使用`base64 url`编码即可

> `base64 url`不是一个加密算法，而是一种编码方式，它是在`base64`算法的基础上对`+`、`=`、`/`三个字符做出特殊处理的算法
>
> 而`base64`是使用64个可打印字符来表示一个二进制数据，具体的做法参考[百度百科](https://baike.baidu.com/item/base64/8545775?fr=aladdin)

浏览器提供了`btoa`函数，可以完成这个操作：

```
window.btoa(JSON.stringify({
  "alg":"HS256",
  "typ":"JWT"
}))
// 得到字符串：eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9
```



同样的，浏览器也提供了`atob`函数，可以对其进行解码：

```
window.atob("eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9")
// 得到字符串：{"alg":"HS256","typ":"JWT"}
```



> nodejs中没有提供这两个函数，可以安装第三方库`atob`和`bota`搞定
>
> 或者，手动搞定

##### payload

这部分是jwt的主体信息，它仍然是一个JSON对象，它可以包含以下内容：

```
{
  "ss"："发行者",
	"iat"："发布时间",
	"exp"："到期时间",
	"sub"："主题",
	"aud"："听众",
	"nbf"："在此之前不可用",
  "jti"："JWT ID"
}
```



以上属性可以全写，也可以一个都不写，它只是一个规范，就算写了，也需要你在将来验证这个jwt令牌时手动处理才能发挥作用

上述属性表达的含义分别是：

- ss：发行该jwt的是谁，可以写公司名字，也可以写服务名称
- iat：该jwt的发放时间，通常写当前时间的时间戳
- exp：该jwt的到期时间，通常写时间戳
- sub：该jwt是用于干嘛的
- aud：该jwt是发放给哪个终端的，可以是终端类型，也可以是用户名称，随意一点
- nbf：一个时间点，在该时间点到达之前，这个令牌是不可用的
- jti：jwt的唯一编号，设置此项的目的，主要是为了防止重放攻击（重放攻击是在某些场景下，用户使用之前的令牌发送到服务器，被服务器正确的识别，从而导致不可预期的行为发生）



当用户登陆成功之后，我可能需要把用户的一些信息写入到jwt令牌中，比如用户id、账号等等（密码就算了😳）

其实很简单，payload这一部分只是一个json对象而已，你可以向对象中加入任何想要加入的信息

比如，下面的json对象仍然是一个有效的payload

```
{
  "foo":"bar",
  "iat":1587548215
}
```



`foo: bar`是我们自定义的信息，`iat: 1587548215`是jwt规范中的信息

最终，payload部分和header一样，需要通过`base64 url`编码得到：

```
window.btoa(JSON.stringify({
  "foo":"bar",
  "iat":1587548215
}))
// 得到字符串：eyJmb28iOiJiYXIiLCJpYXQiOjE1ODc1NDgyMTV9
```

##### signature

这一部分是jwt的签名，正是它的存在，保证了整个jwt不被篡改

这部分的生成，是对前面两个部分的编码结果，按照头部指定的方式进行加密

比如：头部指定的加密方法是`HS256`，前面两部分的编码结果是`eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmb28iOiJiYXIiLCJpYXQiOjE1ODc1NDgyMTV9`

则第三部分就是用对称加密算法`HS256`对字符串`eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmb28iOiJiYXIiLCJpYXQiOjE1ODc1NDgyMTV9`进行加密，当然你得指定一个秘钥，比如`shhhhh`

```
HS256(`eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmb28iOiJiYXIiLCJpYXQiOjE1ODc1NDgyMTV9`, "shhhhh")
// 得到：BCwUy3jnUQ_E6TqCayc7rCHkx-vxxdagUwPOWqwYCFc
```



最终，将三部分组合在一起，就得到了完整的jwt

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmb28iOiJiYXIiLCJpYXQiOjE1ODc1NDgyMTV9.BCwUy3jnUQ_E6TqCayc7rCHkx-vxxdagUwPOWqwYCFc
```



由于签名使用的秘钥保存在服务器，这样一来，客户端就无法伪造出签名，因为它拿不到秘钥。

换句话说，之所以说无法伪造jwt，就是因为第三部分的存在。

而前面两部分并没有加密，只是一个编码结果而已，可以认为几乎是明文传输

> 这不会造成太大的问题，因为既然用户登陆成功了，它当然有权力查看自己的用户信息
>
> 甚至在某些网站，用户的基本信息可以被任何人查看
>
> 你要保证的，是不要把敏感的信息存放到jwt中，比如密码

jwt的`signature`可以保证令牌不被伪造，那如何保证令牌不被篡改呢？

比如，某个用户登陆成功了，获得了jwt，但他人为的篡改了`payload`，比如把自己的账户余额修改为原来的两倍，然后重新编码出`payload`发送到服务器，服务器如何得知这些信息被篡改过了呢？

令牌在服务器组装完成后，会以任意的方式发送到客户端

客户端会把令牌保存起来，后续的请求会将令牌发送给服务器

而服务器需要验证令牌是否正确，如何验证呢？

首先，服务器要验证这个令牌是否被篡改过，验证方式非常简单，就是对`header+payload`用同样的秘钥和加密算法进行重新加密

然后把加密的结果和传入jwt的`signature`进行对比，如果完全相同，则表示前面两部分没有动过，就是自己颁发的，如果不同，肯定是被篡改过了。

```
传入的header.传入的payload.传入的signature
新的signature = header中的加密算法(传入的header.传入的payload, 秘钥)
验证：新的signature == 传入的signature
```

当令牌验证为没有被篡改后，服务器可以进行其他验证：比如是否过期、听众是否满足要求等等，这些就视情况而定了

注意：这些验证都需要服务器手动完成，没有哪个服务器会给你进行自动验证，当然，你可以借助第三方库来完成这些操作

##### 总结

最后，总结一下jwt的特点：

- jwt本质上是一种令牌格式。它和终端设备无关，同样和服务器无关，甚至与如何传输无关，它只是规范了令牌的格式而已
- jwt由三部分组成：header、payload、signature。主体信息在payload
- jwt难以被篡改和伪造。这是因为有第三部分的签名存在。

### 登录和认证

#### 服务端

```shell
npm install jsonwebtoken
```

```js
// jwt.js
const jwt = require('jsonwebtoken')
const secretKey = 'china'
exports.createToken = (res, info = {}, maxAge = 60 * 60) => {
  const token = jwt.sign(info, secretKey, { expiresIn: maxAge })
  res.cookie('authorization', token, {
    maxAge: maxAge * 1000,
    path: '/'
  })
  res.set('authorization', token)
  return token
}
exports.verify = (token) => {
  try {
    const res = jwt.verify(token, secretKey)
    return res
  } catch (error) {
    console.log(error)
    return null
  }
}


```

```js
// verifytoken.js 中间件
const { verify } = require('../utils/jwt')
module.exports = (req, res, next) => {
  let token = req.cookies.authorization
  if (!token) {
    token = req.headers.authorization
  }
  // 空token验证
  if (!token) {
    res.status(403).send({
      code: 403,
      msg: '请传入token'
    })
    return
  }
  // token不为空
  token = token.split(' ')
  token = token.length === 1 ? token[0] : token[1]
  const result = verify(token)
  if (result) {
    // token验证通过
    req.user = { ...result }
    next()
  } else {
    //无效的token验证
    res.status(401).send({
      code: 401,
      msg: '无效token'
    })
  }
}
```

#### 客户端

```js
//1. 发送请求的时候，如果有token，需要附带到请求头中
//2. 响应的时候，如果有token，保存token到本地（localstorage）
//3. 响应的时候，如果响应的消息码是403（没有token，token失效），在本地删除token
import axios from "axios";

export default function() {
  //1. 发送请求的时候，如果有token，需要附带到请求头中
  const token = localStorage.getItem("token");
  let instance = axios;
  if (token) {
    instance = axios.create({
      headers: {
        authorization: "bearer " + token,
      },
    });
  }

  instance.interceptors.response.use(
    (resp) => {
      //2. 响应的时候，如果有token，保存token到本地（localstorage）
      if (resp.headers.authorization) {
        localStorage.setItem("token", resp.headers.authorization);
      }
      return resp;
    },
    (err) => {
      //3. 响应的时候，如果响应的消息码是403（没有token，token失效），在本地删除token
      if (err.response.status === 403) {
        localStorage.removeItem("token");
      }
      return Promise.reject(err);
    }
  );

  return instance;
}
```

### 日志记录

- level日志级别：信息日志 错误日志 调试日志
- category日志分类 ：sql日志 请求日志
- appender日志出口： 写到哪里，输出格式

##### log4js

```shell
npm install log4js
```

```js
const log4js = require('log4js')
const path = require('path')
function getAppendersConf(type) {
  return {
    type: 'dateFile',
    keepFileExt: true, // 保留文件扩展名
    maxLogSize: 1024 * 100 * 24, // 文件最大字节
    filename: path.resolve(__dirname, `../logs/${type}/logging.log`),
    layout: {
      type: 'pattern',
      pattern: '%c [%p] [%h] %d{yyyy-MM-dd hh:mm:ss} "%m"%n'
    },
    daysTokeep: 7
  }
}
log4js.configure({
  // 日志类型
  categories: {
    api: {
      appenders: ['api'],
      level: 'all'
    },
    sql: {
      appenders: ['sql'],
      level: 'all'
    },
    default: {
      appenders: ['default'],
      level: 'all'
    }
  },
  // 日志出口
  appenders: {
    sql: getAppendersConf('sql'),
    api: getAppendersConf('api'),
    default: {
      type: 'stdout'
    }
  }
})
process.on('exit', () => {
  log4js.shutdown()
})
exports.sqlLogger = log4js.getLogger('sql')
exports.apiLogger = log4js.getLogger('api')
exports.defaultLogger = log4js.getLogger() // 日志分类，默认default


```

#### 中间件

```js
app.use(log4js.connectLogger(apiLogger, { level: 'auto' }))
```

