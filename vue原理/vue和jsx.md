## vue3和jsx

###  vue3和jsx

vue3中jsx的基本使用

jsx和template的区别

jsx和slot（体会jsx的优势）

注意：jsx最早是由react提出的概念（现在已经发展壮大）

#### vue3中jsx的基本使用

使用.jsx格式文件和defineComponent

引用自定义组件，传递属性

**在vue中使用jsx**

```vue
<script>
import { ref } from 'vue'
import Child from './Child'

export default {
    components: { Child },
    setup() {
        const countRef = ref(200)

        const render = () => {
            return <p>demo1 {countRef.value}</p> // jsx
        }
        return render
    }
}
</script>
```

单独的jsx文件

```jsx
import { defineComponent, ref, reactive } from 'vue'
import Child from './Child'

export default defineComponent(() => {
  const render = () => {
    return (
      <>
 		<Child a={100}></Child> //使用时，首字母必须大写
       
      </>
    )
  }
  return render
})

// 1. setup 函数
// 2. 组件的配置
```

```jsx
import { defineComponent } from 'vue'

export default defineComponent({
    props: ['a'],
    setup(props) {
        const render = () => {
            return <p>Child {props.a}</p>
        }
        return render
    }
})
```

#### jsx和template的区别

语法上有很大的区别

本质相同

具体示例：插值，自定义组件，属性和事件，条件和循环

**语法区别：**

jsx本质就是js代码，可以使用js的任何能力

template只能嵌入简单的js表达式，其他需要指令，如v-if

jsx已经成为ES规范，template还是vue自家规范

**两者本质相同：**

都会被编译为js代码（render）

#### 具体示例：插值，自定义组件，属性和事件，条件和循环

插值，template{{}}，jsx{}

引入，template小写或小写加-线方式，jsx组件引入格式和使用格式必须一致

属性和事件，template，静态属性，用属性名，动态属性用 :属性名，jsx直接就是属性名

条件和循环

```jsx
import { defineComponent, ref, reactive } from 'vue'
import Child from './Child'

export default defineComponent(() => {
  const flagRef = ref(true)

  function changeFlag() {
    flagRef.value = !flagRef.value
  }

  const state = reactive({
    list: ['a1', 'b1', 'c1'],
  })

  const render = () => {
    return (
      <>
        <p onClick={changeFlag}>demo1 {flagRef.value.toString()}</p>
        {flagRef.value && <Child a={flagRef.value}></Child>}

        <ul>
          {state.list.map((item) => (
            <li>{item}</li>
          ))}
        </ul>
      </>
    )
  }
  return render
```

#### jsx实现slot

```vue
<template>
    <tabs default-active-key="1" @change="onTabsChange">
        <tab-panel key="1" title="title1">
            <div>tab panel content 1</div>
        </tab-panel>
        <tab-panel key="2" title="title2">
            <div>tab panel content 2</div>
        </tab-panel>
        <tab-panel key="3" title="title3">
            <div>tab panel content 3</div>
        </tab-panel>
    </tabs>
</template>

<script>
import Tabs from './Tabs.jsx'
import TabPanel from './TabPanel'

export default {
    components: { Tabs, TabPanel },
    methods: {
        onTabsChange(key) {
            console.log('tab changed', key)
        }
    },
}
</script>
```

```vue
<template>
    <slot></slot>
</template>

<script>
export default {
    name: 'TabPanel',
    props: ['key', 'title'],
}
</script>
```

```jsx
import { defineComponent, ref } from 'vue'

export default defineComponent({
    name: 'Tabs',
    props: ['defaultActiveKey'],
    emits: ['change'],
    setup(props, context) {
        const children = context.slots.default()
        const titles = children.map(panel => {
            const { key, title } = panel.props || {}
            return {
                key,
                title
            }
        })

        // 当前 actKey
        const actKey = ref(props.defaultActiveKey)
        function changeActKey(key) {
            actKey.value = key
            context.emit('change', key)
        }

        // jsx
        const render = () => <>
            <div>
                {/* 渲染 buttons */}
                {titles.map(titleInfo => {
                    const { key, title } = titleInfo
                    return <button
                        key={key}
                        style={{ color: actKey.value === key ? 'blue' : '#333' }}
                        onClick={() => changeActKey(key)}
                    >{title}</button>
                })}
            </div>

            <div>
                {children.filter(panel => {
                    const { key } = panel.props || {}
                    if (actKey.value === key) return true // 匹配上 key ，则显示
                    return false // 否则，隐藏
                })}
            </div>
        </>
        return render
    }
})
```

