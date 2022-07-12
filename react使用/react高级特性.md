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

### 异步组件

import()  //vue中的异步组件使用方式

React.lazy

React.Suspense

```js
import React from 'react'

const ContextDemo = React.lazy(() => import('./ContextDemo'))

class App extends React.Component {
    constructor(props) {
        super(props)
    }
    render() {
        return <div>
            <p>引入一个动态组件</p>
            <hr />
            <React.Suspense fallback={<div>Loading...</div>}>
                <ContextDemo/>
            </React.Suspense>
        </div>

        // 1. 强制刷新，可看到 loading （看不到就限制一下 chrome 网速）
        // 2. 看 network 的 js 加载
    }
}

export default App
```

### 性能优化

#### SCU

shouldComponentUpdate

基本用法

```js
shouldComponentUpdate(nextProps,nextState){
	if(nextState.count !== this.state.count){
		return true
	}
	return false
}
```

```js
import React from 'react'
import PropTypes from 'prop-types'

class Input extends React.Component {
    constructor(props) {
        super(props)
        this.state = {
            title: ''
        }
    }
    render() {
        return <div>
            <input value={this.state.title} onChange={this.onTitleChange}/>
            <button onClick={this.onSubmit}>提交</button>
        </div>
    }
    onTitleChange = (e) => {
        this.setState({
            title: e.target.value
        })
    }
    onSubmit = () => {
        const { submitTitle } = this.props
        submitTitle(this.state.title) // 'abc'

        this.setState({
            title: ''
        })
    }
}
// props 类型检查
Input.propTypes = {
    submitTitle: PropTypes.func.isRequired
}

class List extends React.Component {
    constructor(props) {
        super(props)
    }
    render() {
        const { list } = this.props

        return <ul>{list.map((item, index) => {
            return <li key={item.id}>
                <span>{item.title}</span>
            </li>
        })}</ul>
    }
}
// props 类型检查
List.propTypes = {
    list: PropTypes.arrayOf(PropTypes.object).isRequired
}

class Footer extends React.Component {
    constructor(props) {
        super(props)
    }
    render() {
        return <p>
            {this.props.text}
            {this.props.length}
        </p>
    }
    componentDidUpdate() {
        console.log('footer did update')
    }
    shouldComponentUpdate(nextProps, nextState) {
        if (nextProps.text !== this.props.text
            || nextProps.length !== this.props.length) {
            return true // 可以渲染
        }
        return false // 不重复渲染
    }

    // React 默认：父组件有更新，子组件则无条件也更新！！！
    // 性能优化对于 React 更加重要！
    // SCU 一定要每次都用吗？—— 需要的时候才优化
}

class TodoListDemo extends React.Component {
    constructor(props) {
        super(props)
        // 状态（数据）提升
        this.state = {
            list: [
                {
                    id: 'id-1',
                    title: '标题1'
                },
                {
                    id: 'id-2',
                    title: '标题2'
                },
                {
                    id: 'id-3',
                    title: '标题3'
                }
            ],
            footerInfo: '底部文字'
        }
    }
    render() {
        return <div>
            <Input submitTitle={this.onSubmitTitle}/>
            <List list={this.state.list}/>
            <Footer text={this.state.footerInfo} length={this.state.list.length}/>
        </div>
    }
    onSubmitTitle = (title) => {
        this.setState({
            list: this.state.list.concat({
                id: `id-${Date.now()}`,
                title
            })
        })
    }
}

export default TodoListDemo

```

总结

SCU默认返回true，即React默认重新渲染所有子组件

必须配合“不可变值”一起使用

可先不用SCU，有性能问题时再考虑使用

#### PureComponent 和memo

PureComponent，SCU中实现了浅比较

memo，函数组件中PureComponent

浅比较已经适用大部分情况（尽量不要做深比较）

```js
class List extends React.PureComponent {
    constructor(props) {
        super(props)
    }
    render() {
        const { list } = this.props

        return <ul>{list.map((item, index) => {
            return <li key={item.id}>
                <span>{item.title}</span>
            </li>
        })}</ul>
    }
    shouldComponentUpdate() {/*浅比较*/}
}
```

