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

##### toRef

针对一个响应式对象（reactive封装）的prop

创建一个ref，具有响应式

两者保持引用关系

```vue
<template>
    <p>toRef demo - {{ageRef}} - {{state.name}} {{state.age}}</p>
</template>

<script>
import { ref, toRef, reactive } from 'vue'

export default {
    name: 'ToRef',
    setup() {
        const state = reactive({
            age: 20,
            name: '双越'
        })

        const age1 = computed(() => {
            return state.age + 1
        })

        // // toRef 如果用于普通对象（非响应式对象），产出的结果不具备响应式
        // const state = {
        //     age: 20,
        //     name: '双越'
        // }

        const ageRef = toRef(state, 'age')

        setTimeout(() => {
            state.age = 25
        }, 1500)

        setTimeout(() => {
            ageRef.value = 30 // .value 修改值
        }, 3000)

        return {
            state,
            ageRef
        }
    }
}
</script>
```

##### toRefs

将响应式对象（reactive封装）转换为普通对象

对象的每个prop都是对应的ref

两者保持应用关系

```vue
<template>
    <p>toRefs demo {{age}} {{name}}</p>
</template>

<script>
import { ref, toRef, toRefs, reactive } from 'vue'

export default {
    name: 'ToRefs',
    setup() {
        const state = reactive({
            age: 20,
            name: '双越'
        })

        const stateAsRefs = toRefs(state) // 将响应式对象，变成普通对象

        // const { age: ageRef, name: nameRef } = stateAsRefs // 每个属性，都是 ref 对象
        // return {
        //     ageRef,
        //     nameRef
        // }

        setTimeout(() => {
            state.age = 25
        }, 1500)

        return stateAsRefs
    }
}
</script>
```

##### toRef 和 toRefs最佳使用方式

合成函数返回响应式对象

```js
function useFeatureX(){
	const state = reactive({
		x:1,
		y:2
	})
	
	return toRefs(state)
}
```

```js
export defualt {
	setup(){
		const {x,y} = useFeatureX()
		
		return {
			x,
			y
		}
	}
}
```

最佳使用方式

1. 用reactive做对象的响应式，用ref做值类型的响应式
2. setup中返回toRefs（state），或者toRef（state，"xxx"）
3. ref的变量命名都用xxxRef
4. 合成函数返回响应式对象

#### ref、toRef和toRefs深入理解

为何需要ref

为何需要.value

为何需要toRef toRefs

##### 为何需要ref

1. 返回值类型，会丢失响应式
2. 如在setup、computed、合成函数，都有可能返回值类型
3. Vue如不定义ref，用户将自造ref，反而混乱

##### 为何需要.value

ref是一个对象（不丢失响应式），value存储值

通过.value属性的get和set实现响应式

用于模板、reactive时不需要.value，其他情况都需要.value

最主要的时值类型不具备响应式，而对象具备

```js
//仿computed对响应式处理的实现思路
//错误的实现方式
function computed(getter){
	let value = 0
	setTimeout(()=>{
		value = getter()
	},1500)
	return value
}
a = computed(() => 100) //过了1.5秒 a = 0

//正确的实现方式
function computed(getter){
	let ref = {
		value:null
	}
	setTimeout(()=>{
		value = getter()
	},1500)
	return value
}

a = computed(() => 100) //过了1.5秒 a = 100
```

##### 为何需要toRef和toRefs

初衷：不丢失响应式的情况下，把对象数据 **分解/扩散**（如合成函数的返回和响应式数据的返回）

前提：针对的是响应式对象（reactive封装），非普通对象

**不创造**响应式，而是**延续**响应式

#### vue3升级了哪些重要功能

1. createApp
2. emits属性
3. 生命周期
4. 多事件
5. Fragment
6. 移除.sync
7. 异步组件的写法
8. 移除filter
9. Teleport
10. Suspense
11. CompositionAPI

##### createApp

