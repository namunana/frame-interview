## vue原理

1. 组件化
2. 响应式
3. vdom和diff
4. 模板渲染
5. 渲染过程
6. 前端路由

### 组件化基础

“很久以前”就有组件化

数据驱动视图（MVVM，setState）

asp jsp php 已经有组件化了

nodejs中也有组件化

**数据驱动视图**

传统组件，只是静态渲染，更新还要依赖于操作Dom

数据驱动视图 - Vue MVVM 和 React setState

![mvvm](..\static\img\mvvm.png)



### vue响应式

核心API -  Object.defineProperty

Object.defineProperty的一些缺点（Vue3 启用 Proxy）

proxy有兼容性问题，而且无法polyfill

```js
const data = {}
const name = 'zhangsan'
Object.defineProperty(data, "name", {
	get: function() {
		console.log('get')
		return name
	}
	set: function() {
		console.log('set')
		name = newVal
	}
})

console.log(data.name)  //get zhangsan
data.name = 'lisi' //set
```

监听对象，监听数组

复杂对象，深度监听

几个缺点

### 深度监听

#### vue如何监听对象

适用递归的方式可以监听对象

```js
// 触发更新视图
function updateView() {
    console.log('视图更新')
}

// 重新定义属性，监听起来
function defineReactive(target, key, value) {
    // 深度监听
    observer(value)

    // 核心 API
    Object.defineProperty(target, key, {
        get() {
            return value
        },
        set(newValue) {
            if (newValue !== value) {
                // 深度监听
                observer(newValue)

                // 设置新值
                // 注意，value 一直在闭包中，此处设置完之后，再 get 时也是会获取最新的值
                value = newValue

                // 触发更新视图
                updateView()
            }
        }
    })
}

// 监听对象属性
function observer(target) {
    if (typeof target !== 'object' || target === null) {
        // 不是对象或数组
        return target
    }

    // 重新定义各个属性（for in 也可以遍历数组）
    for (let key in target) {
        defineReactive(target, key, target[key])
    }
}

// 准备数据
const data = {
    name: 'zhangsan',
    age: 20,
    info: {
        address: '北京' // 需要深度监听
    }
}

// 监听数据
observer(data)
```

#### vue如何监听数组

```js
// 重新定义数组原型
const oldArrayProperty = Array.prototype
// 创建新对象，原型指向 oldArrayProperty ，再扩展新的方法不会影响原型
const arrProto = Object.create(oldArrayProperty);
['push', 'pop', 'shift', 'unshift', 'splice'].forEach(methodName => {
    arrProto[methodName] = function () {
        oldArrayProperty[methodName].call(this, ...arguments)
        // Array.prototype.push.call(this, ...arguments)
        updateView() // 触发视图更新
    }
})

// 监听对象属性
function observer (target) {
    if (typeof target !== 'object' || target === null) {
        // 不是对象或数组
        return target
    }

    // 污染全局的 Array 原型
    // Array.prototype.push = function () {
    //     updateView()
    //     ...
    // }

    if (Array.isArray(target)) {
        target.__proto__ = arrProto
    }

    // 重新定义各个属性（for in 也可以遍历数组）
    for (let key in target) {
        defineReactive(target, key, target[key])
    }
}

// 准备数据
const data = {
    name: 'zhangsan',
    age: 20,
    info: {
        address: '北京' // 需要深度监听
    },
    nums: [10, 20, 30]
}

// 监听数据
observer(data)

data.nums.push(4) // 监听数组
```

