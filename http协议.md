#### HTTP状态码

##### 状态码分类

* 1xx服务器收到请求
* 2xx请求成功，如200
* 3xx重定向，如302
* 4xx客户端错误，如404
*  5xx服务端错误，如500

##### 常见状态码

* 200成功
* 301永久重定向（配合headers:location，浏览器自动处理）
* 302临时 重定向（配合headers: location，浏览器自动处理）,如百度，知乎，短网址，308
* 304资源未被修改
* 404资源未找到
* 403没有权限 
* 500服务器错误 
* 504网关超时 

##### 关于协议和规范

* 就是一个约定
* 要求大家跟着执行 

#### http methods

##### 传统methods

* get获取服务器的数据
* post向服务器提交数据

##### 现在的methods

* get获取数据
* post新建数据
* patch/put更新数据
* delete删除数据

#### Restful API

* 一种API设计方法（规范）

* 传统API设计：把每个URL当作一个功能
* restful API设计：把每个URL当作一个唯一资源 (标识)

##### 如何设计成一个资源

* 尽量不使用URL参数

  * 传统API设计：api/list?pageIndex=2
  * Restful API设计：api/list/2
* 用method表示操作类型

  * 传统API设计

    * post请求：/api/create-blog
    * post请求：/api/update-blog?id=100
    * get请求：/api/get-blog?id=100
	* Restful API设计
	  * post请求：/api/blog
	  * patch请求：/api/blog/100
	  * get请求：/api/blog/100

#### http headers

##### Request headers

* Accept浏览器可接受的数据格式
* Accept-Encoding浏览器可接收的压缩算法，如: gzip
* Accept-Languange浏览器可接受的语言，如：zn-Ch
* Connection: keep-alive一次TPC链接重复使用
* Cookie
* Host请求域名
* User-Agent（简称UA）浏览器信息（标识）
* Content-type 发送的数据格式，如：application/json

##### Response headers

* Content-type 返回的数据格式，如：application/json
* Content-length 返回数据的大小，多少字节
* Content-Encode 返回数据的压缩算法，如：gzip
* Set-Cookie

##### 缓存相关headers



#### http 缓存

##### 强制缓存

* 初次请求返回资源和Cache-Control

* Cache-Control 在response headers中，控制强制缓存的逻辑
* Cache-Control:max-age=315360000(单位毫秒)
* Size：disk cache（磁盘高速缓存）

##### Cache-Control取值

* max-age=315360000最大过期时间
* no-cache 不需要强制缓存
* no-store 不需要强制缓存,也不需要服务端缓存(不常见)
* private 
* public

##### Expires

* 在response headers中
* 控制缓存过期
* 已经被Cache-Control取代 

##### 协商缓存（对比缓存）304

* 服务器端缓存策略（服务端判断是否可以使用缓存内容）
* 服务端判断客户端资源，是否和服务端一样（通过资源标识判断）
* 资源标识
  * 在Response Headers中，有两种
  * Last-Mondifid资源的最后修改时间
  * Etag资源的唯一标识(一个字符串，类似指纹)
  * 共存优先使用Etag，Last-Mondifid只能精确到秒
  * 如果资源重复生成，而内容不变，则Etag更精准
* 一致返回304，否则返回200和最新资源

##### 刷新的http缓存的影响

* 正常操作：强制缓存有效，协商缓存有效
* 手动刷新：强制缓存失效，协商缓存有效
* 强制刷新：都失效

#### http和https

* http是明文传输，敏感信息容易被中间劫持
* https劫持了也无法解密

##### https加密方式

* 对称加密：一个key同时负责加密和解密
* 非对称加密：一对key,a加密后只能用b解密
* https同时使用了两种加密方式

##### https证书

* 中间人攻击
* 使用第三方证书（慎用免费，不合规的证书）
* 浏览器校验证书
