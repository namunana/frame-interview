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

### 非受控组件

1. ref
2. defaultValue  defaultChecked
3. 手动操作DOM元素

state只完成初始化操作，input改变值，state的值不会随之改变

```js
import React from 'react'

class App extends React.Component {
    constructor(props) {
        super(props)
        this.state = {
            name: 'namu',
            flag: true,
        }
        this.nameInputRef = React.createRef() // 创建 ref
        this.fileInputRef = React.createRef()
    }
    render() {
        // input defaultValue
        return <div>
            {/* 使用 defaultValue 而不是 value ，使用 ref */}
            <input defaultValue={this.state.name} ref={this.nameInputRef}/>
            {/* state 并不会随着改变 */}
            <span>state.name: {this.state.name}</span>
            <br/>
            <button onClick={this.alertName}>alert name</button>
        </div>

        // // checkbox defaultChecked
        // return <div>
        //     <input
        //         type="checkbox"
        //         defaultChecked={this.state.flag}
        //     />
        // </div>

        // file
        // return <div>
        //     <input type="file" ref={this.fileInputRef}/>
        //     <button onClick={this.alertFile}>alert file</button>
        // </div>

    }
    alertName = () => {
        const elem = this.nameInputRef.current // 通过 ref 获取 DOM 节点
        alert(elem.value) // 不是 this.state.name
    }
    alertFile = () => {
        const elem = this.fileInputRef.current // 通过 ref 获取 DOM 节点
        alert(elem.files[0].name)
    }
}

export default App

```

非受控组件使用场景

必须手动操作DOM元素，setState实现不了

文件上传`<input type="file"> `

某些富文本编辑器，需要传入DOM

### portals

组件默认会按照既定层次嵌套渲染

如何让组件渲染到父组件以外

```js
import React from 'react'
import ReactDOM from 'react-dom'
import './style.css'

class App extends React.Component {
    constructor(props) {
        super(props)
        this.state = {
        }
    }
    render() {
        // // 正常渲染
        // return <div className="modal">
        //     {this.props.children} {/* vue slot */}
        // </div>

        // 使用 Portals 渲染到 body 上。
        // fixed 元素要放在 body 上，有更好的浏览器兼容性。
        return ReactDOM.createPortal(
            <div className="modal">{this.props.children}</div>,
            document.body // DOM 节点
        )
    }
}

export default App

```

portals使用场景

overflow: hidden （父组件设置了bfc，子组件想脱离bfc）

父组件z-index值太小

fixed需要放在body第一层

### context

公共信息（语言，主题）如何传递给每个组件

用props太繁琐

用redux小题大作

```js
import React from 'react'

// 底层组件 - 函数是组件
function ThemeLink (props) {
    // const theme = this.context // 会报错。函数式组件没有实例，即没有 this

    // 函数式组件可以使用 Consumer
    return <ThemeContext.Consumer>
        {value => <p>link's theme is {value}</p>}
    </ThemeContext.Consumer>
}

// 底层组件 - class 组件
class ThemedButton extends React.Component {
    // 指定 contextType 读取当前的 theme context。
    // static contextType = ThemeContext // 也可以用 ThemedButton.contextType = ThemeContext
    render () {
        const theme = this.context // React 会往上找到最近的 theme Provider，然后使用它的值。
        return <div>
            <p>button's theme is {theme}</p>
        </div>
    }
}
ThemedButton.contextType = ThemeContext // 指定 contextType 读取当前的 theme context。

// 中间的组件再也不必指明往下传递 theme 了。
function Toolbar (props) {
    return (
        <div>
            <ThemedButton />
            <ThemeLink />
        </div>
    )
}

// 创建 Context 填入默认值（任何一个 js 变量）
const ThemeContext = React.createContext('light')

class App extends React.Component {
    constructor(props) {
        super(props)
        this.state = {
            theme: 'light'
        }
    }
    render () {
        return <ThemeContext.Provider value={this.state.theme}>
            <Toolbar />
            <hr />
            <button onClick={this.changeTheme}>change theme</button>
        </ThemeContext.Provider>
    }
    changeTheme = () => {
        this.setState({
            theme: this.state.theme === 'light' ? 'dark' : 'light'
        })
    }
}

export default App

```

