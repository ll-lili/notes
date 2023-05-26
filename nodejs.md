#### fs文件系统

##### 读取文件

* 回调的API

  ```javascript
  /**
  	path <string> | <Buffer> | <URL> | <integer> 文件名或文件描述符
      options <Object> | <string> 如果 options 是字符串，则它指定编码：
          encoding <string> | <null> 默认值: null
          flag <string> 请参阅对文件系统 flags 的支持。 默认值: 'r'。
          signal <AbortSignal> 允许中止正在进行的读取文件
      callback <Function>
          err <Error> | <AggregateError>
          data <string> | <Buffer>
  */
  const fs = require('fs')
  const path = require('path')
  
  const filename = path.resolve(__dirname, './test/txt')
  fs.readFile(filename, (err, data) => {
      if(err) throw err
      console.log(data.toString())
  })
  ```

##### 写入文件

* 回调的API

  ```javascript
  const fs = require('fs')
  const path = require('path')
  
  const filename = path.resolve(__dirname, './test/txt')
  const content = '写入内容\n'
  fs.writeFile(filename, content, {
      flag: 'a' // a追加，w替换
  }, (err) => {
      if (err) throw err
      console.log('写入成功')
  })
  ```

##### 检测文件是否存在(`fs.exists`弃用)

* 回调的API

  ```javascript
  const fs = require('fs')
  const path = require('path')
  
  const filename = path.resolve(__dirname, './test/txt')
  fs.access(filename, (err) => {
    console.log(`${err ? 'does not exist' : 'exists'}`)
  })
  /*
  在调用 fs.open()、fs.readFile() 或 fs.writeFile() 之前，不要使用 fs.access() 检查文件的可访问性。 
  这样做会引入竞争条件，因为其他进程可能会在两次调用之间更改文件的状态。 而是，用户代码应直接打开/读取/写入文件，并处理无法访问文件时引发的错误。
  */
  ```

  

#### stream流

* 标准输入输出

  ```javascript
  process.stdin.pipe(process.stdout)
  ```

* 复制文件

  ```javascript
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

  