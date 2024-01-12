### mockjs

- mock.js两大功能：劫持ajax + 全面的random能力
- mock.js用于nodejs服务端，使用random能力

```shell
npm init -y

npm install mockjs --save

```

```js
// mock/test.js
const Mock = require('mockjs')
const Random = Mock.Random
module.exports = [
    {
        url:'/api/test',
        method: 'get',
        reponse () {
            return {
                errno: 0,
                data: {
                    name: Random.cname()
                }
            }
        }
    }
]

// mock/question.js

const Mock = require('mockjs')
const Random = Mock.Random
module.exports = [
    {
        url:'/api/question/:id',
        method: 'get',
        reponse () {
            return {
                errno: 0,
                data: {
                    id: Random.id(),
                    title: Random.ctitle()
                }
            }
        }
    }
]

// mock/index.js

const test = require('./test')
const question = require('./question')

module.exports = [
    ...test,
    ...question
]
```

```shell
npm install koa koa-router --save
```

```js
// index.js
const Koa = require('koa')
const Router = require('koa-router')
const mockList = require('./mock/index')

const app = new Koa()
const router = new Router()
async function getRes(fn) {
  return new Promise((resolve) => {
    setTimeout(() => {
      const res = fn()
      resolve(res)
    }, 2000)
  })
}
mockList.forEach((item) => {
  const { url, method, response } = item
  router[method](url, async (ctx) => {
    const res = await getRes(response)
    ctx.body = res
  })
})
app.use(router.routes())
app.listen(3001)

```