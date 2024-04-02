### 函数防抖（debounce）

函数去抖动理解位电梯关门

函数防抖的三个条件：

1. 高频触发函数
2. 耗时
3. 以最后一次调用为准

```js
function debounce(fun, duration = 500) {
    let timerId
    return function (...rest)  {
      if (timerId) {
        clearTimeout(timerId)
      }
      timerId = setTimeout(() => {
        fun.apply(this, rest)
      }, duration)
    }
}
function demo(a, b) {
    console.log(this)
    console.log(a, b)
}
const res = debounce(demo)
window.onresize = function () {
	res('11','22')
}

```

> apply/ bind /call
>
> * apply bind apply 的第一个参数改变this指向的对象
> * apply call 直接调用函数，bind则返回一个新函数
> * bind  call 第一个参数后面的参数为函数的参数，apply的参数需要传入一个数组

### 获取组件的类型（vue）

通过模板（类）拿到实例类型：` InstanceType<typeof ElForm>`

```vue
<template>
	<div>
        <ElForm ref="formRef" / >
    </div>
</template>
<script setup lang="ts">
import { ref } from 'vue'
import { ElForm } from 'element-plus'
import { useComRef } from 'useComRef.ts'
    
// const formRef = ref<InstanceType<typeof ElForm>>()
const formRef = useComRef<typeof ElForm>()
</script>
```

封装

```ts
import { ref } from 'vue'
export function useComRef<T> () {
    return ref<InstanceType<T>()
}
```

