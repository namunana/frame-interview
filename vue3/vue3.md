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

#### CompositionAPI 对比OptionsAPI

CompositionAPI 带来了什么？

CompositionAPI和OptionsAPI如何选择？

别误解CompositionAPI



**CompositionAPI 带来了什么？**

更好的代码组织

![comopt](..\static\img\comopt.png)

更好的逻辑复用

​	可以把相同的逻辑写在一个hook函数里，只要引用函数即可

更好的类型推导

​	vue2中

```js
export default{
	data(){
		return {
			a: 10
		}
	},
	methods:{
		fn1(){
			const a = this.a
			return a
		}
	},
	mounted(){
		this.fn1 //此时不能判断得到的值
	}
}
```

   vue3中

```js
export default{
	setup(){
		const {age,name} = useUser(); //通过hook函数引入变量，可以明确的知道变量
	}
}
```

**如何选择**

不建议共用，会引起混乱

小型项目、业务逻辑简单，用OptionsAPI

中型项目、逻辑复杂，用CompositionAPI

**别误解CompositionAPI**

CompositionAPI是为了解决复杂业务逻辑设计的

CompositionAPI就像Hooks在React中的地位

#### ref toRef和toRefs

##### ref

生成值类型的响应式数据

可用于模板和reactive

通过.value修改值

```vue
<template>
  <p>ref demo {{ ageRef }} {{ state.name }}</p>
</template>

<script>
import { ref, reactive } from 'vue'

export default {
  name: 'Ref',
  setup () {
    const ageRef = ref(20) // 值类型 响应式
    const nameRef = ref('namu')

    const state = reactive({
      name: nameRef
    })

    setTimeout(() => {
      console.log('ageRef', ageRef.value)

      ageRef.value = 25 // .value 修改值
      nameRef.value = 'namunana'
    }, 1500)

    return {
      ageRef,
      state
    }
  }
}
</script>
```

```vue
<template>
    <p ref="elemRef">我是一行文字</p>
</template>

<script>
import { ref, onMounted } from 'vue'

export default {
    name: 'RefTemplate',
    setup() {
        const elemRef = ref(null)

        onMounted(() => {
            console.log('ref template', elemRef.value.innerHTML, elemRef.value)
        })

        return {
            elemRef
        }
    }
}
</script>
```

