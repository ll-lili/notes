###  HTMl、CSS


#### 如何理解HTML语义化？

* 代码可读性强
* 有利于搜索引擎读懂



#### margin负值问题

* margin-top 和margin-left 负值，元素向 上向左移动
* margin-right 负值，右侧元素左移，自身不受影响
* margin-bottom 负值，下方元素上移，自身不受影响



#### BFC   

* BFC形成了一种封闭的空间，一个独立渲染区域，其中的元素布局不会影响到外部元素的布局，同时也不会受到外部元素的影响。BFC可以解决一些常见的布局问题，如清除浮动、高度塌陷、margin传递给父元素。
* 使用float 使其浮动的元素    
* 绝对定位的元素。包含 position: fixed 或position: sticky
* 使用以下属性的元素 display: inline-block
* 表格单元格或使用 display: table-cell, 包括使用 display: table-* 属性的所有表格单元格
* 表格标题或使用 display: table-caption 的元素
* 块级元素的 overflow 属性不为 visible
* 元素属性为 display: flow-root 或 display: flow-root list-item（无副作用）



#### float布局

* 三栏布局，中间一栏最先加载和渲染（内容最重要）
* 两侧内容固定，中间内容随着宽度自适应
* 一般用于 PC网页

```css
/*
	圣杯布局和双飞翼布局的技术总结：
	1.使用float 布局
	2.两侧使用margin 负值，以便和中间内容横向重叠
	3.防止中间内容被两侧覆盖，一个用 padding 一个用 margin
*/
/* 圣杯布局 */
.body {
  background-color: beige;
}
.header, .footer {
  height: 50px;
  background-color: bisque;
  clear: both;
}
.container {
  padding-left: 200px;
  padding-right: 150px;
}
.container .column {
  height: 50px;
  float: left;
}
.container .center {
  background-color: aqua;
  width: 100%;
}
.container .left {
  background-color: blue;
  width: 200px;
  margin-left: -100%;
  position: relative;
  left: -200px;
}
.container .right {
  background-color: brown;
  width: 150px;
  margin-right: -150px;
}
```

```html
<!-- 圣杯布局-->
<body>
  <div class="header"></div>
  <div class="container">
    <div class="center column"></div>
    <div class="left column"></div>
    <div class="right column"></div>
  </div>
  <div class="footer"></div>
</body>
```

```css
/* 双飞翼布局 */
.col {
  float: left;
  width: 100%;
}
.main {
  height:200px;
  background-color: antiquewhite;
}
.center {
  margin: 0 200px;
}
.left {
  height: 200px;
  width: 200px;
  background-color: blue;
  margin-left: -100%;
}
.right {
  height: 200px;
  width: 200px;
  background-color: aquamarine;
  margin-left: -200px
}
```

```html
<!-- 双飞翼布局 -->
<body>
  <div class="main col">
    <div class="center">center</div>
  </div>
  <div class="left col">left</div>
  <div class="right col">right</div>
</body>
```



#### CSS水平居中

* inline 元素：text-align:center
* block元素：margin:auto
* absolute元素：left:50% + margin-left 负值



#### CSS垂直居中

* inline 元素：line-height 的值等于 height 值
* absolute 元素：top:50% + margin-top 负值
* absolute 元素：transform(-50%, -50%)
* absolute 元素：top, left, bottom, right = 0+ margin: auto



#### line-height是如何继承的？

* 写具体数值，如 30px，则继承该值 
* 写比例，如2/1.5 ，则继承该比例（继承元素font- size * 比例）
* 写百分比，如200% ，则继承计算出来的值（被继承元素font-size * 百分比）



#### 响应式布局 

* px ，绝对长度单位，最常用
* em，相对长度单位，相对于父元素，不常用
* rem，相对长度单位，相对于根元素，常用于响应式布局
* 100px等于1rem
* media-query，根据不同的屏幕宽度设置根元素 font-size
* vh 网页视口高度的 1/100
* vw网页视口宽度的 1/100
* vmax 取两者最大值；vmin 取两者最小值



### JS基础

##### 变量

* 变量存在栈中
* 值类型就是在栈中存储的具体值string，number，boolean,  symbol, undefined,不能被修改
* 引用类型在栈中存储的一个内存地址，指向堆值的对象：object, array, null(特殊引用类型，指向内存地址为空), function(特殊引用类型，不用存储数据)

