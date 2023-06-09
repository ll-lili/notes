#### uni-app 使用样式穿透（::v-deep）修改uni-ui组件样式，在小程序中不生效

```vue
<script>
// 添加如下代码即可
export default {
  options: {
    styleIsolation: 'shared' // 微信小程序解除组件样式隔离
  }
}
</script>
```

####  uni-app(vue3)设置proxy: vite.config.js

```javascript
// vite.confog.js
import { defineConfig } from 'vite'
import uni from '@dcloudio/vite-plugin-uni'

export default defineConfig({
	plugins: [uni()],
	server: {
	  proxy: {
	    '/test': { // 此处写'/api'，会报错，暂不知道原因
				target: 'http://127.0.0.1:3001',
				changeOrigin: true,
				rewrite: (path) => path.replace(/^\/test/, '')
			}
	  }
	}
})

```

#### uni-app事件对象（微信小程序获取不到$el）

* 使用uni.createSelectorQuery().in(this)获取节点信息
* https://uniapp.dcloud.net.cn/api/ui/nodes-info.html#createselectorquery
* 不要将uni.createSelectorQuery().in(this)保存在变量使用，而是直接调用起方法
* 否则会出现多次调用
*  wx.createSelectorQuery()同理

#### uni-search-bar

* https://uniapp.dcloud.net.cn/component/uniui/uni-search-bar.html
* 文档是针对vue2的
* value属性在vue3下是不起作用的要使用modelValue

#### uni-app小程序不支持$attrs

#### uni-app小程序不支持<component :is="">

