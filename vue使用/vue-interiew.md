### 

#### v-show和v-if的区别

1. v-show 通过css display 控制显示隐藏
2. v-if 组件真正的渲染和销毁，而不是显示和隐藏
3. 频繁切换显示状态用v-show，否则用v-if

#### 为何在v-for中用key

必须用key，且不能是index和random

diff算法中通过tag和key来判断，是否是sameNode

减少渲染次数，提升渲染性能

#### 描述vue组件生命周期

单组件生命周期图

父子组件生命周期关系

#### vue组件如何通讯

父子组件props 和$.emit

自定义事件event，$on  $off  $emit

vuex

#### 描述组件渲染和更新过程

![xrgx](..\static\img\xrgx.png)

#### 双向数据绑定v-model实现原理

input元素value= this.name

绑定input事件this.name=$event.target.value

data更新触发re-render

#### 对MVVM的理解

![mvvm](..\static\img\mvvm.png)

#### computed有和特点

缓存，data不变不会重新计算

提高性能

#### 为何组件data必须是一个函数

因为vue文件编译出来是一个class，使用这个文件就是实例化，而使用data里的数据是在闭包之中，各个实例不会相互影响

#### ajax请求应该放在哪个生命周期

mounted

js是单线程的，ajax异步获取数据

放在mounted之前没有用，只会让逻辑更加混乱

#### 如何将组件所有props传给子组件

$props

`<User v-bind="$props" />`

#### 多个组件有相同逻辑如何抽离

mixin

以及mixin的一些缺点

#### 何时要使用异步组件

加载大组件

路由异步加载

#### 何时需要使用keep-alive

缓存组件，不需要重复渲染

如多个静态tab页切换

优化性能

#### 何时需要使用beforeDestory

解绑自定义事件event.$off

清除定时器

解绑自定义DOM事件，如window scroll等

#### 什么是作用域插槽

#### vuex中action和mutation有何区别

action中处理异步，mutation不可以

mutation做原子操作

action可以整合多个mutation

#### vue-router常用的路由模式

hash默认

H5 history（需要服务端支持）

两者比较

#### 如何配置vue-router异步加载

```
export default new VueRouter({
	routes: [
		{
			path: '/',
			component: () => import(
				'./../components/Navigator'
			)
		},
		{
			path: '/feedback',
			component: () => import(
				'./../components/FeedBack'
			)
		}
	]
})
```

#### 用vnode描述一个DOM结构

#### 监听data变化的核心API是什么

Object.defineProperty

以及深度监听，监听数组

有何缺点

#### vue如何监听数组变化

Object.defineProperty不能监听数组变化

重新定义原型，重写push pop等方法，实现监听

Proxy可以原生支持监听数组变化

#### 描述响应式原理

监听data变化

组件渲染和更新流程

#### diff算法的时间复杂度

O（n）

在O（n^3）基础上做了一些调整

#### 简述diff算法的过程

patch（elem,vnode）和patch(vnode,newVnode)

patchVnode 和addVnodes和removeVnodes

updateChildren(key的重要性)

#### vue为何是异步渲染，$nextTick有何用

异步渲染（以及合并data修改），以提高渲染性能

$nextTick在DOM更新完之后，触发回调

#### vue常见性能优化方式

合理使用v-show和v-if

合理使用computed，缓存

v-for加key，以及避免和v-if同时使用

自定义事件和DOM事件及时销毁

合理使用异步组件

合理使用keep-alive

data层级不要太深

使用vue-loader在开发环境做模板编译（预编译）

webpack层面的优化

前端通通用性能优化，如图片懒加载

使用SSR