```js
//vue2.x
const app = new Vue({})

//vue3
const app = Vue.createApp({})

//vue2.x
Vue.use()
Vue.mixin()
Vue.component()
Vue.directive()

//vue3
app.use()
app.mixin()
app.component()
app.directive()
```

##### emits属性

```js
<HelloWorl :msg="msg" @sayHello="sayHello"/>
```

```js
export default{
	name: 'HelloWorld',
	props: {
		msg:String
	},
	emits:['sayHello'],
	setup(props,{emit}){
		function clickHandle(){
			emit('sayHello','abc')
		}
	}
}
```

##### 多事件处理

```html
<button @click="one($event),two($event)">Submit</button>
```

##### Fragment

```html
//vue2
<template>
	<div class="blog-post">
		<h3>{{title}}</h3>
		<div v-html="content"></div>
	</div>
</template>
vue3
<template>
    <h3>{{title}}</h3>
    <div v-html="content"></div>
</template>
```

##### 移除.sync

```html
//vue2
<MyComponent v-bind:title.sync="title"/>
//vue3
<MyComponent v-model:title="title" />
```

##### 异步组件

```js
//vue2
new Vue({
	components:{
		'my-component':()=>import('./my-async-component.vue')
	}
})
//vue3
import{createApp,defineAsyncComponent} from 'vue'
createApp({
	components:{
		AsyncComponent:defineAsyncComponent(() =>
			import('./components/AsyncComponent.vue')
		)
	}
})
```

##### 移除filter

```html
//以下在vue3不可用
{{message | capitalize}}

<div v-bind:id="rawId | formatId"></div>
```

##### Teleport

```html
<button @click="modalOpen=true">Open</button>

<teleport>
	<div v-if="modalOpen">
		<div>
			telePort弹窗
			<button @click="modalOpent = false">Close</button>
		</div>
	</div>
</teleport>
```

##### Suspence

```vue
<template>
  <suspense>
    <template #default>
      <todo-list />
    </template>
    <template #fallback>
      <div>
        Loading...
      </div>
    </template>
  </suspense>
</template>

<script>
export default {
  components: {
    TodoList: defineAsyncComponent(() => import('./TodoList.vue'))
  }
}
</script>
```

##### CompositionAPI

reactive

ref相关

readonly

watch和watchEffect

setup

生命周期钩子函数

#### CompositionAPI逻辑复用

抽离逻辑代码到一个函数

函数命名约定为useXxx格式（React Hooks 也是）

在setup中引用useXxx函数

```js
import { reactive, ref, onMounted, onUnmounted } from 'vue'

function useMousePosition() {
    const x = ref(0)
    const y = ref(0)

    function update(e) {
        x.value = e.pageX
        y.value = e.pageY
    }

    onMounted(() => {
        console.log('useMousePosition mounted')
        window.addEventListener('mousemove', update)
    })

    onUnmounted(() => {
        console.log('useMousePosition unMounted')
        window.removeEventListener('mousemove', update)
    })

    return {
        x,
        y
    }
}

// function useMousePosition2() {
//     const state = reactive({
//         x: 0,
//         y: 0
//     })

//     function update(e) {
//         state.x = e.pageX
//         state.y = e.pageY
//     }

//     onMounted(() => {
//         console.log('useMousePosition mounted')
//         window.addEventListener('mousemove', update)
//     })

//     onUnmounted(() => {
//         console.log('useMousePosition unMounted')
//         window.removeEventListener('mousemove', update)
//     })

//     return state
// }

export default useMousePosition
// export default useMousePosition2

```

```vue
<template>
    <p>mouse position {{x}} {{y}}</p>
</template>

<script>
import { reactive } from 'vue'
import useMousePosition from './useMousePosition'
// import useMousePosition2 from './useMousePosition'

export default {
    name: 'MousePosition',
    setup() {
        const { x, y } = useMousePosition()
        return {
            x,
            y
        }

        // const state = useMousePosition2()
        // return {
        //     state
        // }
    }
}
</script>
```

#### proxy实现响应式

**Object.defineProperty的缺点**

深度监听需要一次性递归

