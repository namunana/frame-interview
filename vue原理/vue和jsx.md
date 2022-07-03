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

