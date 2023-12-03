### VUE2.x 的使用

#### computed 和 watch

- computed 有缓存，data 不变则不会重新计算

- watch 监听引用类型，拿不到 oldVal

- 如何深度监听

  ```vue
  <script>
  export default {
    data() {
      return {
        name: 'll',
        info: {
          city: '北京'
        }
      }
    },
    watch: {
      name(oldVal, val) {
        console.log(oldVal, val)
      },
      info: {
        handler(oldVal, val) {
          // watch监听引用类型，拿不到oldVal
          console.log(oldVal, val)
        },
        deep: true // 深度监听
      }
    }
  }
  </script>
  ```

#### v-if 和 v-show

- v-if: 组件真正的渲染和销毁，而不是显示和隐藏
- v-show: 只是设置 display 属性控制显示和隐藏
- 频繁切换时候选择 v-show,一次性选择 v-if

#### 循环（列表）渲染

- 遍历对象也可以用 v-for。（val, key, index）in listObj
- key 的重要性，key 尽量要和业务关联。（尽量避免 index,random）
- v-for 和 v-if 不能一起用。
- diff 算法中通过 tag 和 key 来判断是否是 sameVNode
- 减少渲染次数，提升性能

#### 事件

- event 参数，自定义参数。（如果事件接收参数，获取 event,传$event）

- 事件修饰符，按键修饰符。

- 观察事件被绑定到哪里。

  ```vue
  <template>
  	<div>
          <button @click="handleClick1">事件1</button>
          <button @click="handleClick2(2, $event)">事件2</button>
      <div>
  </template>
  <script>
  export default {
      methods: {
          handleClick1 (event) {
              console.log(event, event.__proto__.constructor)
              /**
                  1.event是原生的event对象
                  2.事件绑定在当前元素上
              */
          },
          handleClick2 (num, event) {
              console.log(event)
          }
      }
  }
  </script>
  ```

#### 表单

- v-model

#### props 和$emit

- prop: 父组件向子组件传值
- $emit：子组件向父组件触发事件

#### 自定义事件(非父子组件传值)

- import EventBus from 'eventBus.js'

- 在一个组件中的`created`钩子函数绑定自定义事件（EventBus .$on('onAdd', this.handleAdd)）

- 在另一个组件中触发自定义事件（EventBus .$emit('onAdd'), val）

- 需要注意的是，需要在组件的`created`生命周期中通过`EventBus.$on`方法监听事件，在组件销毁的时候通过`EventBus.$off`方法取消监听。

- EventBus 对象

  ```javascript
  /**
  	Event Bus 是 Vue.js 的一个核心概念，它是一个全局事件总线，用于不同组件之间的通信。
  	通过 Event Bus，我们可以在任何组件中触发事件，并监听该事件的所有组件都可以得到通知。
  	EventBus.js
  */
  import Vue from 'vue'
  export default new Vue()
  ```

#### 生命周期

- 挂载阶段
- 更新阶段
- 销毁阶段

<img src="http://v2.cn.vuejs.org/images/lifecycle.png" alt="vue2声明周期图" style="zoom: 40%;" />

#### 父子组件生命周期调用顺序

- 父组件 created

- 子组件 created

- 子组件 mounted

- 父组件 mounted

  <hr>

- 父组件 beforeUpdate

- 子组件 beforeUpdate

- 子组件 updated

- 父组件 updated

  <hr>

- 父组件 beforeDestroy

- 子组件 beforeDestroy

- 子组件 destroyed

- 父组件 destriyed

#### 自定义 v-model

- 一个组件上的 v-model 默认会利用名为 value 的 prop 和名为 input 的事件
- v-model 的值将会传入这个名为 value 的 prop
- 同时当会触发 input 事件，会附带一个新值（$event.target.value）,名为 value 的 prop 会更新
- data 更新触发 re-render