无法监听新增属性/删除属性（Vue.set  Vue.delete）

无法原生监听数组，需要特殊处理

**Proxy实现响应式**

基本使用

Reflect语法

实现响应式

```js
// const data = {
//     name: 'zhangsan',
//     age: 20,
// }
const data = ['a', 'b', 'c']

const proxyData = new Proxy(data, {
    get(target, key, receiver) {
        // 只处理本身（非原型的）属性
        const ownKeys = Reflect.ownKeys(target)
        if (ownKeys.includes(key)) {
            console.log('get', key) // 监听
        }

        const result = Reflect.get(target, key, receiver)
        return result // 返回结果
    },
    set(target, key, val, receiver) {
        // 重复的数据，不处理
        if (val === target[key]) {
            return true
        }

        const result = Reflect.set(target, key, val, receiver)
        console.log('set', key, val)
        // console.log('result', result) // true
        return result // 是否设置成功
    },
    deleteProperty(target, key) {
        const result = Reflect.deleteProperty(target, key)
        console.log('delete property', key)
        // console.log('result', result) // true
        return result // 是否删除成功
    }
})


```

**Reflect作用**

和Proxy能力——对应

规范化、标准化、函数式

代替Object上的工具函数

```js
// 创建响应式
function reactive (target = {}) {
    if (typeof target !== 'object' || target == null) {
        console.log("不是数组或对象")
        // 不是对象或数组，则返回
        return target
    }

    // 代理配置
    const proxyConf = {
        get (target, key, receiver) {
            // 只处理本身（非原型的）属性
            const ownKeys = Reflect.ownKeys(target)
            if (ownKeys.includes(key)) {
                console.log('get', key) // 监听
            }

            const result = Reflect.get(target, key, receiver)
            console.log("result", result)

            // 深度监听
            // 性能如何提升的？
            return reactive(result)
        },
        set (target, key, val, receiver) {
            // 重复的数据，不处理
            if (val === target[key]) {
                return true
            }

            const ownKeys = Reflect.ownKeys(target)
            if (ownKeys.includes(key)) {
                console.log('已有的 key', key)
            } else {
                console.log('新增的 key', key)
            }

            const result = Reflect.set(target, key, val, receiver)
            console.log('set', key, val)
            // console.log('result', result) // true
            return result // 是否设置成功
        },
        deleteProperty (target, key) {
            const result = Reflect.deleteProperty(target, key)
            console.log('delete property', key)
            // console.log('result', result) // true
            return result // 是否删除成功
        }
    }

    // 生成代理对象
    const observed = new Proxy(target, proxyConf)
    console.log("observed", observed)
    return observed
}

// 测试数据
const data = {
    name: 'zhangsan',
    age: 20,
    info: {
        city: 'beijing',
        a: {
            b: {
                c: {
                    d: {
                        e: 100
                    }
                }
            }
        }
    }
}

const proxyData = reactive(data)

```

优点：

深度监听，性能更好

可监听 新增/删除 属性

可监听数组变化

总结：

Proxy能规避Object.defineProperty的问题

Proxy无法兼容所有浏览器，无法polyfill

#### v-model参数用法

```vue
<template>
    <p>{{name}} {{age}}</p>

    <user-info
        v-model:name="name"
        v-model:age="age"
    ></user-info>
</template>

<script>
import { reactive, toRefs } from 'vue'
import UserInfo from './UserInfo.vue'

export default {
    name: 'VModel',
    components: { UserInfo },
    setup() {
        const state = reactive({
            name: '双越',
            age: '20'
        })

        return toRefs(state)
    }
}
</script>
```

```vue
<template>
    <input :value="name" @input="$emit('update:name', $event.target.value)"/>
    <input :value="age" @input="$emit('update:age', $event.target.value)"/>
</template>

<script>
export default {
    name: 'UserInfo',
    props: {
        name: String,
        age: String
    }
}
</script>
```

#### watch 和watchEffect的区别

两者都可以监听data属性的变化