##### typeof运算符

* 识别出所有值类型
* 识别出函数
* 判断是否为引用类型（不可细分，细分可以使用：Object.prototype.toString.call(值)）

##### 深拷贝

```javascript
function deepClone (obj = {}) {
  // 对传入的obj进行类型判断
  if (typeof obj !== 'object' || obj == null ) {
    return obj
  }
  // 初始化result
  let result
  if (obj instanceof Array) {
    result = []
  } else {
    result = {}
  }
  for (const key in obj) {
    // 判断key是否为obj的自身属性
    if (obj.hasOwnProperty(key)) {
      // 递归调用
      result[key] = deepClone(obj[key])
    }
  }
  return result
}

const obj1 = {
  username: 'll',
  adress: {
    city: 'shenyang'
  },
  likeNumber: [1, 2, 3]
}
const obj2 = deepClone(obj1)
obj2.adress.city = 'beijing'
console.log(obj1)
console.log(obj2)
```

##### 类型转换

* 字符串拼接

* ==

* If 和逻辑运算

  ```javascript
  // 除了==null，其他一律用===
  const obj = {}
  if (obj.a == null) {}
  // 相当于
  if （obj.a === null || obj.a === undefined){}
  ```

##### 原型

* 每个函数（包括class）都有显示原型（prototype）
* 每个对象（实例）都有隐式原型(\__proto__)
* 对象的隐式原型等于对应class的显示原型

##### 基于原型的执行规则

* 获取对象的属性或者执行方法时
* 现在自身的属性和方法中寻找
* 如果找不到，自动去\__proto__中去寻找

##### 原型链

* 每个函数（包括class）的显示原型（prototype）的隐式原型等于其父类的显示原型（prototype）
* Object.prototype是原型链的尽头，Object.prototype.\__proto__ === null
* A instance B 理解：B的显示原型是否在A的原型链上

##### 作用域（隔离变量，不同作用域下同名变量不会有冲突）

* 全局作用域
  * 全局作用域在网页运行时创建，网页关闭时销毁
  * 所有直接编写到script标签中的代码都位于全局作用域中
  * 全局作用域中的变量是全局变量，可以在任意位置访问
* 函数作用域
  * 函数作用域在函数调用时创建，调用结束后销毁
  * 函数每次调用都会产生一个全新的函数作用域
  * 函数内部的局部变量，外部无法访问
* 块级作用域（ES6）
  * 代码块执行时创建，代码块执行结束后销毁
  * 代码块内部的局部变量，外部无法访问

##### 自由变量

* 一个变量在当前作用域没有定义，但被使用了
* 向上级作用逐层查找，知道找到为止
* 如果到全局作用域没有找到，则报错：xxx is not defined
* 自由变量的查找，是在函数定义的地方，向上级作用域查找，不是在执行的地方。

##### 闭包（closure）

* 闭包如何产生？
  * 当一个嵌套的内部子函数引用嵌套的外部父函数的变量（函数）时，就产生的闭
  * 使用chrome调试查看(closure)
* 闭包是什么？
  * 理解一：闭包是嵌套的内部函数
  * 理解二：包含被引入变量的对象
  * 注意：闭包存在于嵌套的内部函数中

* 闭包产生的条件
  * 函数嵌套
  * 内部函数引入外部函数的变量（函数）

*  闭包的作用
  * 使函数内部的变量在函数执行后，仍存活于内存中(延长了局部变量的生命周期)
  * 让函数外部可以操作（读写）函数内部的数据（变量或函数）

##### this（取值是在函数执行时确定的）

* 作为普通函数
* 使用 apply bind call
* 作为对象
* 在class方法中调用
* 箭头函数

```javascript
function fn1 () {
  console.log(this)
}
fn1() // window
fn1.call({username: zhangsan}) // { username: 'zhangsan'}
const fn2 = fn1.bind({ x: 1 }) // bind 返回一个新的函数
fn2() // { x; 1}

const zhangsan = {
  name: 'zhangsan',
  sayHi () {
		console.log(this)
  },
  wait () {
    let that = this
		setTimeOut(function () {
      console.log(this)
      console.log(that)
    })
  },
  wait2 () {
		setTimeOut(() => {
			console.log(this)
    })
  }
}

zhangsan.sayHi() // zhangsan {}
zhangsan.wait() // window zhangsan {}
zhangsan.wait2() // zhangsan {}
```

