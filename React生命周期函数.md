# React生命周期函数

《React与Redux开发实例精解》

>componentWillMount：在渲染前调用
>componentDidMount：在第一次渲染后调用
>componentWillReceiveProps：在组件接收到一个新的prop时被调用。这个方法在第一次渲染时不会被调用。
>shouldComponentUpdate：返回一个布尔值。在组件接收到新的props或者state时被调用。在初始化时或者使用forceUpdate时不被调用。可以在你确认不需要更新组件时使用。
>componentWillUpdate：在组件还没有接收到新的props或者state但还没有render时被调用。在初始化时不会被调用。
>componentDidUpdate：在组价完成更新后立即调用。在初始化时不会被调用。
>componentWillUnmount：在组件从DOM中移除的时候立刻被调用。

[React官方文档](https://facebook.github.io/react/docs/react-component.html)

组件实例初始化、更新、销毁的过程：

Mounting

> These methods are called when an instance of a component is being created and inserted into the DOM:
```
constructor()
componentWillMount()
render()
componentDidMount()
```

Updating
> An update can be caused by changes to props or state. These methods are called when a component is being re-rendered:

```
componentWillReceiveProps()
shouldComponentUpdate()
componentWillUpdate()
render()
componentDidUpdate()
```

Unmounting
> This method is called when a component is being removed from the DOM:
```
componentWillUnmount()
```








