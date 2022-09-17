---
title: Redux
date: 2021-01-17 22:25:42
categories:
- React
tags: 
    - React
---
### 🌴 Redux
### 🌴 React-Redux
[demo代码](https://github.com/xdd7130/REACT/tree/main/react-04-router)
#### UI组件
只负责 UI 的呈现，不带有任何业务逻辑
没有状态（即不使用this.state这个变量）
所有数据都由参数（this.props）提供
不使用任何 Redux 的 API
#### 容器组件
负责管理数据和业务逻辑，不负责 UI 的呈现
带有内部状态
使用 Redux 的 API
UI 组件负责 UI 的呈现，容器组件负责管理数据和逻辑。

如果一个组件既有 UI 又有业务逻辑，那怎么办？回答是，将它拆分成下面的结构：外面是一个容器组件，里面包了一个UI 组件。前者负责与外部的通信，将数据传给后者，由后者渲染出视图。

React-Redux 规定，所有的 UI 组件都由用户提供，容器组件则是由 React-Redux 自动生成。也就是说，用户负责视觉层，状态管理则是全部交给它。

#### connect()
```bash
import { connect } from 'react-redux'
const VisibleTodoList = connect()(TodoList);
```
上面VisibleTodoList 便是通过UI组件TodoList,通过connect方法自动生成的容器组件。
但需要定义业务逻辑，组件才有意义。
```bash
import { connect } from 'react-redux'

const VisibleTodoList = connect(
  mapStateToProps,
  mapDispatchToProps
)(TodoList)
```
connect方法接受两个参数：mapStateToProps和mapDispatchToProps。它们定义了 UI 组件的业务逻辑。前者负责输入逻辑，即将state映射到 UI 组件的参数（props），后者负责输出逻辑，即将用户对 UI 组件的操作映射成 Action。

#### mapStateToProps()
它是一个函数，建立一个从（外部的）state对象到（UI 组件的）props对象的映射关系。
mapStateToProps执行后应该返回一个对象，里面的每一个键值对就是一个映射。
```bash
const mapStateToProps = (state) => {
  return {
    todos: getVisibleTodos(state.todos, state.visibilityFilter)
  }
}
```
mapStateToProps是一个函数，它接受state作为参数，返回一个对象。这个对象有一个todos属性，代表 UI 组件的同名参数，后面的getVisibleTodos也是一个函数，可以从state算出 todos 的值。

#### mapDispatchToProps()
mapDispatchToProps是connect函数的第二个参数，用来建立 UI 组件的参数到store.dispatch方法的映射。它定义了哪些用户的操作应该当作 Action，传给 Store。它可以是一个函数，也可以是一个对象。

是函数则会得到dispatch和ownProps（容器组件的props对象）两个参数。
```basj
const mapDispatchToProps = (
  dispatch,
  ownProps
) => {
  return {
    onClick: () => {
      dispatch({
        type: 'SET_VISIBILITY_FILTER',
        filter: ownProps.filter
      });
    }
  };
}
```
从上面代码可以看到，mapDispatchToProps作为函数，应该返回一个对象，该对象的每个键值对都是一个映射，定义了 UI 组件的参数怎样发出 Action。

是一个对象，它的每个键名也是对应 UI 组件的同名参数，键值应该是一个函数，会被当作 Action creator ，返回的 Action 会由 Redux 自动发出。
```bash
const mapDispatchToProps = {
  onClick: (filter) => {
    type: 'SET_VISIBILITY_FILTER',
    filter: filter
  };
}
```
#### Provider 组件
connect方法生成容器组件以后，需要让容器组件拿到state对象，才能生成 UI 组件的参数。
React-Redux 提供Provider组件，可以让容器组件拿到state。
```bash
import { Provider } from 'react-redux'
import { createStore } from 'redux'
import todoApp from './reducers'
import App from './components/App'

let store = createStore(todoApp);

render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
)
```