##### apply/ bind /call

* apply bind call的第一个参数改变this指向的对象
* apply call 直接调用函数，bind则返回一个新函数
* bind  call 第一个参数后面的参数为函数的参数，apply的参数需要传入一个数组

##### 手写bind函数

```javascript
Function.prototype.bind2 = function (...rest) {
  //参数转换为数组
  const arr = rest
  // const arr = Array.prototype.slice.call(arguments)
  // const arr = Array.from(arguments)
  // 获取数组的第一项
  const t = arr.shift()
  const self = this // 要执行的函数
  return function () {
    return self.apply(t, arr)
  }
}
```

### JS WEB API

* DOM
* BOM
* 事件绑定
* Ajax
* 存储

#### DOM节点操作

```javascript
// 获取一个元素
const div1 = document.getElementById('div1')
const div2 = document.querySelector('#div1')
// 获取一个集合
const divList = document.getElementsByTagName('div')
console.log(divList.length) // 集合的长度
console.log(divList[0]) // 集合的第一个元素

// property 修改对象属性，不会体现到html结构中
console.log(divList[0].style.width)
console.log(divList[0].className)
console.log(divList[0].nodeName)
// 通过nodeType来判断，nodeType === 1是元素节点，nodeType===2是属性节点，nodeType===3是文本节点
console.log(divList[0].nodeType) 
// attribute 修改html属性，会改变html结构
divList[0].setArribute('data-name', 'd')
console.log(divList[0].getAttribute('data-name')) // d

// 集合
document.getElementsByClassName('.container')
document.querySelectorAll('p')


```

#### DOM结构操作

```javascript
/**
	新增和插入节点
*/
const div1 = document.getelementById('div1')
// 	添加新节点
const p1 = document.createElemet('p')
p1.innerHTML('this is p1')
div1.appendChild(p1)
// 移动已有节点
const p2 = document.getElementById('p2')
div1.applendChild(p2)

/**
	获取子元素列表
	childNodes: Nodelist类型，获取的是所有节点（包括元素节点、属性节点、文本节点）
	通过nodeType来判断，nodeType === 1是元素节点，nodeType===2是属性节点，nodeType===3是文本节点。
	
	children: 返回所有子元素的节点，HTMLCollection类型
*/
const div1 = document.getelementById('div1')

const childNodes = div1.childNodes 
const children = div1.chidlren

/**
	获取父元素
	parentNode:  Node类型，返回指定节点的父节点,如果指定节点没有父节点，则返回 null
	parentElement返回指定元素的上级元素节点，如果没有上级元素返回null
*/
const parentNode = div1.parentNode
const parentElement = div1.parentElement

/**
	获取第一个子节点
	firstChild：Node 类型，返回指定节点的第一个子节点，注意空白文本节点
	firstElementChild 返回父元素的第一个子元素节点，如果没有则返回null。ie9以上版本支持
*/
const firstChild = div1.firstChild
const firstElementChild = div1.firstElementChild

/**
	获取最后一个子节点
	lastChild：Node 类型，返回指定节点的最后一个子节点，注意空白文本节点
	lastElementChild 返回父元素的最后一个子元素节点，如果没有则返回null。ie9以上版本支持
*/
const lastChild = div1.lastChild
const lastElementChild = div1.lastElementChild

/**
	访问下一个兄弟节点
	nextSibling： Node 类型，返回指定节点之后紧跟的节点，如果没有 nextSibling 节点，则返回值为 null。
	nextElementSibling: 返回当前元素紧跟着的元素节点，如果没有则返回null。ie9以上版本支持
	
	前一个兄弟节点（previousSibling）
*/
const nextSibling = div1.nextSibling
const nextElementSibing = div2.nextElementSibling

/**
	删除节点
*/
const div1 = document.getElementById('div1')
const child = div1.childNodes[0]
div1.removeChild(child)
```

#### DOM性能

* DOM操作非常耗费CPU，内存性能，避免频繁DOM操作

* 对DOM查询做缓存

  ```javascript
  const div1 = document.getElementById('div1')
  const children = div1.childNodes
  const length = children.length
  for (let i = 0; i < length; i++) {
  	// ...
  }
  ```

  