```vue
<!-- 子组件CustomVModel.vue -->
<template>
  <div>
    <input
      type="text"
      :value="message"
      @input="$emit('change', $event.target.value)"
    />
  </div>
</template>

<script>
/**
   一个组件上的 v-model 默认会利用名为 value 的 prop 和名为 input 的事件，但是像单选框、复选框等类型的输入控件可能会将 value attribute 用于不同的目的。model 选项可以用来避免这样的冲突：
*/
export default {
  props: {
    message: {
      type: String,
      default: ''
    }
  },
  model: {
    prop: 'message', // 绑定父组件的数据
    event: 'change'
  }
}
</script>
```

```vue
<!-- 父组件 -->
<template>
  <div>
    <CustomVModel v-model="value" />
  </div>
</template>

<script>
/**
这里的 value 的值将会传入这个名为 message 的 prop。同时当 <CustomVModel> 触发一个 change 事件并附带一个新的值的时候，这个 message 的 property 将会被更新。
注意你仍然需要在组件的 props 选项里声明 message 这个 prop。
*/
export default {
  components: {
    CustomVModel
  },
  data() {
    return {
      value: ''
    }
  }
}
</script>
```

#### $nextTick

- Vue 是异步渲染
- data 改变之后，DOM 不会立刻渲染
- $nextTick 会在 DOM 渲染之后被触发，或获取最新的 DOM 节点

#### slot

- 通过父组件向子组件添加内容
- 作用域插槽: 子组件通过插槽向父组件传值，父组件通过插槽获取子组件的值
- 具名插槽

```vue
<!-- 子组件 Child.vue -->
<template>
  <div>
    <slot :slotData="info"> 这里是默认内容，父组件没有设置内容时显示 </slot>
    <slot name="bottom"> bottom </slot>
  </div>
</template>
<script>
export default {
    data () {
        return {
            info: {
			title: '这是一个标题'，
               descript: '描述'
            }
        }
    }
}
</script>
```

```vue
<!-- 父组件 -->
<template>
  <div>
    父组件
    <Child>
      <!-- 作用域插槽 -->
      <template v-slot="slotProps"> {slotProps.slotData.title} </template>
      <!-- 具名插槽 -->
      <template #bottom> 具名插槽 </template>
    </Child>
  </div>
</template>
```

#### 动态组件

- :is="component-name" 用法
- 需要根据数据，动态渲染的场景。即组件类型不确定。

```vue
<template>
	<div>
        <!-- is属性必须是动态属性 -->
        <component :is="componentName">
    </div>
</template>
<script>
import VideoItem from '@/components'
export default {
	components: {
        VideoItem
    },
    data () {
        return {
            componentName: 'VideoItem'
        }
    }
}
</script>
```

#### 异步加载组件-Vue 常见性能优化

- import()函数
- 按需加载，异步加载大组件
- Vue 常见性能优化

```vue
<script>
export default {
  components: {
    VideoItem: () => import('@/components/VideoItem')
  }
}
</script>
```

#### 缓存组件（keep-alive）-Vue 常见性能优化

- 缓存组件
- 频繁切换，不需要重复渲染
- Vue 常见性能优化

#### 组件如何抽离公共逻辑（mixin）-Vue 常见性能优化

- 多个组件又相同的逻辑，抽离出来
- 变量来源不明确，不利于阅读
- 多个 mixin 能造成命名冲突
- mixin 和组件肯恶搞出现多对多的关系，复杂的较高，不利于阅读和维护

```vue
<script>
import myMixin from 'mixin'
export default {
  mixins: [myMixin]
}
</script>
```

#### UI组件的二次封装

```vue
<template>
	<div>
        <!-- 动态绑定属性 -->
        <el-input v-bind="$attrs">
    		<!--动态设置插槽-->
            <template v-for="(value, name) in $slots" #[name]="scopeData">
                <slot :name="name" v-bind="scopeData">
    				
    			</slot>
			</template>
    	</el-input>
    </div>
</template>
<script>
    export default {
        props: ['a', 'b'],
        created () {
            console.log(this.$attrs)
            // 输出除了props中的其他所有属性(事件)
            console.log(this.$slots)
            // 输出插槽 {}
        }
    }
</script>
```



### VUE2.x 原理

#### 如何理解 MVVM

