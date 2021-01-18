## 组件




- 创建组件构造器 Vue.extend()
- 注册组件Vue.component()
- 使用组件 

父传子

- props
- $ref
- $children

子传父

- 事件 监听
    - 子组件内$emit("foo", args)
    - 子组件监听@foo

兄弟组件 通过共同父辈组件

```js
this.$parent.$on("foo", handle) //收组件
this.$parent.$emit("foo") //发组件
```

- 事件总线
    - 可以用一个新的Vue实例代替
- Vuex
    - 全局



- 插槽

```vue
<div>
  <slot>匿名</slot>
  <slot name="name">具名</slot>
  <slot bla="bala"></slot>
</div>
```

