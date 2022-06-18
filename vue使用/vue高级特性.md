## vue高级特性

分别有哪些？

1. 自定义v-model
2. $nextTick
3. slot
4. 动态、异步组件
5. keep-alive
6. mixin

### v-model

代码演示

```vue
<my-component v-model:title="bookTitle"></my-component>

app.component('my-component', {
  props: {
    title: String
  },
  emits: ['update:title'],
  template: `
    <input
      type="text"
      :value="title"
      @input="$emit('update:title', $event.target.value)">
  `
})
```

