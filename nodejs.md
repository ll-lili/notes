### 全局对象

`global`是Node.js的内置对象，它提供了对Node.js中全局变量的访问。

- setTimeOut 返回的是一个对象；浏览器中返回的是一个数字
- setImmediate:  类似于setTimeOut  0
- __dirname: 获取当前模块所在的目录（绝对路径，不属于global）
- __dilename： 获取当前模块的文件路径（绝对路径，不属于global）
- Buffer: 类型化数组，继承自UInt8Array(0-255)
- process： 进程
  - cwd() 运行node命令，命令行所在的工作路径
  - argv获取命令中的所有参数（string[]）
  - exit() 强制退出node进程
  - platform 运行平台
  - kill(pic) 根据ID杀死进程
  - env 环境变量

> `globalThis`是ES2020引入的一个新特性，它提供了一种跨浏览器和Node.js环境的一致性方式来访问全局对象。在Node.js中，`globalThis`的值是`global`。

### Node模块化细节

#### 模块查找

- 绝对路径：require('d:\\\xx\\\xxx\\\x.js')
- 相对路径：./或者../,相对于当前模块，转换为绝对路径，加载模块
- 相对路径：require('jquery')
  - 检查是否是内置模块，如： fs, path等
  - 检查当前目录中的node_modules
  - 检查上级目录中的node_modules
  - 转化为绝对路径
  - 加载模块

- 后缀名：会自动补全：js, json,node,mjs的顺序
- 文件名：如果仅提供目录，不提供文件名，则自动寻找该目录中的index.js
- package.json中的main字段
  - 表示包的默认入口
  - 导入或执行包时若仅提供目录，则使用main补全入口
  - 默认值为index.js

#### module对象

记录当前模块的信息

#### require函数

require.resolve('../src') 返回一个绝对路径

> module.exports = {}   module.exports 指向的内存地址是最终导出的对象
>
> exports  = module.exports
>
> this === exports == module.exports
>
> exports不要重新赋值
>
> this始终等于exports

### Node中的ES模块化

1. 文件后缀名为.mjs

2. 或者，在最近的package.json中的type的是module

### 基本内置模块

#### os

- cpus() 获取cpun内核的信息
- tempdir()  获取临时目录
- EOL 分隔符
- arch() 获取cpu的架构名
- freemem() 获取空闲内存
- homedir() 用户目录
- hostname() 主机名

#### path

- basename('xx/a.js', '.js') 指的是路径中最后的文件 a.js,如果有第二个参数,且匹配上，去掉扩展名，则结果：a
- sep 分隔符 路径的
- delimiter 分隔，如：环境变量，process.den.PATH.split(path.delimiter )
- dirname('a/b/c/d/.a.js') 获取路径中的目录, (a/b/c/d/)
- extname('a/b/c/d/.a.js') 获取后缀名 （js）
- join('a', 'b','c','../', 'a.js') 拼接多段路径 (a/b/a.js)
- resolve(__dirname, './src/a.js') 得到绝对路径

#### url

- URL() 用法 new url.URL(url)，同url.parse(url)
- format(url.parse(url)):结果是url
- url.parse('https://www.dmzshequ.com/forum.php?mod=forumdisplay&fid=2', true).query

#### util

- promisify 回调函数转换成promise
- callbackify promise转换成回调函数
- isDeepStrictEqual(obj, obj2) 深度严格比较

### 文件I/O

#### fs模块

##### 读取文件

```js
const path = require('path')
const fs = require('fs')
const filename = path.resolve(__dirname, './1.txt')
fs.readFile(filename, 'utf-8',(error, data) => {
	console.log(data)
})

const filename = path.resolve(__dirname, './test/txt')
fs.readFile(filename, (err, data) => {
    if(err) throw err
    console.log(data.toString())
})

fs.promises.readFile(filename, 'utf-8').then(data => {
    console.log(data)
})
```

##### 写入文件

```js
const path = require('path')
const fs = require('fs')
const filename = path.resolve(__dirname, './1.txt') // 写入目录不存在会报错
fs.promises.writeFile(filename, 'abc').then(data => {
    console.log(data)
})
fs.promises.writeFile(filename, 'abc', {
    flag: 'a' // a追加内容 ， w替换
}).then(data => {
    console.log(data)
})
```

##### 获取文件或目录信息

fs.promises.stat(filename)反回：

- szie: 占用字节
- atime： 上次访问的时间
- mtime: 上次文件内容被修改的时间
- ctime: 上次文件状态被修改的时间
- birthtime： 文件创建时间
- isDirentory（）： 判断是否为目录
- isFile（）：判断是否为文件