* 将频繁操作改为一次性操作

  ```javascript
  const listNode = document.getElementById('list')
  // 创建一个文档片段，此时还没有插入到 DOM 树中
  const frag = document.createDocumentFragment()
  // 执行插入
  for(let x = 0; x < 10; x++) {
    const li = document.createElement("li")
    li.innerHTML = "List item " + X
    frag.appendChild(li)
  }
  // 都完成之后，再插入到 DOM 树中
  listNode.appendChild (frag)
  ```

#### BOM

* Navigate
* screen
* location
* History

##### navigator

```javascript
/**
	chrome: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko)  						   Chrome/112.0.0.0 Safari/537.36'
	safari: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/16.4 Safari/605.1.15
*/
const ua = navigator.userAgent
```

##### Screen

```javascript
console.log(screen.width)
console.log(screen.width)
```

##### location

```javascript
location.href // 完整地址
location.protocol // 	协议
location.host // 域名
location.search // query string ?...
location.hash // #...
location.pathname // 路径 /xx/x
```

##### history

```javascript
history.back()
history.forward()
history.of(-1)
```

#### 事件（事件对象）

- event.target：指的是触发事件的那个节点，也就是事件最初发生的节点。
- event.target.matches：可以对关键节点进行匹配，来执行相应操作。
- event.currentTarget：指的是正在执行的监听函数的那个节点。
- event.isTrusted：表示事件是否是真实用户触发。
- event.preventDefault()：取消事件的默认行为。
- event.stopPropagation()：阻止事件的派发（包括了捕获和冒泡）。
- event.stopImmediatePropagation()：阻止同一个事件的其他监听函数被调用。

##### 事件绑定

```javascript
const btn1 = document.getElementById('btn1')
btn1.addEventListener('click', function(e) {
  e.preventDefault() // 阻止默认行为
  e.stopPropagation() // 阻止冒泡
  // e.target 当前元素
  console.log(e)
})
```

##### 事件冒泡

* DOM结构由下向上逐层触发事件
* 事件捕获，addEventlistener()函数的第三参数设置为true（与事件冒泡相反）

##### 事件代理

* 将事件绑定到父元素上
* 利用事件冒泡机制，对触发的元素做判断，达到某种目的
* 避免将所有子元素都绑定事件
* 代码简洁
* 建设浏览器内存占用

```html
<div id="div1">
  <a href="#">a1</a>
  <a href="#">a2</a>
  <a href="#">a3</a>
  <a href="#">a4</a>
</div>
<button>添加一个a</button>
<script>
const div1 = document.getElementById('div1')
div1.addEventListener('click', (e) => {
  if (e.nodeName === 'A') {
    // ...
  }
})
</script>
```

##### 事件绑定函数

```javascript
function bindEvent(el, type, selector, cb) {
  if (cb == null) { // 判断第四个参数是否存在
		cb = selector
    selector = null
  }
  el.addEventListener(type, (e) => {
    const target = e.target
    if (selector) {
			// 事件代理
      if (target.macthes(selector)) {
				cb.call(target, e)
      }
    } else {
      // 普通事件绑定
      cb.call(target, e)
    }
  })
}
```

#### ajax

* XMLHttpRequest
* 状态码
* 跨域

```javascript
/**
	xhr.readystate
		0: UNSET 尚末调用 open 方法
		1: OPENED open方法己被调用 
		2: HEADERS_RECEIVED send方法已被调用，header已被接收
		3: LOADING 下载中，response Text己有部分内容
		4: DONE 下载完成
	xhr.status: htpp协议
		2xx -表示成功处理请求，如200
		3xx -需要重定向，浏览器直接跳转，如 301 302 304
		4xx -客户端请求错误，如 404 403
		5xx -服务器端错误
*/
consg xhr = new XMLHttpRequest()
xhr.open('GET', '/api/list', true) // true代表异步
xhr.onreadystatecahnge = function() {
  if (xhr.readystate === 4 && xhr.status === 200){
    console.log(xhr.responseText)
  }
}
xhr.send(null) // 发送请求,post请求参数必须是字符串
```

#### 跨域

* 同源策略
* JSONP
* CORS（服务端支持）
* 所有的跨域，都必须经过 server 端允许和配合
* 未经server 端允许就实现跨域，说明浏览器有漏洞 ，危险信号

##### 同源策略

* ajax 请求时，浏览器要求当前网页和 server 必须同源（安全）
* 同源：协议、域名、端口，三者必须一致
* 加载图片 css js 可无视同源策略

