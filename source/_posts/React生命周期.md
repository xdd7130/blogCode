---
title: 生命周期
date: 2020-12-29 19:33:26
categories:
- React
tags: 
    - React
---

在组件的整个生命周期中，随着该组件的props或者state发生改变，其DOM表现也会有相应的变化。

一个组件就是一个状态机，对于特定地输入，它总返回一致的输出。

一个React组件的生命周期分为三个部分：实例化、存在期和销毁时。

React应用中，组件加载顺序及生命周期如下图所示：

### constructor( )
constructor是ES6对类的默认方法，通过 new 命令生成对象实例时自动调用该方法。并且，该方法是类中必须有的，如果没有显示定义，则会默认添加空的constructor( )方法。当存在constructor的时候⚠️必须手动调用super方法。 在constructor中如果要访问this.props需要传入props，示例如下：
```bash
class MyClass extends React.component{
    constructor(props){
        super(props); // 声明constructor时必须调用super方法
        console.log(this.props); // 可以正常访问this.props
    }
}

constructor 常用来初始化state

class MyClass extends React.Component {
    constructor(props){
        super(props);
        this.state = {
            list: this.props.List
        };
    }
}
```

### componentWillMount()
该方法在组件挂载之前生效。 在组件挂载之前调用且全局只调用一次。如果在这个钩子里可以setState，render后可以看到更新后的state，不会触发重复渲染。该生命周期可以发起异步请求，并setState。（React v16.3后废弃该生命周期，可以在constructor中完成设置state）

### render()
render是一个React组件必须定义的生命周期，用来渲染dom。⚠️不要在render里面修改state，会触发死循环导致栈溢出。render必须返回reactDom。
```bash
render() {
	const {nodeResultData: {res} = {}} = this.props;
	if (isEmpty(res)) return noDataInfo;
	const nodeResult = this.getNodeResult(res);
	return (
		<div className="workspace-dialog-result">
			{nodeResult}
		</div>
	);
```
### componentDidMount()
在组件挂载完成后调用，且全局只调用一次。可以在这里使用refs，获取真实dom元素。该钩子内也可以发起异步请求，并在异步请求中可以进行setState。
```bash
componentDidMount() {
	axios.get('/auth/getTemplate').then(res => {
		const {TemplateList = []} = res;
		this.setState({TemplateList});
	});
}
```
### componentWillReceiveProps (nextProps )
props发生变化以及父组件重新渲染时都会触发该生命周期，在该钩子内可以通过参数nextProps获取变化后的props参数，通过this.props访问之前的props。该生命周期内可以进行setState。(React v16.3后废弃该生命周期，可以用新的周期 static getDerivedStateFromProps 代替)

### shouldComponentUpdate(nextProps, nextState)
用于判断是否重新渲染，组件挂载之后，每次调用setState后都会调用shouldComponentUpdate判断是否需要重新渲染组件。默认返回true，需要重新render。返回false则不触发渲染。在比较复杂的应用里，有一些数据的改变并不影响界面展示，可以在这里做判断，优化渲染效率。

### componentWillUpdate(nextProps, nextState)
shouldComponentUpdate返回true或者调用forceUpdate之后，componentWillUpdate会被调用。不能在该钩子中setState，会触发重复循环。(React v16.3后废弃该生命周期，可以用新的周期 getSnapshotBeforeUpdate)

### componentDidUpdate()
完成组件渲染，除了首次render之后调用componentDidMount，其它render结束之后都是调用componentDidUpdate。该钩子内setState有可能会触发重复渲染，需要自行判断，否则会进入死循环。
```bash
componentDidUpdate() {
    if(condition) {
        this.setState({..}) // 设置state
    } else {
        // 不再设置state
    }
}
```
### componentWillUnmount()
组件被卸载的时候调用。一般在componentDidMount里面注册的事件需要在这里删除。
