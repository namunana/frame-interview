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

```
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