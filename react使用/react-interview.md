## react-interview

1. **组件如何通讯**

   父子组件的props

   自定义事件

   Redux和Context

2. **JSX本质是什么**

   createElement

   执行返回vnode

3. **Context是什么，如何应用？**

   父组件，向其下所有子孙组件传递信息

   如一些简单的公共信息：主题色、语言等

   复杂逻辑用Redux

4. **shouldComponentUpdate用途**

   性能优化

   配合不可变值一起使用，否则会出错

5. **setState场景题**

   ```js
   componentDidMount(){
   	this.setState({count: this.setState.count + 1})
   	console.log('1',this.setState.count) //0
   	this.setState({count: this.setState.count + 1})
   	console.log('2',this.setState.count) //0
   	setTimeout(() => {
   		this.setState({count: this.setState.count + 1})
   		console.log('3',this.setState.count) //2
   	})
   	setTimeout(() => {
   		this.setState({count: this.setState.count + 1})
   		console.log('4',this.setState.count) //3
   	})
   }
   ```

   

   6. **什么是纯函数**

   返回一个新值，没有副作用（不会 “偷偷” 修改其他值）、

   重点：不可变值

   如arr1  =  arr.slice() ，输入的值类型和返回的值类型要一致

7. **react生命周期**

   单组件生命周期

   父子组件生命周期

   注意SCU

8. **react发起ajax应该放再那个生命周期**

   同vue

   componentDidMount

9. **列表渲染，为何要用key**

   同Vue。必须用key，且不能是index和random

   diff算法中通过tag和key来判断，是否是sameNode

   减少渲染次数，提高渲染性能

10. **函数组件和class组件的区别**

    纯函数，输入props，输出jsx

    没有实例，没有生命周期，没有state

    不能扩展其他方法

11. **什么是受控组件**

    表单的值，受state控制

    需要自行监听onChange事件，更新state

    对比非受控组件

12. **何时使用异步组件**

    同Vue

    加载大组件

    路由懒加载

13. **多个组件有公共逻辑，如何抽离**

    高阶组件HOC

    Render Props

    mixin已经被React废弃

14. **redux如何实现异步请求**

    使用action

    如redux-thunk

15. **react-router如何配置懒加载**

```js
import { BrowserTouter as Router, Route, Switch} from 'react-router-dom'
import React { Suspense,lazy } from 'react'

const Home = React.lazy(() => import('./routes/Home'))
const About = React.lazy(() => import('./routes/About'))

const App = (){
    <Router>
        <Suspense fallback{<div>Loading...</div>}>
        	<Switch>
            	<Route exact path="/" compoent={Home} />
                <Route exact path="/about" compoent={About} />
        	</Switch>
        </Suspense>
    </Router>
}

export default App
```

16. **PureCompenet和SCU有何区别**

    实现了浅比较的SCU

    性能优化

    但要结合不可变值

17. **react事件和DOM事件的区别**

    所有事件挂载到DOM上

    event不是原生的，是SyntheticEvent合成事件对象

    有dispatchEvent机制

18. **React性能优化**

    渲染列表要加key

    自定义事件、DOM事件要及时销毁

    合理使用异步组件

    减少函数bind this的次数

    合理使用SCU PureComponent 和 memo

    合理使用Immutable.js

    webpack层面的优化

    前端通用性能优化，如图片懒加载

    使用SSR

19. **React和Vue的区别**

    都支持组件化

    都是数据驱动视图

    都使用vdom操作DOM

    React使用JSX拥抱JS，Vue使用模板拥抱html

    React函数式编程，Vue声明式编程

    React更多需要自力更生，Vue把想要的都给你