##### JSONP

* <script＞可绕过跨域限制

* 服务器可以任意动态拼接数据返回

* 所以，<script>就可以获得跨域的数据，只要服务端愿意返回

  ```html
  <script>
    window.abc = function (data) {
      console.log(data)
    }
  </script>
  <script src="http://www.demo.com:3000/api/data.js?callback=abc&name=xxx"> 
  // 返回： callback({username: 'xxx'})
  ```

##### CORS服务器设置 http header

```js
// 第二个参数填写允许跨域的域名称，不建议直接写 〝*"
response.setHeader ("Access-Control-Allow-Origin", "http: //localhost:8011");
response.setHeader("Access-Control-ALLow-Headers","X-Requested-With");
response.setHeader("Access-Control-ALLow-Methods", "PUT, POST, GET, DELETE, OPTIONS");
// 接收跨域的cookie
response.setHeader("Access-Control-AlLow-Credentials", "true");
```

#### 存储

##### cookie

* 本身用于浏览器和 server 通讯
* 被“借用〞到本地存储来
* 可用 document.cookie ='key=val'来修改(追加)
* 存储大小，最大4kb
* 发送http请求时，会携带cookie发送到服务端，增加请求的数据量

##### localStorage和sessionStorage

* HTML5 专门为存储而设计，最大可存 5M
* API简单易用 setltem getltem
* 不会随着 http 请求被发送出去

### JS 进阶



#### Object

##### getOwnPropertyDescriptor

- 属性描述符

```js
const obj = {
  name: 'll',
  age: 22
}
console.logo(Object.getOwnPropertyDescriptor(obj, name))
/*
{
  configurable: true, // 可配置
  enumerable: true, // 可枚举
  value: "ll",
  writable: true // 可写
}
*/

```

#### defineProperty

- 静态方法会直接在一个对象上定义一个新属性，或修改其现有属性，并返回此对象。

```js
const obj = {
  name: 'll',
  age: 22
}
Object.defineProperty(obj, 'name', {
// 属性配置
  configurable: false, // 可配置
  enumerable: false, // 可枚举
  value: "hh",
  writable: true // 可写
})
// getter setter
const test = {}
let value = ''
Object.defineProperty(test, 'a', {
	get () {
		// 读取属性a时，得到该方法的返回值
    return value
  },
  set (val) {
		// 设置属性a时，会把值传给val，调用该方法
    value = val
  }
})
```



#### Event loop(事件循环/事件轮询)

* 同步代码，一行一行放在 Call Stack(调用栈)中执行
* 遇到异步，会先“记录〞下，等待时机（定时器，网络请求）
* 时机到了，就移动到 Callback Queue(回调队列)
* 如Call Stack为空（即同步代码执行完）EventLoop开始工作
* 轮询查找Callback Queue，如有则移动到Call Stack执行
* 然后继续轮询查找

```javascript
console.log('start');

setTimeout(function() {
  console.log('callback');
}, 1000);

console.log('end');
// start end callback
```



#### Promise三种状态

* pending fulfilled(resolved) rejected
* Pending > fulfilled 或者 rejected
* 变化不可逆



#### then和catch改变状态

* then正常返回fulfilled，里面有报错则返回 rejected

* catch正常返回fulfilled，里面有报错则返回 rejected

  ```javascript
  Promise.resolve().then(() => {
    console.log(1)
    thorw new Error()
  }).catch(() => {
    console.log(2)
  }).then(() => {
    console.log(3)
  })
  ```

  

#### async/await和Promise的关系

* 执行async函数，返回的是Promise对象
* await相当于Promise中的then，await 后面可以看作是回调函数里面的内容
* Try...catch捕获异常，代替了Promise的catch



#### 宏任务(macroTask)和微任务(microTask)

* 宏任务：setTimeout, setinterval , Ajax,DOM事件
* 微任务：Promise，async/await
* 微任务执行的时机比宏任务早
* 宏任务：DOM 渲染后触发，如 setTimeout
* ﻿微任务：DOM 渲染前触发，如 Promise
* 宏任务是有浏览器规定的
* 微任务是ES6语法规定的



#### event loop和DOM渲染的关系

1. 调用栈（call stack）空闲
2. 执行当前的微任务
3. 尝试渲染DOM
4. 触发event loop