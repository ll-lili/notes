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

