---
title: Hooks
date: 2021-01-23 15:25:06
categories:
- React
tags: 
    - React
---

hooks常用api有：`useState`、`useEffect`、`useContext`、`useReducer`、`useRef`,`useMemo`,`useCallback`,等。

React Hook是React 16.8版本之后添加的新属性，用最简单的话来说，React Hook就是一些React提供的内置函数，这些函数可以让Function Component和Class Component一样能够拥有组件状态（state）以及进行副作用（side effect）。

### 🌴useState
#### 作用
useState和Class Component的this.state一样，用来管理组件状态。在React Hook没出来之前，Function Component也叫做Functional Stateless Component（FSC），这是因为Function Component每次执行的时候都会生成新的函数作用域所以同一个组件的不同渲染（render）之间是不能够共用状态的，因此开发者一旦需要在组件中引入状态就需要将原来的Function Component改成Class Component，这使得开发者的体验十分不好。useState就是用来解决这个问题的，它允许Function Component将自己的状态持久化到React运行时（runtime）的某个地方（memory cell），这样在组件每次重新渲染的时候都可以从这个地方拿到该状态，而且当该状态被更新的时候，组件也会重渲染。
#### 用法
```
const [state, setState] = useState(initialState)
```
useState接收一个initialState变量作为状态的初始值，返回值是一个数组。返回数组的第一个元素代表当前state的最新值，第二个元素是一个用来更新state的函数。这里要注意的是state和setState这两个变量的命名不是固定的，应该根据你业务的实际情况选择不同的名字，可以是text和setText，也可以是width和setWidth这类的命名。

我们在实际开发中，一个组件可能不止一个state，如果组件有多个state，则可以在组件内部多次调用useState，以下是一个简单的例子：
```
import React, { useState } from 'react'
import ReactDOM from 'react-dom'

const App = () => {
  const [counter, setCounter] = useState(0)
  const [text, setText] = useState('')

  const handleTextChange = (event) => {
    setText(event.target.value)
  }

  return (
    <>
      <div>Current counter: {counter}</div>
      <button
        onClick={() => setCounter(counter + 1)}
      >
        Increase counter
      </button>
      <input
        onChange={handleTextChange}
        value={text}
      />
    </>
  )
}

ReactDOM.render(<App />, document.getElementById('root'))

```
和Class Component的this.setState API类似，setCounter和setText都可以接收一个函数为参数，这个函数叫做updater，updater接收的参数是当前状态的最新值，返回值是下一个状态。例如setCounter的参数可以改成一个函数：

```
<button
  onClick={() => {
    setCounter(counter => counter + 1)
  }}
>
  Increase counter
</button>
```
useState的initialState也可以是一个用来生成状态初始值的函数，这种做法主要是避免组件每次渲染的时候initialState需要被重复计算。下面是个简单的例子：

```const [state, setState] = useState(() => {
  const initialState = someExpensiveComputation(props)
  return initialState
})```

#### 注意事项:

- setState是全量替代

Function Component的setState和Class Component的this.setState函数的一个重要区别是this.setState函数是将当前设置的state浅归并（shallowly merge）到旧state的操作。而setState函数则是将新state直接替换旧的state（replace）。因此我们在编写Function Component的时候，就要合理划分state，避免将没有关系的状态放在一起管理，

- 设置相同的state值时setState会bailing out of update

如果setState接收到的新的state和当前的state是一样的（判断方法是`Object.is`），React将不会重新渲染子组件或者触发side effect。这里要注意的是虽然React不会渲染子组件，不过它还是会重新渲染当前的组件的，如果你的组件渲染有些很耗性能的计算的话，可以考虑使用`useMemo`来优化性能

- setState没有回调函数

无论是useState还是Class Component的this.setState都是异步调用的，也就是说每次组件调用完它们之后都不能拿到最新的state值。为了解决这个问题，Class Component的this.setState允许你通过一个回调函数来获取到最新的state值，用法如下：
```bash
this.setState(newState, state => {
  console.log("I get new state", state)
})
```

### 🌴useEffect
#### 作用

