title: react 高阶组件
date: 2017-09-17
tags: react
toc: true
categories: 前端框架
---

### 为什么要使用高阶组件

> 我们先看一个demo

```
export default class Username extends React.Component {
  constructor(){
    super();
    this.state = {val:''}
  }
  componentDidMount(){
    let username = localStorage.getItem('username')||'';
    this.setState({
      val:username
    })
  }
  render(){
    return <div>
      <input type="text" value={this.state.val} onChange={()=>{}}/>
    </div>
  }
}

```
> 在本地缓存中获取用户的姓名，并且将数据作为输入框的默认值，很简单的一个逻辑，不过中情况可能我们还会在密码输入框中用到； 也就是说这一段获取本地缓存数据的操作可能是一段公共逻辑，当然我们可以在每一个组件中写这样的一段逻辑，但是这就失去了组件复用性，这个时候我们就可以使用高阶组件来完成这样的操作（有没有瞬间想到node里面的中间件用法）；

```
// 我们来实现一下这个功能

// local.js
import React from 'react';
let local = (key) => (Component) => {
   return class extends React.Component {
      constructor(){
        super();
        this.state = { val : ''};
      }
      componentDidMount(){
        let temp = localStorage.getItem(key) || '';
        this.getState({
          val : temp
        })
      }
      render(){
        return <Component {...this.state} />
      }
   }
}

export default local;

// index.js
import Local from './local';

class Username extends React.Component {
  render(){
    return <div>
      <input type="text" value={this.props.val} onChange={()=>{}}/>
    </div>
  }
}

export default Local('username')(Username);
```

> 将公共的逻辑拿到外层组件中处理，将处理完成的数据在传入到原本组件中，所以高阶组件也就是一个React组件接收一个组件，然后返回另外一个组件

### context的用法

> react 是单向数据流， 我们想传递数据需要一层层向下传递，数据传递变得非常的麻烦，我们可以用context实现数据的交互
  - 父级组件 childContextTypes getChildContext函数
  - 子级组件 contextTypes


#### 跨组建数据交互

```
// 组件顺序 App -> Header -> Title

// App.js
import React from 'react';
import PropTypes from 'prop-types'
import Header from "./Header";

export default class App extends React.Component {
  constructor(){
    super();
    this.state = {color:'red'}
  }
  static childContextTypes = {
    //定义子组件上下文的类型
    color:PropTypes.string,
    setColor:PropTypes.func
  };
  setColor = (color) =>{
    this.setState({
      color
    })
  };
  getChildContext(){
    // 定义子组件上下文的数据
    return {
      color:this.state.color,
      setColor:this.setColor
    }
  }
  render(){
    return (
      <div>
        <Header/>
      </div>
    )
  }
}

// Header.js
export default class Header extends React.Component {
  static contextTypes = {
     setColor:PropTypes.func
  };
  render(){
    return <div>
      <button onClick={()=>{
        this.context.setColor('green');
      }}>变绿</button>
      <Title/>
    </div>
  }
}

// Title.js
export default class Title extends React.Component {
  static contextTypes = {
    color:PropTypes.string
  };
  render(){ // 通过context获取父组件定义的数据
    return <div style={{color:this.context.color}}>Title</div>
  }
}
```

----
#### 实现 react-redux 库

```
import React from 'react';
import PropTypes from 'prop-types';

class Provider extends React.Component{
  static childContextTypes = {
    store:PropTypes.object
  };
  getChildContext(){
    return {
      store:this.props.store
    }
  }
  constructor(){
    super();
  }
  render(){
    return this.props.children;
  }
}

let connect = (mapStateToProps,mapDispatchToProps) => (Component) =>{
  //如果第二个参数传递一个actions(一个对象)；做如下处理（与bindActionCreators处理方式相同）
  if({}.toString.call(mapDispatchToProps).slice(8,-1)==='Object'){
    let actions=mapDispatchToProps;
    mapDispatchToProps=(dispatch)=>{
      let obj={};
      for (let key in actions) {
        if (actions.hasOwnProperty(key)) {
          obj[key]=(...args)=>{
            dispatch(actions[key](...args));
          }
        }
      }
      return obj;
    }
  }

  return class Proxy extends React.Component{
    static contextTypes = {
      store:PropTypes.object
    };
    constructor(props,context){
      super();
      this.state = mapStateToProps(context.store.getState());
    }
    componentDidMount(){
      this.unsubscribe = this.context.store.subscribe(()=>{
        this.setState(mapStateToProps(this.context.store.getState()))
      });
    }
    componentWillUnmount(){
      this.unsubscribe();
    }
    render(){
      return <Component {...this.state} {...mapDispatchToProps(this.context.store.dispatch)}/>
    }
  }
};
export {Provider,connect}

```

#### bindActionCreators 方法

```
let bindActionCreators = (actionCreators,dispatch) => {
  let obj = {}
  for(let key in actionCreators){
    obj[key] = (...args)=>{
      dispatch(actionCreators[key](...args))
    }
  }
  return obj
};

export default connect(state=>({...state}),dispatch=>bindActionCreators(actionCreators,dispatch))(Counter)
```

**顺手献上源码**

```
function bindActionCreator(actionCreator, dispatch) {
  return function () {
    return dispatch(actionCreator.apply(undefined, arguments));
  };
}

export default function bindActionCreators(actionCreators, dispatch) {
  if (typeof actionCreators === 'function') {
    return bindActionCreator(actionCreators, dispatch);
  }

  if (typeof actionCreators !== 'object' || actionCreators === null) {
    throw new Error('');
  }

  var keys = Object.keys(actionCreators);
  var boundActionCreators = {};
  for (var i = 0; i < keys.length; i++) {
    var key = keys[i];
    var actionCreator = actionCreators[key];
    if (typeof actionCreator === 'function') {
      boundActionCreators[key] = bindActionCreator(actionCreator, dispatch);
    }
  }
  return boundActionCreators;
}
```