watch需要明确监听哪个属性，watch只有数据变化的时候才会执行

watchEffect会根据其中的属性，自动监听其变化 ，watchEffect初始化的时候会执行一次（依赖收集）

**watch**

```vue
<template>
    <p>watch vs watchEffect</p>
    <p>{{numberRef}}</p>
    <p>{{name}} {{age}}</p>
</template>

<script>
import { reactive, ref, toRefs, watch, watchEffect } from 'vue'

export default {
    name: 'Watch',
    setup() {
        const numberRef = ref(100)
        const state = reactive({
            name: '双越',
            age: 20
        })
        

		watch(numberRef, (newNumber, oldNumber) => {
            console.log('ref watch', newNumber, oldNumber)
        }
        // , {
        //     immediate: true // 初始化之前就监听，可选
        // }
        )

        setTimeout(() => {
            numberRef.value = 200
        }, 1500)

        watch(
            // 第一个参数，确定要监听哪个属性
            () => state.age,

            // 第二个参数，回调函数
            (newAge, oldAge) => {
                console.log('state watch', newAge, oldAge)
            },

            // 第三个参数，配置项
            {
                immediate: true, // 初始化之前就监听，可选
                // deep: true // 深度监听
            }
        )

        setTimeout(() => {
            state.age = 25
        }, 1500)
        setTimeout(() => {
            state.name = '双越A'
        }, 3000)

        return {
            numberRef,
            ...toRefs(state)
        }
    }
}
</script>
```

**watchEffect**

```js
watchEffect(() => {
    // 初始化时，一定会执行一次（收集要监听的数据）
    console.log('hello watchEffect')
})
watchEffect(() => {
    console.log('state.name', state.name)
})
watchEffect(() => {
    console.log('state.age', state.age)
})
watchEffect(() => {
    console.log('state.age', state.age)
    console.log('state.name', state.name)
})
setTimeout(() => {
    state.age = 25
}, 1500)
setTimeout(() => {
    state.name = '双越A'
}, 3000)
```

#### setup中如何获取组件实例

在setup和其他CompositionAPI中没有this

可通过getCurrentInstance获取组件实例

若使用OptionsAPI可以照常使用this

```vue
<template>
    <p>get instance</p>
</template>

<script>
import { onMounted, getCurrentInstance } from 'vue'

export default {
    name: 'GetInstance',
    data() {
        return {
            x: 1,
            y: 2
        }
    },
    setup() {
        console.log('this1', this)

        onMounted(() => {
            console.log('this in onMounted', this)
            console.log('x', instance.data.x)
        })

        const instance = getCurrentInstance()
        console.log('instance', instance)
    },
    mounted() {
        console.log('this2', this)
        console.log('y', this.y)
    }
}
</script>
```

#### vue3为何比vue2快

1. proxy响应式
2. PatchFlag
3. hoistStatic
4. cacheHandler
5. SSR优化
6. tree-shaking

##### PatchFlag

静态标记

编译模板时，动态节点做标记

标记，分为不同类型，如TEXT PROPS

diff算法时，可以区分静态节点，以及不同类型的动态节点

```html
<div>
  <span>hello World!</span>
  <span>{{msg}}</span>
  <span :class="name"></span>
  <span :id="name"></span>
  <span :id="name" :class="nana" :msg="ui"></span>
</div>
```

以上代码编译后

```js
import { createElementVNode as _createElementVNode, toDisplayString as _toDisplayString, normalizeClass as _normalizeClass, openBlock as _openBlock, createElementBlock as _createElementBlock } from "vue"

export function render(_ctx, _cache, $props, $setup, $data, $options) {
  return (_openBlock(), _createElementBlock("div", null, [
    _createElementVNode("span", null, "hello World!"),
    _createElementVNode("span", null, _toDisplayString(_ctx.msg), 1 /* TEXT */),
    _createElementVNode("span", {
      class: _normalizeClass(_ctx.name)
    }, null, 2 /* CLASS */),
    _createElementVNode("span", { id: _ctx.name }, null, 8 /* PROPS */, ["id"]),
    _createElementVNode("span", {
      id: _ctx.name,
      class: _normalizeClass(_ctx.nana),
      msg: _ctx.ui
    }, null, 10 /* CLASS, PROPS */, ["id", "msg"])
  ]))
}
```

