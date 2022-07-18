## React 原理

1. 函数式编程
2. vdom和diff算法
3. jsx的本质
4. 合成事件
5. setState  batchUpdate
6. 组件渲染过程

### 函数式编程

纯函数

不可变值

```js
this.setState({
	list: this.state.list.concat({
		id:`id-${Date.now()}`,
		title
	})
})

//不能用push、pop、splice
```

### vdom和diff算法

#### vnode

h函数

vnode数据结构

patch函数

```json
{
	tag:'div',
	prop:{
		className:'home'
		id:'home'
	},
	children:{
		{
			tag:'div',
			prop:{
				className:'namu'
			},
			children:{
				...
			}
		}
	}
}
```



#### diff算法

只比较同一级，不跨级比较

tag不相同，则直接删掉重建，不再深度比较

tag和key，两者都相同，则认为是相同节点，不在深度比较