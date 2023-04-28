#### 创建一个HTTP服务

```javascript
const http = require('http')
const server = http.createServer((req, res) => {
	res.end('hello')
})
server.listen(3000, (req, res) => {
    console.log('server runing port by 3000')
})
```



#### 处理GET请求的query参数

```javascript
const http = require('http')
const url = require('url')
const querystring = require('querystring')
const server = http.createServer((req, res) => {
    const method = req.method // 请求类型
    const url = req.url // 请求的路径
    if (method === 'GET') {
        /*
    	queryfn.get(属性) 获取query的参数
        */
        const queryfn = new URLSearchParams(url.split('?')[1])
        /*
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



#### 处理POST请求的query参数

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



#### 处理HTTP请求的综合示例

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