useEffect是用来使Function Component也可以进行副作用的，何为副作用，函数的副作用就是函数除了返回值外对外界环境造成的其它影响。
在React的世界里，我们的副作用大体可以分为两类，一类是调用浏览器的API，例如使用addEventListener来添加事件监听函数等，另外一类是发起获取服务器数据的请求。Function Component中使用useEffect来定义副作用了。虽然useEffect基本可以覆盖``componentDidMount``， ``componentDidUpdate``，``componentWillUnmount``等生命周期函数组合起来使用的所有场景，但是useEffect和生命周期函数的设计理念还是存在本质上的区别的

#### 用法
```useEffect(effect, dependencies?)```

useEffect的第一个参数effect是要执行的副作用函数，它可以是任意的用户自定义函数，用户可以在这个函数里面操作一些浏览器的API或者和外部环境进行交互，这个函数会在每次组件渲染完成之后被调用，例如下面是一个简单的例子：
```bash
import React, { useState, useEffect } from 'react'
import ReactDOM from 'react-dom'

const UserDetail = ({ userId }) => {
  const [userDetail, setUserDetail] = useState({})

  useEffect(() => {
    fetch(`https://myapi/users/${userId}`)
      .then(response => response.json())
      .then(user => setUserDetail(userDetail))
  })

  return (
    <div>
      <div>User Name: {userDetail.name}</div>
    </div>
  )
}

ReactDOM.render(<UserDetail />, document.getElementById('root'))

```
上面定义的获取用户详情信息的副作用会在UserDetail组件每次完成渲染后执行，所以当该组件第一次挂载的时候就会向服务器发起获取用户详情信息的请求然后更新userDetail的值，这里的第一次挂载我们可以类比成Class Component的componentDidMount。可是如果试着运行一下上面的代码的话，你会发现代码进入了死循环：组件会不断向服务端发起请求。出现这个死循环的原因是useEffect里面调用了setUserDetail，这个函数会更新userDetail的值，从而使组件重渲染，而重渲染后useEffect的effect继续被执行，进而组件再次重渲染。。。为了避免重复的副作用执行，useEffect允许我们通过第二个参数dependencies来限制该副作用什么时候被执行：指明了dependencies的副作用，只有在dependencies数组里面的元素的值发生变化时才会被执行，因此如果要避免上面的代码进入死循环我们就要将userId指定为我们定义的副作用的dependencies：
```bash
import React, { useState, useEffect } from 'react'
import ReactDOM from 'react-dom'

const UserDetail = ({ userId }) => {
  const [userDetail, setUserDetail] = useState({})

  useEffect(() => {
    fetch(`https://myapi/users/${userId}`)
      .then(response => response.json())
      .then(user => setUserDetail(userDetail))
  }, [userId])

  return (
    <div>
      <div>User Name: ${userDetail.name}</div>
    </div>
  )
}

ReactDOM.render(<UserDetail />, document.getElementById('root'))

```

除了发起服务端的请求外，我们往往还需要在useEffect里面调用浏览器的API，例如使用addEventListener来添加浏览器事件的监听函数等。我们一旦使用了addEventListener就必须在合适的时候调用removeEventListener来移除对事件的监听，否则会有性能问题，useEffect允许我们在副作用函数里面返回一个cleanup函数，这个函数会在组件重新渲染之前被执行，我们可以在这个返回的函数里面移除对事件的监听，下面是一个具体的例子：

```bash
import React, { useEffect } from 'react'
import ReactDOM from 'react-dom'

const WindowScrollListener = () => {
  useEffect(() => {
    const handleWindowScroll = () => console.log('yean, window is scrolling!')
    window.addEventListener('scroll', handleWindowScroll)

    // this is clean up function
    return () => {
      window.removeEventListener(handleWindowScroll)
    }
  }, [])

  return (
    <div>
      I can listen to the window scroll event!
    </div>
  )
}

ReactDOM.render(<WindowScrollListener />, document.getElementById('root'))

```
上面的代码中我们会在WindowScrollListener组件首次渲染完成后注册一个监听页面滚动事件的函数，并在组件下一次渲染前移除该监听函数。由于我们指定了一个空数组作为这个副作用的dependencies，所以这个副作用只会在组件首次渲染时被执行一次，而它的cleanup函数只会在组件unmount时才被执行，这就避免了频繁注册页面监听函数从而影响页面的性能。

#### 注意事项

- 避免使用“旧的”变量