```js
const path = require('path')
const fs = require('fs')
const filename = path.resolve(__dirname, './1.txt')
async function test () {
    const stat = await fs.promises.stat(filename)
}
test() 
// 输出
Stats {
  dev: 18467,
  mode: 33206,
  nlink: 1,
  uid: 0,
  gid: 0,
  rdev: 0,
  blksize: 4096,
  ino: 1125899908174319,
  size: 957,
  blocks: 8,
  atimeMs: 1700455608499.0798,
  mtimeMs: 1697162446086.7505,
  ctimeMs: 1697162446086.7505,
  birthtimeMs: 1697159754037.5637,
  atime: 2023-11-20T04:46:48.499Z,
  mtime: 2023-10-13T02:00:46.087Z,
  ctime: 2023-10-13T02:00:46.087Z,
  birthtime: 2023-10-13T01:15:54.038Z
}
```

##### 获取目录中子目录或文件

```js
fs.promises.readdir(path.resolve(__dirname, '../')).then((data) => {
  console.log(data)
   // [ 'dist', 'package.json', 'src', 'tsconfig.json' ]
})

```

##### 创建目录

```js
fs.promises.mkdir(path.resolve(__dirname, '../demo')).then((data) => {
  console.log(data)
})

```

#### fs模块练习

```js
const fs = require('fs')
const path = require('path')

const path = require('path')
const fs = require('fs')

class File {
  constructor(filename, name, ext, isFile, size, createTime, updateTime) {
    this.filename = filename
    this.name = name
    this.ext = ext
    this.isFile = isFile
    this.size = size
    this.createTime = createTime
    this.updateTime = updateTime
  }
  async getContent(isBuffer = false) {
    if (this.isFile) {
      if (isBuffer) {
        return await fs.promises.readFile(this.filename)
      } else {
        return await fs.promises.readFile(this.filename, 'utf-8')
      }
    } else {
      return null
    }
  }
  async getChildren() {
    if (this.isFile) {
      return []
    } else {
      let children = await fs.promises.readdir(this.filename)
      children = children.map((name) => {
        const filename = path.join(this.filename, name)
        return File.getFile(filename)
      })
      return Promise.all(children)
    }
  }
  static async getFile(filename) {
    const stat = await fs.promises.stat(filename)
    const name = path.basename(filename)
    const ext = path.extname(filename)
    const isfile = stat.isFile()
    const size = stat.size
    const createTime = new Date(stat.birthtime)
    const updateTime = stat.mtime
    return new File(filename, name, ext, isfile, size, createTime, updateTime)
  }
}

async function readDir(dirname) {
    const file = await File.getFile(dirname)
    return await file.getChildren()
}
async function test () {
    const dirname = path.resolve(__dirname, './')
    return await readDir(dirname)
}
test()
```

### 文件流

流指数据的流动，数据从一个地方缓缓的流动到另一个地方。

流是有方向的：

可度流（readable）: 数据从源头流向内存

可写流（writable）：数据从内存流向源头

双工流（duplex）： 数据既可以从源头流向内存，又可以从内存流向源头



文件流：内存数据和磁盘文件数据之间的流动

#### 文件读取流

- fs.createReadStream(path[, options])
- options:可选配置
  - encoding： 编码方式
  - start: 起始字节
  - end:结束字节
  - highWaterMark:每次的读取量，和编码有关

```JS
const fs = require('fs')
const path = require('path')
const filename = path.resolve(__dirname, './1.txt')
const rs = fs.createReadStream(filename, {
    encoding: 'utf-8',
    highWaterMark: 64*1024，
    autoclose: true // 读完后会自动关闭
})
rs.on('open', () => {
    // 文件被打开
})
rs.on('error', () => {
    // 出错了
})
// re.close() 手动关闭
rs.on('close', () => {
    // 文件关闭时触发
})
rs.on('data', (chunk) => {
    // 读取到一部分数据后会触发，反复触发
    // 事件注册后数据开始读取
})
rs.on('end', () => {
    // 全部数据读取完成
})
// rs.pause() 暂停读取，触发pause事件
// rs.resume() 恢复读取，触发resume事件
```

#### 文件写入流

- fs.createWriteStream(path[, options])
- options:
  - flags: 操作文件的方式
  - encoding：编码方式
  - start：起始字节
  - highWaterMark:每次的写入字节数

```js
const fs = require('fs')
const path = require('path')
const filename = path.resolve(__dirname, './1.txt')
const ws = fs.createWriteStream(filename, {
    flags: 'w', // w覆盖，a追加
    encoding: 'utf-8',
    highWaterMark: 2
})
ws.on('open', () => {
    // 文件被打开
})
ws.on('error', () => {
    // 出错了
})
ws.on('close', () => {
    // 文件关闭时触发
})
const flag = ws.write(data)
// 写入数据,返回布尔值
// 当写入列队清空时，会触发drain事件

// ws.end([data]) // 结束写入，将自动关闭文件，data可选，表示关闭前的最后一次写入


```

#### pipe管道