#### template和jsx实现作用域插槽对比

template:

```vue
<template>
    <child>
        <!-- <p>scoped slot</p> -->

        <template v-slot:default="slotProps">
            <p>msg: {{slotProps.msg}} 123123</p>
        </template>
    </child>
</template>

<script>
import Child from './Child'

export default {
    components: { Child }
}
</script>
```

```vue
<template>
    <p>child</p>
    <slot :msg="msg"></slot>
</template>

<script>
export default {
    data() {
        return {
            msg: '作用域插槽 Child'
        }
    }
}
</script>
```

jsx:

```jsx
import { defineComponent } from 'vue'
import Child from './Child'

export default defineComponent(() => {
    function render(msg) {
        return <p>msg: {msg} 123123</p>
    }

    return () => {
        return <>
            <p>Demo - JSX</p>
            <Child render={render}></Child>
        </>
    }
})
```

```jsx
import { defineComponent, ref } from 'vue'

export default defineComponent({
    props: ['render'],
    setup(props) {
        const msgRef = ref('作用域插槽 Child - JSX')

        return () => {
            return <p>{props.render(msgRef.value)}</p>
        }
    }
})
```

#### 总结：

vue3中jsx的基本应用

jsx和template的区别

jsx和slot

### vue3 和 script-setup

#### 基本使用：

顶级变量、自定义组件，可以直接用于模板

可以正常使用ref reactive computed等能力

和其他`<script>`同时使用

注意：vue版本必须 > 3.2

```vue
<script>
function add(a, b) { return a + b }
</script>

<script setup>
import { ref, reactive, toRefs } from 'vue'
import Child1 from './Child1'


const countRef = ref(100)

function addCount() {
    countRef.value++
}

const state = reactive({
    name: '双越'
})
const { name } = toRefs(state)

console.log( add(10, 20) )

</script>

<template>
    <p @click="addCount">{{countRef}}</p>
    <p>{{name}}</p>
    <child-1></child-1>
</template>
```

```vue
<script setup>
</script>

<template>
    <p>Child1</p>
</template>

```

#### 属性和事件

通过 defineProps, defineEmits 来定义属性和事件

```vue
<script setup>
import { defineProps, defineEmits } from 'vue'

// 定义属性
const props = defineProps({
    name: String,
    age: Number
})

// 定义事件
const emit = defineEmits(['change', 'delete'])
function deleteHandler() {
    emit('delete', 'aaa')
}

</script>

<template>
    <p>Child2 - name: {{props.name}}, age: {{props.age}}</p>
    <button @click="$emit('change', 'bbb')">change</button>
    <button @click="deleteHandler">delete</button>
</template>
```

```vue
<script setup>
import Child2 from './Child2'

function onChange(info) {
    console.log('on change', info)
}
function onDelete(info) {
    console.log('on delete', info)
}


</script>

<template>


    <child-2 :name="name" :age="countRef" @change="onChange" @delete="onDelete"></child-2>
    <hr>
</template>
```

#### 暴露组件信息

通过defineExpose属性暴露

```vue
<script setup>
import { ref, defineExpose } from 'vue'

const a = ref(101)
const b = 201

defineExpose({
    a,
    b
})

</script>

<template>
    <p>Child3</p>
</template>
```

```vue
<script setup>
import { ref, onMounted } from 'vue'

import Child3 from './Child3'


const child3Ref = ref(null)
onMounted(() => {
    // 拿到 Child3 组件的一些数据
    console.log(child3Ref.value)
    console.log(child3Ref.value.a)
    console.log(child3Ref.value.b)
})

</script>

<template>
    <child-3 ref="child3Ref"></child-3>
</template>

```

#### 总结：

基本使用 `<script>`写在`<template>`前面

定义属性defineProps，定义事件defineEmits

defineExpose暴露数据给父组件