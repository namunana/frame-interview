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

### 虚拟DOM

vdom(virtualDOM)是实现vue和react的重要基石

diff算法是vdom中最核心，最关键的部分



DOM操作非常消耗性能

以前用JQuery，可以控制DOM操作的时机，手动调整

Vue和React是数据驱动视图



有了一定复杂度，想要减少计算次数比较困难

能不能把计算，更多的转移为JS计算，因为JS执行速度很快

vdom - 用JS模拟DOM结构，计算出最小的变更，操作DOM



如何用JS模拟DOM结构

```html
<div id="div1" class="container">
	<P>vdom</p>
	<ul style="font-size:20px">
		<li>a</li>
	</ul>
</div>
```

```json
{
	"tag":'div',
	"props":{
		"className":'container',
		"id":1
	},
	"children":[
		{
			"tag":'p',
			"children":'vdom'
		},
		{
			"tag":'ul',
			"props": {"style":'font-size: 20px'},
			"children":[
				{
					"tag":'li',
					"children": 'a'
				}
			]
		}
	]
}
```

通过snabbdom学习vdom

简洁强大的vdom库，易学易用

Vue参考它实现的vdom的diff



vue3.0重写了vdom的代码，优化了性能，但vdom的基本理念不变

snabbdom-demo

```js
const snabbdom = window.snabbdom

// 定义 patch
const patch = snabbdom.init([
    snabbdom_class,
    snabbdom_props,
    snabbdom_style,
    snabbdom_eventlisteners
])

// 定义 h
const h = snabbdom.h

const container = document.getElementById('container')

// 生成 vnode
const vnode = h('ul#list', {}, [
    h('li.item', {}, 'Item 1'),
    h('li.item', {}, 'Item 2')
])
patch(container, vnode)

document.getElementById('btn-change').addEventListener('click', () => {
    // 生成 newVnode
    const newVnode = h('ul#list', {}, [
        h('li.item', {}, 'Item 1'),
        h('li.item', {}, 'Item B'),
        h('li.item', {}, 'Item 3')
    ])
    patch(vnode, newVnode)

    // vnode = newVnode // patch 之后，应该用新的覆盖现有的 vnode ，否则每次 change 都是新旧对比
})

```

传统的重新渲染

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
    <div id="container"></div>
    <button id="btn-change">change</button>

    <script type="text/javascript" src="https://cdn.bootcss.com/jquery/3.2.0/jquery.js"></script>
    <script type="text/javascript">
        const data = [
            {
                name: '张三',
                age: '20',
                address: '北京'
            },
            {
                name: '李四',
                age: '21',
                address: '上海'
            },
            {
                name: '王五',
                age: '22',
                address: '广州'
            }
        ]

        // 渲染函数
        function render(data) {
            const $container = $('#container')

            // 清空容器，重要！！！
            $container.html('')

            // 拼接 table
            const $table = $('<table>')

            $table.append($('<tr><td>name</td><td>age</td><td>address</td>/tr>'))
            data.forEach(item => {
                $table.append($('<tr><td>' + item.name + '</td><td>' + item.age + '</td><td>' + item.address + '</td>/tr>'))
            })

            // 渲染到页面
            $container.append($table)
        }

        $('#btn-change').click(() => {
            data[1].age = 30
            data[2].address = '深圳'
            // re-render  再次渲染
            render(data)
        })

        // 页面加载完立刻执行（初次渲染）
        render(data)

    </script>
</body>
</html>
```

使用vdom的重新渲染

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
    <div id="container"></div>
    <button id="btn-change">change</button>

    <script src="https://cdn.bootcss.com/snabbdom/0.7.3/snabbdom.js"></script>
    <script src="https://cdn.bootcss.com/snabbdom/0.7.3/snabbdom-class.js"></script>
    <script src="https://cdn.bootcss.com/snabbdom/0.7.3/snabbdom-props.js"></script>
    <script src="https://cdn.bootcss.com/snabbdom/0.7.3/snabbdom-style.js"></script>
    <script src="https://cdn.bootcss.com/snabbdom/0.7.3/snabbdom-eventlisteners.js"></script>
    <script src="https://cdn.bootcss.com/snabbdom/0.7.3/h.js"></script>
    <script type="text/javascript">
        const snabbdom = window.snabbdom
        // 定义关键函数 patch
        const patch = snabbdom.init([
            snabbdom_class,
            snabbdom_props,
            snabbdom_style,
            snabbdom_eventlisteners
        ])

        // 定义关键函数 h
        const h = snabbdom.h

        // 原始数据
        const data = [
            {
                name: '张三',
                age: '20',
                address: '北京'
            },
            {
                name: '李四',
                age: '21',
                address: '上海'
            },
            {
                name: '王五',
                age: '22',
                address: '广州'
            }
        ]
        // 把表头也放在 data 中
        data.unshift({
            name: '姓名',
            age: '年龄',
            address: '地址'
        })

        const container = document.getElementById('container')

        // 渲染函数
        let vnode
        function render(data) {
            const newVnode = h('table', {}, data.map(item => {
                const tds = []
                for (let i in item) {
                    if (item.hasOwnProperty(i)) {
                        tds.push(h('td', {}, item[i] + ''))
                    }
                }
                return h('tr', {}, tds)
            }))

            if (vnode) {
                // re-render
                patch(vnode, newVnode)
            } else {
                // 初次渲染
                patch(container, newVnode)
            }

            // 存储当前的 vnode 结果
            vnode = newVnode
        }

        // 初次渲染
        render(data)


        const btnChange = document.getElementById('btn-change')
        btnChange.addEventListener('click', () => {
            data[1].age = 30
            data[2].address = '深圳'
            // re-render
            render(data)
        })

    </script>
</body>
</html>
```

总结：

用JS模拟DOM结构

新旧vnode做对比，得出最小的更新范围，最后更新DOM

数据驱动视图下，有效控制DOM操作