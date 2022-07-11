## react高级特性

1. 函数组件
2. 非受控组件
3. portals
4. context
5. 异步组件
6. 性能优化
7. 高阶组件
8. Render Props

### 函数组件

class组件和函数组件对比

纯函数，输入props，输出JSX

没有实例，没有生命周期，没有state

不能扩展其他方法

```js
import React from "react"

class List extends React.Component {
  constructor(props) {
    super(props)
  }

  render () {
    const { list } = this.props
    return (
      <ul>
        {list.map((item, index) => {
          return <li key={item.id}>{{ item }}</li>
        })}
      </ul>
    )
  }
}

function List1 (props) {
  const { list } = this.props

  return (
    <ul>
      {list.map((item, index) => {
        return <li key={item.id}>{{ item }}</li>
      })}
    </ul>
  )
}

export default List
```

