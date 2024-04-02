### VUE2.x的使用 

#### 注入

Vue 会将以下配置注入到vue实例中

- data

- props

- computed

- Methods

> 模版中可以直接使用vue实例中的成员

组件的出现是为了实现以下两个目标：

1. 降低整体复杂度，提升代码的可读性和可维护性
2. 提升局部代码的可复用性

绝大部分情况下，一个组件就是页面中某个区域，组件包含该区域的：

- 功能（JS代码）

- 内容（模板代码）

- 样式（CSS代码）

> 要在组件中包含样式，需要构建工具的支撑

#### 组件开发

##### 创建组件

组件是根据一个普通的配置对象创建的，所以要开发一个组件，只需要写一个配置对象即可

该配置对象和vue实例的配置是**几乎一样**的

```js
//组件配置对象
var myComp = {
  data(){
    return {
      // ...
    }
  },
  template: `....`
}
```

值得注意的是，组件配置对象和vue实例有以下几点差异：

- 无`el`
- `data`必须是一个函数，该函数返回的对象作为数据
- 由于没有`el`配置，组件的虚拟DOM树必须定义在`template`或`render`中



##### 注册组件

注册组件分为两种方式，一种是**全局注册**，一种是**局部注册**

##### 全局注册

一旦全局注册了一个组件，整个应用中任何地方都可以使用该组件

<img src="http://mdrs.yuanjin.tech/img/2020-02-18-10-24-44.png" style="zoom:50%;" />

全局注册的方式是：

```js
// 参数1：组件名称，将来在模板中使用组件时，会使用该名称
// 参数2：组件配置对象
// 该代码运行后，即可在模板中使用组件
Vue.component('my-comp', myComp)
```

在模板中，可以使用组件了

```html
<my-comp />
<!-- 或 -->
<my-comp></my-comp>
```


> 但在一些工程化的大型项目中，很多组件都不需要全局使用。
> 比如一个登录组件，只有在登录的相关页面中使用，如果全局注册，将导致构建工具无法优化打包
> **因此，除非组件特别通用，否则不建议使用全局注册**



##### 局部注册

局部注册就是哪里要用到组件，就在哪里注册

<img src="http://mdrs.yuanjin.tech/img/2020-02-18-10-28-45.png" style="zoom:50%;" />

局部注册的方式是，在要使用组件的组件或实例中加入一个配置：

```js
// 这是另一个要使用my-comp的组件
var otherComp = {
  components:{
    // 属性名为组件名称，模板中将使用该名称
    // 属性值为组件配置对象
    "my-comp": myComp
  },
  template: `
    <div>
      <!-- 该组件的其他内容 -->
      <my-comp></my-comp>
    </div>
  `;
}
```

#### 应用组件

在模板中使用组件特别简单，把组件名当作HTML元素名使用即可。

但要注意以下几点：

1. **组件必须有结束**

组件可以自结束，也可以用结束标记结束，但必须要有结束

下面的组件使用是错误的：

```html
<my-comp>
```

2. **组件的命名**

无论你使用哪种方式注册组件，组件的命名需要遵循规范。

组件可以使用`kebab-case 短横线命名法`，也可以使用`PascalCase 大驼峰命名法`

下面两种命名均是可以的

```js
var otherComp = {
  components:{
    "my-comp": myComp,  // 方式1
    MyComp: myComp //方式2
  }
}
```

> 实际上，使用`小驼峰命名法 camelCase`也是可以识别的，只不过不符合官方要求的规范

使用`PascalCase`方式命名还有一个额外的好处，即可以在模板中使用两种组件名

```js
var otherComp = {
  components:{
    MyComp: myComp
  }
}
```

模板中：

```html
<!-- 可用 -->
<my-comp />
<MyComp />
```

因此，在使用组件时，为了方便，往往使用以下代码：

```js
var MyComp = {
  //组件配置
}

var OtherComp = {
  components:{
    MyComp // ES6速写属性
  }
}
```



#### 组件树

一个组件创建好后，往往会在各种地方使用它。它可能多次出现在vue实例中，也可能出现在其他组件中。

于是就形成了一个组件树

<img src="http://mdrs.yuanjin.tech/img/2020-02-18-11-00-58.png" style="zoom:50%;" />

##### 向组件传递数据

大部分组件要完成自身的功能，都需要一些额外的信息

比如一个头像组件，需要告诉它头像的地址，这就需要在使用组件时向组件传递数据

传递数据的方式有很多种，最常见的一种是使用**组件属性 component props**

首先在组件中申明可以接收哪些属性:

```js
var MyComp = {
  props:["p1", "p2", "p3"],
  // 和vue实例一样，使用组件时也会创建组件的实例
  // 而组件的属性会被提取到组件实例中，因此可以在模板中使用
  template: `
    <div>
      {{p1}}, {{p2}}, {{p3}}
    </div>
  `
}
```

