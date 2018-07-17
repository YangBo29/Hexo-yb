title: react 声明周期
date: 2017-07-02
tags: react
toc: true
categories: 前端框架
---

## react 的声明周期
先上一张图，我们会根据这张图来分析

![](http://ww1.sinaimg.cn/large/e315dd2ely1fplhd8yxcgj20w60g1t9z.jpg)

> react的声明周期其实是可以分为两大类的，第一种是组件实例化时只执行一次的，第二种是组件存在时会跟着组件内部操作而触发的；那就让我们分别来看看他们在react的一个组件组件中所充当的角色

### 一、getDefaultProps

```bash
import React from 'react'; *以后的代码省略这行代码了
import PropTypes from 'prop-types';

class Component extends React.Component{
  static defaultProps = {
      name: 'yang'
    }
    render() {
      return (
        <div>Hello, {this.props.name}</div>
      )
    }
}

```
> 当组件实例化的时候会执行defaultProps这个函数，作用是设置组件所需参数的默认值、也可以配合prop-types这个包来进行参数验证[prop-types](https://reactjs.org/docs/typechecking-with-proptypes.html)

注释 ： extends 是 es6 中类继承的语法；
```javascript
 // 例如： 你想让类A 继承类B 那么写法 就是
class A extends B {
 //(类的名字最好是首字母大写，保持这种良好的代码风格)
 constructor(){
    super();
    // 在es5 一个构造函数继承另外一个构造函数的时候，会有很多方法：call继承，原型继承，组合式继承，寄生组合式继承（如果不知道的小伙变要好好学习了）
    // 而es6 中类继承则是通过super来进行的 ，super的特性不属于本篇文章的讨论范围，如果感兴趣的话可以去翻查一下
 }
}

```

### 二、getInitialState
``` bash
class Component extends React.Component{
  constructor(){
    super();
    this.state = {
      render: true
    }
  }
}
```
> 当组件实例化的时候会对实例的一些私有属性进行初始化，对es6语法了解的话，应该很容易理解，如果对es6的class类的语法不了解的话建议大家先去了解一下[es6](http://es6.ruanyifeng.com/#docs/class), state是组件内部状态，这个属性十分的重要，state的属性变更会直接影响这组件的渲染

### 三、componentWillMount

> 是组件挂载前的最后一个生命周期，只执行一次，最后修改state的机会

### 四 render

```
class Component extends React.Component{
  constructor(){
    super();
    this.state = {
      name: 'yang'
    }
  }
  render(){
    return (
      <div> `hello ${this.state.name}`</div>
    )
  }
}
```
> 组件渲染，render方法会根据return 的返回结果生成虚拟的DOM，经过react内部机制的[DOM Diff](http://www.ruanyifeng.com/blog/2012/08/how_to_read_diff.html) 比对后，渲染到页面上。
> 在render函数中有几个注意事项:
 -  必须存在一个根节点
 -  不可以修改state
 -  可以输出null，false

### 五 componentDidMount

组件挂载完成，通常我们会在这个生命周期函数内进行页面数据的请求操作， 只会执行一次

### 六 componentWillReceiveProps

```javascript
class Component extends React.Component{
  constructor(){
    super();
    this.state = {
      name: this.props.name || 'yang'
    }
  }
  componentWillReceiveProps(nextProps){
    if(typeof nextProps.name !== 'undefined'){
      this.setState({
          name: nextProps.name
      })
    }
  }
  render(){
    return (
      <div> `hello ${this.state.name}`</div>
    )
  }
}
```
> 当外部传入的参数发生改变的时候会触发componentWillReceiveProps这个函数的执行，在函数里可以修改本组件的state值，不用担心会引起render的多次调用

### 七 shouldComponentUpdate

```javascript
class Component extends React.Component{
  constructor(){
    super();
    this.state = {
      name: this.props.name || 'yang'
    }
  }
  shouldComponentUpdate(nextProps,nextState){
    return nextProps.name != this.props.name;
  }
  render(){
    return (
      <div> `hello ${this.state.name}`</div>
    )
  }
}
```
> 这个生命周期函数是在props和state发生改变时必然会触发的，如果没有手动声明，默认为true ，如果手动设置为false 组件将不会在更新；这个生命周期是开发者在react开发的时候对性能优化上帮助最大的，很多开发这都轻视了这个函数。

### 八 componentWillUpdate

> state或者props发生改变时，在组件准备更新之前执行的生命周期函数，在这个生命周期内不要在去修改state和props 不然后果很严重

### 九 componentDidUpdate

> 组件重新渲染后会被调用，在这个函数中可以获取到更新之后的数据和dom元素

### 十 componentWillUnmount

> 当用户执行移除组件或者删除组件的操作后，在执行前触发的生命周期函数，但是react对善后工作做的不是很到位，如果你在组件中创建了计时器这种异步操作事件，在componentWillUnmount函数中一定要手动的移除这些事件，否则会报错

```javascript
class Component extends React.Component{
  constructor(){
    super();
    this.state = {
      name: this.props.name || 'yang'
    }
  }
  componentDidMount(){
    this.timer = setTimeout(()=>{
        this.setState({name:'我要卸载了'})
    },5000)
  }
  componentWillUnmount(){
    clearTimeout(this.timer)
  }
  handelClick(){
    ReactDom.unmountComponentAtNode(window.root)
  }
  render(){
    return (
      <div onClick={this.handelClick}> `hello ${this.state.name}`</div>
    )
  }
}
```

对react 生命周期的以一些理解，希望对您有帮助