- model view vm

- M (model)：模型对象：指的是构成界面内容的相关数据

- V(view): 视图对象：指的是给用户或者开发者展示数据的界面

- VM(viewmodel): 视图模型对象：是 view 与 model 之间的桥梁 （是一个 vue 的实例，vue.js 的核心）

![](https://img2020.cnblogs.com/blog/2082604/202101/2082604-20210128114101662-221051691.png)

#### Vue 响应式

- 核心 APi - Object.defineProperty

- `Object.defineProperty` 是一个用于直接**在对象上定义新属性或修改现有属性**的方法。该方法接受三个参数，分别为要定义属性的对象、属性名和属性描述符对象。属性描述符对象包括以下可选属性：

  - `configurable`: 表示此属性是否可以被删除或修改属性描述符，默认为 `false`。
  - `enumerable`: 表示此属性是否会出现在对象枚举中，默认为 `false`。
  - `value`: 表示此属性的默认值，默认为 `undefined`。
  - `writable`: 表示此属性是否可以被重写，默认为 `false`。
  - `get`: 表示获取属性值时要执行的函数，默认为 `undefined`。
  - `set`: 表示设置属性值时要执行的函数，默认为 `undefined`。

  如果给属性同时设置了 `get` 和 `set` 方法，则此属性变为一个访问器属性（accessor property），它的值不再是一个固定值，而是由该属性的 `get` 和 `set` 方法共同决定。

  当你把一个普通的 JavaScript 对象传入 Vue 实例作为 `data` 选项，Vue 将遍历此对象所有的 property，并使用 [`Object.defineProperty`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty) 把这些 property 全部转为 [getter/setter](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Working_with_Objects#定义_getters_与_setters)。

  ```javascript
  let obj = {}
  let val = 1
  
  Object.defineProperty(obj, 'prop', {
    get: function () {
      console.log('Getting value')
      return val
    },
    set: function (newVal) {
      console.log('Setting value')
      val = newVal
    }
  })
  
  console.log(obj.prop) // Getting value, 1
  obj.prop = 2 // Setting value
  console.log(obj.prop) // Getting value, 2
  ```

#### 如何深度监听 data 变化(对象，数组)

```javascript
// 3.视图更新函数
function updateView() {
  console.log('视图更新了')
}
// 重新定义数组的隐士原型__proto__
// 创建新对象，并将对象的__proto__指向Array.prototype，在扩展新的方法不会影响到原型
const arrProto = Object.create(Array.prototype)
const methods = ['push', 'pop', 'shift', 'unshift']
methods.foreach((method) => {
  arrProto[method] = function () {
    updateView() // 触发视图更新
    arrProto.__proto__[method].call(this, arguments)
  }
})
// 2.重新定义对象属性(转为getter、setter)，监听起来
function defineReactive(target, key, value) {
  observer(value) // 深度监听
  Object.defineProperty(target, key, {
    get() {
      return value
    },
    set(newVal) {
      if (newVal !== value) {
        /**
                	设置新值
                	注意： value一直在闭包中，get()函数一直能拿到最新的值
                */
        value = newVal
        observer(value) // 深度监听
        // 触发视图更新
        updateView()
      }
    }
  })
}

// 1.监听对象属性
function observer(target) {
  // 判断target为一个对象或者数组
  if (typeof target === 'object' && target !== null) {
    if (Array.isArray(target)) {
      // 将数组的隐士原型指向自定义的原型
      target.__proto__ = arrProto
    }
    // 重新定义对象各个属性（for in 也可以遍历数组）
    for (const key in target) {
      defineReactive(target, key, target[key])
    }
  }
}

// 数据
const data = {
  count: 0,
  message: ''
}
// 开始监听data对象属性
observer(data)

data.count++
data.message = 'hello'
```

- 深度监听，需要递归到底，一次性计算量大
- 无法监听新增属性、删除属性（Vue.set Vue.delete）
- 无法原生监听数组，需要特殊处理

#### 虚拟 DOM(Vitual DOM)和 diff

- vdom 是实现 vue 和 React 的重要基石
- diff 算法是 vdom 中最核心、最关键的部分
- 用 JS 模拟 DOM 结构
- 新旧 vdom 对比，得出最小的更新范围，最后更新 DOM
- 数据驱动视图模式下，有效控制 DOM 操作
- snabbdom 库
- h 函数返回一个 vNode 对象 { sel, data, children, text, elm, key}
- patch(elm, vNode) 创建一个 oldVnode 绑定 elm
- patch(vNode, vNode) 统一绑定 elm
- patchVnode(节点和 key 相同)
- addVnodes(new 有 children, old 无 children) removeVnodes(new 无 children, old 有 children)
- updateChildren(new 有 children, old 有 children) （纵向交叉对象，相同继续 patchVnode，）
- vdom 核心概念很重要:h、vnode、patch、diff 、key 等
- vdom 存在的价值更加重要:数据驱动视图，控制 DOM 操作

#### with 语法

- with(obj){}

- 改变{}的自由变量的查找规则，当做 obj 属性来查找
- 如果找不到匹配的 obj 属性，就会报错
- with 要慎用，它打破了作用域规则，易读性变差

#### 模板编译

- 模板不是 html，有指令、插值、js 表达式，能实现判断、循环
- html 是标签语言，只有 js 才能实现判断循环（图灵完备的）
- 因此，模板一定是转换为某种 js 代码，即编译模板

- 模板编译为 render 函数，执行 render 函数返回 vnode
- 基于 vnode 在执行 patch 和 diff
- 使用 webpack vue-loader，会在开发环境下编译模板

```javascript
const compiler = require('vue-template-compiler')

const template = `<div>{{message}}</div>`
const res = compiler(template)
console.log(res.render)
/*
	编译为: with(this){return _c('div',[_v(_s(message))])}
	
	this: new Vue()
	_c: createElement
	_v: createTextVNode
	_s: toString
*/
```

#### 组件渲染/更新过程

- 初次渲染过程
  - 编译模板为 render 函数
  - 触发响应式，监听 data 属性 getter setter
  - 执行 render 函数，生成 vnode, pathc(elem,vnode)
- 更新过程
  - 修改 data,触发 setter(此前在 getter 中已被监听)
  - 重新执行 render 函数，生成 newVnode
  - pathc(vnode, newVnode)
- 异步渲染

  - $nextTick() 返回一个 promise 对象
  - 汇总 data 的修改，一次性更新视图
  - 减少 DOM 操作次数，提高性能

- 每个组件实例都对应一个 **watcher** 实例，它会在组件渲染的过程中把“接触”过的数据 property 记录为依赖。之后当依赖项的 setter 触发时，会通知 watcher，从而使它关联的组件重新渲染。

![data](https://v2.cn.vuejs.org/images/data.png)

#### 网页 URL 组成部分

```javascript
// http://127.0.0.1:8881/01-hash.html?a=100&b=20#/aaa/bbb
location.protocol // http:
location.hostname // 127.0.0.1
location.host //127.0.0.1:8881
location.port //8881'
location.pathname // 01-hash.html
location.search // ?a=100&b=20
location.hash //  #/aaa/bbb
```

#### hash 的特点

- hash 变化会触发网页跳转，即浏览器前进、后退
- hash 变化不会刷新页面，SPA 的特点
- hash 永远不会提交到 server 端（前端自生自灭）

```javascript
window.onhashchange = (event) => {
  console.log(event.oldURL)
  console.log(event.newURL)
  console.log(location.hash)
}
// 页面初次加载，获取hash
document.addEventlistener('DOMContentLoaded', () => {
  console.log(location.hash)
})
// 手动修改url
document.getElementById('btn').addEventListener('click', () => {
  location.href = '#user'
})
```

#### H5 history

- 用 url 规范的路由，但是跳转不刷新页面
- history.pushState
- window.onpopstate

#### 为何组件的 data 是一个函数

Vue 中组件本身是一个类每次使用需要实例化一个对象，如果 data 是一个对象，

组件复用的时候 data 都指向一个对象，一个组件对 data 的修改，

影响到其他组件；如果 data 是一个函数，每个组件调用 data 函数，

都会有一个独立的 data.

#### 如何将组件所有 props 传递给子组件

- $props
- <User v-bind="$props” />

#### 何时要使用异步组件

- 加载大组件
- 路由异步加载

#### 何时需要使用 befoeDestory

- 解绑自定义事件 event.$off
- 清楚定时器
- 解绑自定的 DOM 事件（通过 addEventListener 添加的事件）

#### 描述响应式原理

- 监听 data 变化
- 组件的渲染和更新流程

#### diff 算法过程

- patch（elm, vNode） 和 patch(vNode, vNode)
- patchVnode addVnodes 和 removeVnodes
- updataChildren (tag 和 key 来判断是否是 sameVNode)

#### Vue 常见性能优化方式

- 合理使用 v-show 和 v-if
- 合理使用 computed
- v-for 不要和 v-if 同时使用
- 自定义事件，DOM 事件及时销毁
- 合理使用异步组件
- 合理使用 keep-alive
- data 层级不要太深（一次性递归监听 data 属性，消耗性能大）

### VUE3.X 使用

#### Vue3 比 Vue2 有什么优势

- 体积更小
- 性能更好
- 更好的 ts 支持
- 更好代码组织
- 更好的逻辑抽离
- 新功能

#### vue3 和 vue2 的生命周期有什么区别

- options API

  - beforeDestory > beforeUnmount
  - destoryed > unmounted
  - 其他沿用 vue2 生命周期

- composition API （setup 相当于 beforecCreate/created）
  - onBeforeCreate
  - onCreated
  - onBeforeMount
  - onMounted
  - onBeforeUpdate
  - onUpdated
  - onBeforeUnmount
  - onUnmounted

#### Composition API

- 更好的代码组织
- 更好的逻辑复用
- 更好的类型推导

#### 如何理解 ref toref 和 torefs

- ref

  - 生成值类型的响应式数据
  - 可用于模板和 reactive
  - 通过.value 修改值
  - 获取 dom 元素（元素 ref 属性和 ref 的变量相同）

- toRef

  - 针对一个响应式对象（reactive 封装的）的一个 prop

  - 创建一个 ref，具有响应式

  - 两者保持引用关系

    ```vue
    <script setup>
    import { reactive, toref } from 'vue'
    
    const state = reactive({
        username: 'zhangsan'
        age: 18
    })
    const ageRef = toref(state, 'age')
    state.age = 20
    ageRef.value = 30
    </script>
    ```

- toRefs

  - 将响应式对象（reactive 封装）转换为普通对象。（可以进行解构,解构值具有响应式）

  - 对象的每个 prop 都是对应的 ref

  - 两者保持应用关系

    ```vue
    <script setup>
    import { reactive, toref } from 'vue'
    
    const state = reactive({
        username: 'zhangsan'
        age: 18
    })
    const stateRefs = torefs(state)
    const {age: ageRef, username} = stateRefs
    </script>
    ```

#### ref 为何需要 value 属性

- ref 是一个对象（不丢失响应式），value 存储值
- 通过.value 属性的 get 和 set 实现响应式
- 用于模板、reactive 时，不需要.value，其他情况需要

#### 为什么需要 toRef 和 toRefs

- 针对的响应式对象（reactive 封装的），非普通对象
- 不丢失响应式的情况下，把对象数据分解、扩散
- 不创造响应式，而是延续响应式

#### vue3 升级了那些重要功能

- createApp
- emits 属性(配置自定义事件)
- 生命周期
- 多事件
- Fragment
- 移除.sync 改为 v-model： 参数
- 异步组件的写法
- 移除 filter
- Teleport
- Suspence
- Composition API

```javascript
// vue的实例
// 2.0
const app = new Vue({
  /* 选项 */
})

Vue.use()
Vue.mixin()
Vue.component()
Vue.directive()
// 3.0
const app = Vue.createApp({
  /* 选项 */
})

app.use()
app.component()
app.mixin()
app.directive()
```

```vue
<!-- 选项式API -->
<template>
  <h1>hello</h1>
  <button @click="one($event), two($event)"></button>
  <AsyncComponent />
</template>
<script>
import { defineAsyncComponent } from 'vue'
export default {
    emits: ['inFocus', 'submit']
    components: {
        AsyncComponent: defineAsyncComponent(
        	() => import('./components/AsyncComponent.vue')
        )
    },
    setup(props, { emit }) {

    }
}
</script>
```

```vue
<template>
  <AdminPage />
</template>
<script setup>
import { defineAsyncComponent } from 'vue'

const AdminPage = defineAsyncComponent(() =>
  import('./components/AdminPageComponent.vue')
)
const emit = defineEmits(['inFocus', 'submit'])
function buttonClick() {
  emit('submit')
}
</script>
```

#### CompositionAPI 如何实现逻辑复用

- 抽离逻辑代码到一个函数
- 函数命名一般约定为 useXxxx 格式（React Hooks 也是）
- 在 setup 引用 use 函数

#### Proxy

```javascript
/*
	第一个参数 target 就是用来代理的“对象”，被代理之后它是不能直接被访问的，而 handler 就是实现代理的过程。
*/
let p = new Proxy(target, handler)

// get拦截对象属性的读取，比如proxy.foo和proxy['foo']。
let arr = [7, 8, 9]
arr = new Proxy(arr, {
  get(target, prop) {
    // console.log(target, prop)
    return prop in target ? target[prop] : 'error'
  }
})
console.log(arr[1])
console.log(arr[10])

let dict = {
  hello: '你好',
  world: '世界'
}
dict = new Proxy(dict, {
  get(target, prop) {
    return prop in target ? target[prop] : prop
  }
})
console.log(dict['world'])
console.log(dict['imooc'])

// set拦截对象属性的设置，比如proxy.foo = v或proxy['foo'] = v，返回一个布尔值。
let arr = []
arr = new Proxy(arr, {
  set(target, prop, val) {
    if (typeof val === 'number') {
      target[prop] = val
      return true
    } else {
      return false
    }
  }
})
arr.push(5)
arr.push(6)
console.log(arr[0], arr[1], arr.length)

// has拦截propKey in proxy的操作，返回一个布尔值。
let range = {
  start: 1,
  end: 5
}

range = new Proxy(range, {
  has(target, prop) {
    return prop >= target.start && prop <= target.end
  }
})
console.log(2 in range)
console.log(9 in range)

/*
	ownKeys
	拦截Object.getOwnPropertyNames(proxy)、Object.getOwnPropertySymbols(proxy)、Object.keys(proxy)、for...in循环，返回一个数组。该方法返回目标对象所有自身的属性的属性名，而Object.keys()的返回结果仅包括目标对象自身的可遍历属性。
*/
let obj = {
  name: 'imooc',
  [Symbol('es')]: 'es6'
}
console.log(Object.getOwnPropertyNames(obj))
console.log(Object.getOwnPropertySymbols(obj))
console.log(Object.keys(obj))
for (let key in obj) {
  console.log(key)
}

let userinfo = {
  username: 'xiecheng',
  age: 34,
  _password: '***'
}
userinfo = new Proxy(userinfo, {
  ownKeys(target) {
    return Object.keys(target).filter((key) => !key.startsWith('_'))
  }
})

// for (let key in userinfo) {
//     console.log(key)
// }
console.log(Object.keys(userinfo))

// deleteProperty 拦截delete proxy[propKey]的操作，返回一个布尔值。
let user = {
  name: 'xiecheng',
  age: 34,
  _password: '***'
}
user = new Proxy(user, {
  get(target, prop) {
    if (prop.startsWith('_')) {
      throw new Error('不可访问')
    } else {
      return target[prop]
      // Reflect.get(target, prop)
    }
  },
  set(target, prop, val) {
    if (prop.startsWith('_')) {
      throw new Error('不可访问')
    } else {
      target[prop] = val
      // Reflect.set(target, prop, val)
      return true
    }
  },
  deleteProperty(target, prop) {
    // 拦截删除
    if (prop.startsWith('_')) {
      throw new Error('不可删除')
    } else {
      delete target[prop]
      // Reflect.deleteProerty(target, prop)
      return true
    }
  },
  ownKeys(target) {
    return Object.keys(target).filter((key) => !key.startsWith('_'))
    // return Object.ownKeys(target).filter(key => !key.startsWith('_'))
  }
})
console.log(user.age)
console.log(user._password)
user.age = 18
console.log(user.age)
try {
  user._password = 'xxx'
} catch (e) {
  console.log(e.message)
}

try {
  // delete user.age
  delete user._password
} catch (e) {
  console.log(e.message)
}
console.log(user.age)

for (let key in user) {
  console.log(key)
}

// apply拦截 Proxy 实例作为函数调用的操作，比如proxy(...args)、proxy.call(object, ...args)、proxy.apply(...)。
let sum = (...args) => {
  let num = 0
  args.forEach((item) => {
    num += item
  })
  return num
}

sum = new Proxy(sum, {
  apply(target, ctx, args) {
    return target(...args) * 2
  }
})
console.log(sum(1, 2))
console.log(sum.call(null, 1, 2, 3))
console.log(sum.apply(null, [1, 2, 3]))

// construct拦截 Proxy 实例作为构造函数调用的操作，比如new proxy(...args)。

let User = class {
  constructor(name) {
    this.name = name
  }
}
User = new Proxy(User, {
  construct(target, args, newTarget) {
    console.log('construct')
    return new target(...args)
  }
})
console.log(new User('imooc'))
```

#### Reflect(映射)

- Reflect 对象与 Proxy 对象一样，也是 ES6 为了操作对象而提供的新 API

- 将 Object 属于语言内部的方法放到 Reflect 上,

  ```javascript
  /*
  现阶段,某些方法同时在 Object 和 Reflect 对象上部署,未来的新方法将只部署在 Reflect 对象上。
  */
  Object.defineProperty()
  Reflect.defineProperty()
  ```

- 修改某些 Obiect 方法的返回结果，让其变得更合理

  ```javascript
  /*
  例如,Object.defineProperty(obj, name, desc) 在无法定义属性时,会抛出一个错误,而 Reflect.defineProperty(obj, name, desc) 则会返回 false。
  */
  Object.defineProperty() // 无返回值
  Reflect.defineProperty() // 返回值为true/false
  ```

- 让 Object 操作变成函数行为

  ```javascript
  console.log('assign' in Object)
  console.log(Reflect.has(Object, 'assign'))
  ```

- Reflect 对象的方法与 Proxy 对象的方法一一对应

- Reflect 对象的方法如下:

  - Reflect.apply(target(目标函数), thisArg（target 函数调用时绑定的 this 对象）, args（target 函数调用时传入的实参列表，该参数应该是一个类数组的对象）)
  - Reflect.construct(target, args) 方法的行为有点像 new 操作符 构造函数 ， 相当于运行 new target(...args).
  - Reflect.get(target, name, receiver)
  - Reflect.set(target, name, value, receiver)
  - Reflect.defineProperty(target, name, desc) 基本等同于 Object.defineProperty() 方法，唯一不同是返回 Boolean 值。
  - Reflect.deleteProperty(target, name) 允许你删除一个对象上的属性，返回一个 Boolean 值表示该属性是否被成功删除。
  - Reflect.has(target, name)
  - Reflect.ownKeys(target) 方法返回一个由目标对象自身的属性键组成的数组
  - Reflect.isExtensible(target) 判断一个对象是否可扩展 （即是否能够添加新的属性），它与 Object.isExtensible() 方法一样。
  - Reflect.preventExtensions(target) 方法阻止新属性添加到对象 例如：防止将来对对象的扩展被添加到对象中)。该方法与 Object.preventExtensions() 方法一致
  - Reflect.getOwnPropertyDescriptor(target, name) 与 Object.getOwnPropertyDescriptor() 方法相似。如果在对象中存在，则返回给定的属性的属性描述符，否则返回 undefined。
  - Reflect.getPrototypeOf(target) 与 Object.getPrototypeOf() 方法是一样的。都是返回指定对象的原型（即，内部的 [[Prototype]] 属性的值）。
  - Reflect.setPrototypeOf(target, prototype) 方法改变指定对象的原型 （即，内部的 [[Prototype]] 属性值）

  这些方法与 Proxy 对象的方法相同,而且它们返回的值也一样。Reflect 对象还有一些静态方法:

  \- Reflect.get(target, name, receiver) 相当于 target[name],返回属性的值。
  \- Reflect.set(target, name, value, receiver) 相当于 target[name] = value,设置属性值。
  \- Reflect.has(obj, name) 相当于 name in obj,判断对象是否有指定属性

#### Vue3 如何实现响应式

```javascript
// 创建响应式
function reactive(target = {}) {
  const targetTypes = ['[object Object]', '[object Array]']
  if (!targetTypes.includes(Object.prototype.toString.call(target))) {
    return target
  }
  // 代理配置
  const proxyConf = {
    get(target, prop) {
      const keys = Reflect.ownKeys(target)
      // 只处理本身属性（非原型）
      if (keys.includes(prop)) {
        // 监听
        console.log('get', prop)
      }
      const value = Reflect.get(target, prop)
      return reactive(value) // 深度监听
    },
    set(target, prop, value) {
      if (value === Reflect.get(target, prop)) {
        return true
      }
      console.log('set', prop)
      const newVlaue = Reflect.set(target, prop, value)
      return newVlaue
    },
    deleteProperty(target, prop) {
      console.log('delete', prop)
      return Reflect.deleteProperty(target, prop)
    }
  }
  // 生成代理对象
  return new Proxy(target, proxyConf)
}

const data = {
  message: 'hello',
  count: 0
}
const proxyData = reactive(data)
```

- 深度监听性能更好
- 可监听新增/删除的属性
- 可见听数组变化

#### watch 和 watchEffect 的区别

- 两者都可以监听 data 变化
- watch 需要明确要监听的属性
- watchEffect 会根据其中的属性，自动监听其变化

```vue
<script setup>
import { ref, reactive, watch, watchEffect } from 'vue'
export default {
    const countRef = ref(0)
    const state = reactive({
        name: 'LL',
        age: 15
    })
    // watch
    watch(countRef, (newVal, oldVal) => {
        console.log(newVal, oldVal)
    }, {
        immediate: true // 初始化就监听
    })
    watch(() => state.age, (newVal, oldVal) => {
        console.log(newVal, oldVal)
    }, {
        deep: true // 深度监听
    })

    // watchEffect
    watchEffect(() => { // 初始化就执行，收集要监听的数据
        console.log(state.name)
    })
}
</script>
```

#### setup 中获取组件实例

- 在 setup 中和其他 Composition API 中没有 this
- 可通过 getCurrentInstance()
- 若使用 Options API 可照常使用 this

#### Vue3 为何比 Vue2 快

- Proxy
- PatchFlag
- hoistStatic
- cacheHandler
- SSR 优化
- tree-shaking

##### PatchFlag

- 编译模板时，动态节点做标记
- 标记分为不同类型，如 TEXT PROPS CLASS
- diff 算法时，可以区分静态节点，以及不同类型的动态节点（静态节点无需 patch）

##### hoistStatic

- 将静态节点的定义，提升到父作用域，缓存起来
- 多个相邻的静态节点，会被合并起来

##### cacheHandler

- 缓冲事件

##### SSR 优化

- 静态节点直接输出，绕过 vdom
- 动态节点，还是需要动态渲染

#### tree-shaking

- 模板编译的时候按需引入

#### Composition API 和 React Hooks 对比

- 前者 setup 只会被调用一次，而后者函数会被多次调用
- 前者无需 useMemo useCallback,因为 setup 只调用一次
- 前者无需考虑调用顺序，而后者需要保证 hooks 的顺序一致
- 前者 reactive+ref 比后者 useState，要难理解