在使用组件时，向其传递属性：

```js
var OtherComp = {
  components: {
    MyComp
  },
  data(){
    return {
      a:1
    }
  },
  template: `
    <my-comp :p1="a" :p2="2" p3="3"/>
  `
}
```

**注意：在组件中，属性是只读的，绝不可以更改，这叫做单向数据流**

<img src="http://mdrs.yuanjin.tech/img/2020-02-18-11-13-19.png" style="zoom:50%;" />



#### 子组件样式（scope）

子组件最外层元素（根元素）样式，可以在父组件中控制

#### computed和watch

* computed有缓存，data不变则不会重新计算

* watch监听引用类型，拿不到oldVal

* 如何深度监听

  ```vue
  <script>
  export default {
      data () {
          return {
              name: 'll',
              info: {
                  city: '北京'
              }
          }
      },
      watch: {
          name (oldVal, val){
              console.log(oldVal,val)
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



#### v-if和v-show

* v-if:  true渲染dom;false不会渲染dom
* v-show: 无论true还是false都会渲染dom;只是设置display属性为none或者block
* 频繁切换时候选择v-show,一次性选择v-if



####  循环（列表）渲染

* 遍历对象也可以用v-for。（val, key, index）in listObj
* key的重要性，key尽量要和业务关联。（尽量避免index,random）
* v-for和v-if不能一起用。



#### 事件

* event参数，自定义参数。（如果事件接收参数，获取event,传$event）

* 事件修饰符，按键修饰符。

* 观察事件被绑定到哪里。

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
              console.log(event, event.__proto__constructor)
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

* v-model

#### props和$emit

* prop: 父组件向子组件传值
* $emit：子组件向父组件触发事件

#### 自定义事件(非父子组件传值)

* import EventBus from 'eventBus.js'

* 在一个组件中的`created`钩子函数绑定自定义事件（EventBus .$on('onAdd', this.handleAdd)）

* 在另一个组件中触发自定义事件（EventBus .$emit('onAdd'), val）

* 需要注意的是，需要在组件的`created`生命周期中通过`EventBus.$on`方法监听事件，在组件销毁的时候通过`EventBus.$off`方法取消监听。

* EventBus对象

  ```javascript
  /**
  	Event Bus 是 Vue.js 的一个核心概念，它是一个全局事件总线，用于不同组件之间的通信。
  	通过 Event Bus，我们可以在任何组件中触发事件，并监听该事件的所有组件都可以得到通知。
  	EventBus.js
  */
  import Vue from 'vue'
  export default new Vue
  ```

  

#### 生命周期

* 挂载阶段
* 更新阶段
* 销毁阶段

<img src="http://v2.cn.vuejs.org/images/lifecycle.png" alt="vue2声明周期图" style="zoom: 40%;" />

####  父子组件生命周期调用顺序

* 父组件 created

* 子组件 created

* 子组件 mounted

* 父组件 mounted

  <hr>

* 父组件 beforeUpdate

* 子组件 beforeUpdate

* 子组件 updated

* 父组件 updated

  <hr>

* 父组件 beforeDestroy

* 子组件 beforeDestroy

* 子组件 destroyed

* 父组件 destriyed



#### 自定义v-model

```vue
<!-- 子组件CustomVModel.vue -->
<template>
	<div>
        <input type="text" :value="message" @input="$emit('change', $event.target.value)" />
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
    data () {
        return {
            value: ''
        }
    }
}
</script>
```



#### $nextTick

* Vue是异步渲染
* data改变之后，DOM不会立刻渲染
* $nextTick会在DOM渲染之后被触发，或获取最新的DOM节点



#### slot

* 通过父组件向子组件添加内容
* 作用域插槽: 子组件通过插槽向父组件传值，父组件通过插槽获取子组件的值
* 具名插槽

```vue
<!-- 子组件 Child.vue -->
<template>
	<div>
        <slot :slotData="info">
            这里是默认内容，父组件没有设置内容时显示
    	</slot>
        <slot name="bottom">
            bottom
    	</slot>
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
            <template v-slot="slotProps">
                {slotProps.slotData.title}
			</template>
			<!-- 具名插槽 -->
			<template #bottom>
				具名插槽 
			</template>
    	</Child>
    </div>
