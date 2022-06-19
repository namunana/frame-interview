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

### $nextTick()

Vue 是异步渲染的

data改变之后，Dom不会立刻渲染

nextTick()会在Dom渲染之后被触发，以获取最新Dom节点

```vue
<template>
  <div id="app">
    <ul ref="ul1">
        <li v-for="(item, index) in list" :key="index">
            {{item}}
        </li>
    </ul>
    <button @click="addItem">添加一项</button>
  </div>
</template>

<script>
export default {
  name: 'app',
  data() {
      return {
        list: ['a', 'b', 'c']
      }
  },
  methods: {
    addItem() {
        this.list.push(`${Date.now()}`)
        this.list.push(`${Date.now()}`)
        this.list.push(`${Date.now()}`)

        // 1. 异步渲染，$nextTick 待 DOM 渲染完再回调
        // 3. 页面渲染时会将 data 的修改做整合，多次 data 修改只会渲染一次
        this.$nextTick(() => {
          // 获取 DOM 元素
          const ulElem = this.$refs.ul1
          // eslint-disable-next-line
          console.log( ulElem.childNodes.length )
        })
    }
  }
}
</script>
```

### slot

基本使用

```vue
<!-- todo-button 组件模板 -->
<button class="btn-primary">
  <slot></slot>
</button>


<todo-button>
  Add todo
</todo-button>
```

作用域插槽

```vue
app.component('todo-list', {
  data() {
    return {
      items: ['Feed a cat', 'Buy milk']
    }
  },
  template: `
    <ul>
      <li v-for="( item, index ) in items">
        <slot :item="item"></slot>
      </li>
    </ul>
  `
})


<todo-list>
  <template v-slot:default="slotProps">
    <i class="fas fa-check"></i>
    <span class="green">{{ slotProps.item }}</span>
  </template>
</todo-list>

//当只有默认插槽时
<todo-list v-slot="slotProps">
  <i class="fas fa-check"></i>
  <span class="green">{{ slotProps.item }}</span>
</todo-list>

```

具名插槽

```vue
<div class="container">
  <header>
    <slot name="header"></slot>
  </header>
  <main>
    <slot></slot>
  </main>
  <footer>
    <slot name="footer"></slot>
  </footer>
</div>

<base-layout>
  <template v-slot:header>
    <h1>Here might be a page title</h1>
  </template>

  <template v-slot:default>
    <p>A paragraph for the main content.</p>
    <p>And another one.</p>
  </template>

  <template v-slot:footer>
    <p>Here's some contact info</p>
  </template>
</base-layout>
```

### 动态组件

is = "component-name"用法

需要根据数据，动态渲染场景，即组件类型不确定

```html
<div id="dynamic-component-demo" class="demo">
  <button
     v-for="tab in tabs"
     :key="tab"
     :class="['tab-button', { active: currentTab === tab }]"
     @click="currentTab = tab"
   >
    {{ tab }}
  </button>

  <component :is="currentTabComponent" class="tab"></component>
</div>
```

```js
const app = Vue.createApp({
  data() {
    return {
      currentTab: 'Home',
      tabs: ['Home', 'Posts', 'Archive']
    }
  },
  computed: {
    currentTabComponent() {
      return 'tab-' + this.currentTab.toLowerCase()
    }
  }
})

app.component('tab-home', {
  template: `<div class="demo-tab">Home component</div>`
})
app.component('tab-posts', {
  template: `<div"></div>`,  
})
app.component('tab-archive', {
  template: `<div class="demo-tab">Archive component</div>`
})

app.mount('#dynamic-component-demo')
```

### 异步组件

import()函数

按需加载，异步加载大组件

```vue
<template>
  <div>
    <p>vue 高级特性</p>
    <hr />
    <!-- 异步组件 -->
    <FormDemo v-if="showFormDemo"/>
    <button @click="showFormDemo = true">show form demo</button>

  </div>
</template>

<script>
export default {
  components: {
    FormDemo: () => import('../BaseUse/FormDemo'),

  },
  data () {
    return {
      showFormDemo: false
    }
  }
}
</script>
```

### keep alive

缓存组件

频繁切换，不需要重复渲染

Vue常见性能优化

```vue
<template>
    <div>
        <button @click="changeState('A')">A</button>
        <button @click="changeState('B')">B</button>
        <button @click="changeState('C')">C</button>

        <keep-alive> <!-- tab 切换 -->
            <KeepAliveStageA v-if="state === 'A'"/> <!-- v-show -->
            <KeepAliveStageB v-if="state === 'B'"/>
            <KeepAliveStageC v-if="state === 'C'"/>
        </keep-alive>
    </div>
</template>

<script>
import KeepAliveStageA from './KeepAliveStateA'
import KeepAliveStageB from './KeepAliveStateB'
import KeepAliveStageC from './KeepAliveStateC'

export default {
    components: {
        KeepAliveStageA,
        KeepAliveStageB,
        KeepAliveStageC
    },
    data() {
        return {
            state: 'A'
        }
    },
    methods: {
        changeState(state) {
            this.state = state
        }
    }
}
</script>

//keepAliveStateA
<template>
    <p>state A</p>
</template>

<script>
export default {
    mounted() {
        // eslint-disable-next-line
        console.log('A mounted')
    },
    destroyed() {
        // eslint-disable-next-line
        console.log('A destroyed')
    }
}
</script>
```

### mixin

多个组件有相同的逻辑，抽离出来

mixin并不是完美的解决方案，会有一些问题

Vue3提出CompositionAPI旨在解决这些问题

```vue
<template>
    <div>
        <p>{{name}} {{major}} {{city}}</p>
        <button @click="showName">显示姓名</button>
    </div>
</template>

<script>
import myMixin from './mixin'

export default {
    mixins: [myMixin], // 可以添加多个，会自动合并起来
    data() {
        return {
            name: '双越',
            major: 'web 前端'
        }
    },
    methods: {
    },
    mounted() {
        // eslint-disable-next-line
        console.log('component mounted', this.name)
    }
}
</script>

```

```js
export default {
    data() {
        return {
            city: '北京'
        }
    },
    methods: {
        showName() {
            // eslint-disable-next-line
            console.log(this.name)
        }
    },
    mounted() {
        // eslint-disable-next-line
        console.log('mixin mounted', this.name)
    }
}
```

mixin问题

变量来源不明确，不利于阅读

多个mixin可能会造成命名冲突

mixin和组件可能会出现多对多的关系，复杂度较高

### vuex

基本概念：state 、getters、action、mutation

用于Vue组件：dispatch、commit、 mapState、mapGetters、mapActions、 mapMutations