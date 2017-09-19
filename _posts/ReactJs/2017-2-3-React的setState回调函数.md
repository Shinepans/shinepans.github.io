---
layout: post
title: React的setState回调函数
categories: ReactJs
---

## 关于React的setState的回调函数

> 一般而言，在设置页面某些state的时候，需要先设置好state，然后再对页面的一些参数进行修改的时候，可以使用setState的回调函数。

###　分析一下区别

>　不在回调中使用参数，我们在设置state后立即使用state：

```
this.state = {foo: 1};
this.setState({foo: 123});
console.log(this.state.foo);
// 1
```

> 在回调中调用设置好的state

```
this.state = {foo: 2};
this.setState({foo: 123}, ()=> {
    console.log(foo);
    // 123
});
```

> 关于setState的回调函数的作用大概如此，这个函数相当于componentDidUpdate函数，和生命周期的函数类似。