</template>
```



#### 动态组件

* :is="component-name" 用法
* 需要根据数据，动态渲染的场景。即组件类型不确定。

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



#### 异步加载组件-Vue常见性能优化

* import()函数
* 按需加载，异步加载大组件
* Vue常见性能优化

```vue
<script>
export default {
	components: {
        VideoItem: () => import('@/components/VideoItem')
    }
}
</script>
```



#### 缓存组件（keep-alive）-Vue常见性能优化

* 缓存组件
* 频繁切换，不需要重复渲染
* Vue常见性能优化



#### 组件如何抽离公共逻辑（mixin）-Vue常见性能优化

* 多个组件又相同的逻辑，抽离出来
* 变量来源不明确，不利于阅读
* 多个mixin能造成命名冲突
* mixin和组件肯恶搞出现多对多的关系，复杂的较高，不利于阅读和维护

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

### VUE2.x原理



#### 如何理解MVVM

* model view  vm

* M (model)：模型对象：指的是构成界面内容的相关数据

* V(view): 视图对象：指的是给用户或者开发者展示数据的界面

* VM(viewmodel): 视图模型对象：是view与model之间的桥梁 （是一个vue的实例，vue.js的核心）

  

![](https://img2020.cnblogs.com/blog/2082604/202101/2082604-20210128114101662-221051691.png)





#### Vue响应式

* 核心APi - Object.defineProperty 

* `Object.defineProperty` 是一个用于直接**在对象上定义新属性或修改现有属性**的方法。该方法接受三个参数，分别为要定义属性的对象、属性名和属性描述符对象。属性描述符对象包括以下可选属性：

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
    get: function() {
      console.log('Getting value')
      return val
    },
    set: function(newVal) {
      console.log('Setting value')
      val = newVal
    }
  })
  
  console.log(obj.prop) // Getting value, 1
  obj.prop = 2         // Setting value
  console.log(obj.prop) // Getting value, 2
  
  ```

  

####  如何深度监听data变化(对象，数组)

```javascript
// 3.视图更新函数
function updateView() {
    console.log('视图更新了')
}
// 重新定义数组的隐士原型__proto__
// 创建新对象，并将对象的__proto__指向Array.prototype，在扩展新的方法不会影响到原型
const arrProto = Object.create(Array.prototype)
const methods = ['push', 'pop', 'shift', 'unshift']
methods.foreach( method => {
    arrProto[method] = function () {
        updateView() // 触发视图更新
        arrProto.__proto__[method].call(this, arguments)
    }
})
// 2.重新定义对象属性(转为getter、setter)，监听起来
function defineReactive (target, key, value) {
    observer(value) // 深度监听
    Object.defineProperty(target, key, {
		get () {
			return value
        },
        set (newVal) {
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
function observer (target) {
    // 判断target为一个对象或者数组
    if (typeof target === 'object' && target !== null) {
        if (Array.isArray(target)) {
            // 将数组的隐士原型指向自定义的原型
			target.__proto__ =  arrProto
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

* 深度监听，需要递归到底，一次性计算量大
* 无法监听新增属性、删除属性（Vue.set Vue.delete）
* 无法原生监听数组，需要特殊处理



#### 虚拟DOM(Vitual DOM)和diff

* vdom是实现vue和React的重要基石
* diff算法是vdom中最核心、最关键的部分
* 用JS模拟DOM结构
* 新旧vdom对比，得出最小的更新范围，最后更新DOM
* 数据驱动视图模式下，有效控制DOM操作
* snabbdom库
* h函数返回一个vNode对象 { sel, data, children, text,  elm, key}
* patchVnode
* addVnodes removeVnodes
* updateChildren(key的重要性)
* vdom 核心概念很重要:h、vnode、patch、diff 、key等
* vdom存在的价值更加重要:数据驱动视图，控制 DOM操作



#### with语法

* with(obj){}

* 改变{}的自由变量的查找规则，当做obj属性来查找
* 如果找不到匹配的obj属性，就会报错
* with要慎用，它打破了作用域规则，易读性变差



#### 模板编译

* 模板不是html，有指令、插值、js表达式，能实现判断、循环
* html是标签语言，只有js才能实现判断循环（图灵完备的）
* 因此，模板一定是转换为某种js代码，即编译模板

* 模板编译为render函数，执行render函数返回vnode
* 基于vnode在执行patch和diff
* 使用webpack vue-loader，会在开发环境下编译模板

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

* 初次渲染过程
  * 编译模板为render函数
  * 触发响应式，监听data属性getter setter
  * 执行render函数，生成vnode, pathc(elem,vnode)
* 更新过程
  * 修改data,触发setter(此前在getter中已被监听)
  * 重新执行render函数，生成newVnode
  * pathc(vnode, newVnode)
* 异步渲染
  * $nextTick() 返回一个promise对象
  * 汇总data的修改，一次性更新视图
  * 减少DOM 操作次数，提高性能

* 每个组件实例都对应一个 **watcher** 实例，它会在组件渲染的过程中把“接触”过的数据 property 记录为依赖。之后当依赖项的 setter 触发时，会通知 watcher，从而使它关联的组件重新渲染。

![data](https://v2.cn.vuejs.org/images/data.png)