- 将可读流连接到可写流
- 返回参数的值
- 该方法可以解决背压问题

```js
const fs = reuqire('fs')
const path = require('path')

const filename1 = path.resolve(__dirname, 'data.txt')
const filename2 = path.resolve(__dirname, 'data-bak.txt')

const readStream = fs.createReadStream(filename1)
const writeStream = fs.createWriteStream(filename2)

readStream.pipe(writeStream)
readStream.on('end', () => {
    console.log('copy done')
})
```

* http请求读取文件

  ```javascript
  const http = require('http')
  const filename1 = path.resolve(__dirname, 'data.txt')
  const server = http.createServer((req, res) => {
    res.setHeader('Content-type', 'text/plain;charset=utf-8')
    res.setHeader()
    const readStream = fs.createReadStream(filename1)
    readStream.pipe(res)
  })
  
  server.listen(8000)
  ```


### net模块

net是一个通信模块

利用它可以实现，进程间的通信IPC，网络通信TCP/IP

### http模块

http模块建立在net模块之上：无需手动管理socket，无需手动组装消息格式

#### http.request()

- `http.request(url[, options][, callback])`
- 请求对象：ClientRequest
- 响应对象：InComingMessage

```js
const http = require('http')
const request = http.request(url, {metod: 'GEt'}, resp => {
    console.log(resp.statusCode) // 服务器响应状态码
    let str = ''
      resp.on('data', (chunk) => {
        // console.log(`BODY: ${chunk}`)
        str += chunk
      })
      resp.on('end', () => {
        console.log(str, 'No more data in response.')
      })
})
// request.write()  // 写入消息体
request.end() // 表示消息体结束
```

#### http.createServer()

- `http.createServer([options][, requestListener])`
- 请求对象：InComingMessage
- 响应对象：ServerResponse

```js
const http = require('http')
const server = http.createServer((req, res) => {
  // 处理请求
  console.log(req.url)
  console.log(req.headers)
  console.log(req.method)
  let body = ''
  req.on('data', (chunk) => {
    body += chunk.toString('utf-8')
  })
  req.on('end', () => {
    console.log(body)
  })
  // 做出响应
  res.setHeader('a', 1)
  res.write('hello')
  res.end()
})
server.listen(10086, () => {
  console.log('port: 10086')
})
```

####  静态服务器demo

```js
const http = require('http')
const URL = require('url')
const path = require('path')
const fs = require('fs')

// 获取文件是否存在
async function getFileStat(filename) {
  try {
    return await fs.promises.stat(filename)
  } catch (error) {
    console.log(error, 'error')
    return null
  }
}
// 获取文件
async function getFileContent(req) {
  const host = 'http://' + req.headers.host
  const urlObj = new URL.URL(req.url, host)
  const isStatic = urlObj.pathname.split('/')[1] === 'static'
  let filename = path.resolve(__dirname, req.url.substring(1))
  // 是否访问静态文件
  if (isStatic) {
    // 文件是否存在
    const stat = await getFileStat(filename)
    // 不存在
    if (!stat) {
      return null
    } else if (stat.isDirectory()) {
      // 是否是目录
      filename = path.resolve(__dirname, req.url.substring(1), 'index.html')
      const stat = await getFileStat(filename)
      if (!stat) {
        return null
      } else {
        return await fs.promises.readFile(filename)
      }
    } else {
      // 文件存在
      return await fs.promises.readFile(filename)
    }
  } else {
    return null
  }
}

// 请求处理
async function handler(req, res) {
  const content = await getFileContent(req)
  if (content) {
    res.end(content)
  } else {
    res.statusCode = 405
    res.end('file is not found')
  }
}
const server = http.createServer((req, res) => {
  handler(req, res)
})

server.listen(10086, () => {
  console.log('listen port: 10086')
})

```

### https协议

https保证数据在传输过程中不被窃取和篡改，从而保证传输安全。

- 服务器：申请证书
- 客户端：访问时，使用https://
- https的默认端口是443

#### 加密

##### 对称加密

- 产生一个密钥，可以用其加密，也可以用其解密。
- 常用算法：DES 、DES、AES、Blowfish...

##### 非对称加密

- 产生一堆密钥，一个用于加密一个用于解密。
- 常用算法：RSA

### node生命周期（事件循环）

位置1）timers队列：存放计时器的回调函数

位置2）poll轮询队列：除了timers 、checks，绝大部分回调都会放入该队系列（文件读取，监听用户请求）

位置3）poll运作方式：

- 如果poll中有回调，依次执行回调，知道清空队列
- 如果poll中没有回调，等待其他队列出现回调，结束该阶段，进入下一阶段。

check检查阶段： 使用setImmediate的回调会直接进入这个队列

> 事件循环中，每次打算执行一个回调之前，必须先要清空nextTick和promise队列