编译后都会有一个静态标记，如插值表达式是1，`:class`属性是2，等，vue2是静态和动态节点都要比对，vue3只比对动态节点，即被标记了PatchFlag的节点

##### hoistStatic

将静态节点的定义，提升到父作用域，缓存起来

多个相邻的静态节点，会被合并起来

典型的拿空间换时间的优化策略

```html
<div>
  <span>hello vue3</span>
  <span>hello vue3</span>
  <span>hello vue3</span>
  <span>hello vue3</span>
  <span>hello vue3</span>
  <span>hello vue3</span>
  <span>hello vue3</span>
</div>
```

经过编译后

```js
import { createElementVNode as _createElementVNode, openBlock as _openBlock, createElementBlock as _createElementBlock } from "vue"

const _hoisted_1 = /*#__PURE__*/_createElementVNode("span", null, "hello vue3", -1 /* HOISTED */)
const _hoisted_2 = /*#__PURE__*/_createElementVNode("span", null, "hello vue3", -1 /* HOISTED */)
const _hoisted_3 = /*#__PURE__*/_createElementVNode("span", null, "hello vue3", -1 /* HOISTED */)
const _hoisted_4 = /*#__PURE__*/_createElementVNode("span", null, "hello vue3", -1 /* HOISTED */)
const _hoisted_5 = /*#__PURE__*/_createElementVNode("span", null, "hello vue3", -1 /* HOISTED */)
const _hoisted_6 = /*#__PURE__*/_createElementVNode("span", null, "hello vue3", -1 /* HOISTED */)
const _hoisted_7 = /*#__PURE__*/_createElementVNode("span", null, "hello vue3", -1 /* HOISTED */)
const _hoisted_8 = [
  _hoisted_1,
  _hoisted_2,
  _hoisted_3,
  _hoisted_4,
  _hoisted_5,
  _hoisted_6,
  _hoisted_7
]

export function render(_ctx, _cache, $props, $setup, $data, $options) {
  return (_openBlock(), _createElementBlock("div", null, _hoisted_8))
}
```

多个相邻静态节点合并

```js
import { createElementVNode as _createElementVNode, createStaticVNode as _createStaticVNode, openBlock as _openBlock, createElementBlock as _createElementBlock } from "vue"

const _hoisted_1 = /*#__PURE__*/_createStaticVNode("<span>hello vue3</span><span>hello vue3</span><span>hello vue3</span><span>hello vue3</span><span>hello vue3</span><span>hello vue3</span><span>hello vue3</span><span>hello vue3</span><span>hello vue3</span><span>hello vue3</span><span>hello vue3</span><span>hello vue3</span><span>hello vue3</span><span>hello vue3</span>", 14)
const _hoisted_15 = [
  _hoisted_1
]

export function render(_ctx, _cache, $props, $setup, $data, $options) {
  return (_openBlock(), _createElementBlock("div", null, _hoisted_15))
}

// Check the console for the AST
```

##### cacheHandler

缓存时间

```html
<div>
  <span v-on:click="handleClick">hello vue3</span>
</div>
```

编译后

```js
import { createElementVNode as _createElementVNode, openBlock as _openBlock, createElementBlock as _createElementBlock } from "vue"

export function render(_ctx, _cache, $props, $setup, $data, $options) {
  return (_openBlock(), _createElementBlock("div", null, [
    _createElementVNode("span", {
      onClick: _cache[0] || (_cache[0] = (...args) => (_ctx.handleClick && _ctx.handleClick(...args)))
    }, "hello vue3")
  ]))
}

// Check the console for the AST

//先读取缓存里的时间，如果有，直接使用，如果没有，会自定义一个时间到缓存用于下次读取
```

