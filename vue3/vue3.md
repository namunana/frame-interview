### vue3

#### vue3 比vue2 有什么优势

性能更好

体积更小

更好的ts支持

更好的代码组织

更好的逻辑抽离

更多新功能

#### vue3生命周期

Options API生命周期

Composition API 生命周期



**Options生命周期**

beforeDestroy 改为beforeUnmount

destroyed 改为unmounted

其他沿用vue2生命周期

```vue
<template>
  <p>生命周期 {{ msg }}</p>
</template>

<script>
import { onBeforeMount, onMounted, onBeforeUpdate, onUpdated, onBeforeUnmount, onUnmounted } from 'vue'

export default {
  name: 'LifeCycles',

  props: {
    msg: String
  },

  //CompositionAPI 生命周期
  // 等于 beforeCreate 和 created
  setup () {
    console.log('setup')

    onBeforeMount(() => {
      console.log('onBeforeMount')
    })
    onMounted(() => {
      console.log('onMounted')
    })
    onBeforeUpdate(() => {
      console.log('onBeforeUpdate')
    })
    onUpdated(() => {
      console.log('onUpdated')
    })
    onBeforeUnmount(() => {
      console.log('onBeforeUnmount')
    })
    onUnmounted(() => {
      console.log('onUnmounted')
    })
  },

  //Options 生命周期
  beforeCreate () {
    console.log('beforeCreate')
  },
  created () {
    console.log('created')
  },
  beforeMount () {
    console.log('beforeMount')
  },
  mounted () {
    console.log('mounted')
  },
  beforeUpdate () {
    console.log('beforeUpdate')
  },
  updated () {
    console.log('updated')
  },
  // beforeDestroy 改名
  beforeUnmount () {
    console.log('beforeUnmount')
  },
  // destroyed 改名
  unmounted () {
    console.log('unmounted')
  }
}
</script>
```

