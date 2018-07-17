title: react 基础篇（三）
date: 2017-06-25
tags: react
toc: true
categories: 前端框架
---

## react 的基本语法

首先我们要了解react 的jsx语法

react 使用 jsx 语法来代替常规的javascript 使用起来很像 html+javascript的组合

jsx的优点 ：
          1、执行更快，因为它在编译为 JavaScript 代码后进行了优化
          2、它是类型安全的，在编译过程中就能发现错误
          3、使用 JSX 编写模板更加简单快速

注意事项 ： 1、 想要在jsx中使用js语法需要使用‘{}’ 例： { this.state.name }
          2、 在jsx 中不能使用if else 可以改用三元运算符
          3、 jsx 中的注释写法 ：{/\*注释...\*/}

---


我们先看一下这么一段代码

```javascript
// 本篇文章代码必须引入的两个组件，后面的代码中省略
import React from 'react';
import ReactDOM from 'react-dom';

let str = '20170109';
function build(str) {
    return (
        <div>
            <h1>{str}</h1>
            <h2>{true ? str : null}</h2>
        </div>
    )
}
let el = <div>{build('react的基本语法！')}</div>;
ReactDOM.render(el, document.getElementById('root'));
```

---


下面我们看一下组件的写法

```javascript
let student1 = {name: '小明', age: 15};
let student2 = {name: '李雷', age: 16};

class Achievement extends React.Component {
    render(){
        // 这个组件在调用时，默认会调用render方法
        let {name,age} = this.props; // 使用解构的方法可以让我们在写参数的时候更少
        return <p>{name} {age}</p>;
        // 也可以 return <p>{this.props.name} {this.props.age}</p>
    }
}

// 组件的传参的两种方法，可以使用es6的解构方式，也可以一个个传
ReactDOM.render(<div>
    <Achievement {...student1}/>
    <Achievement name={student2.name} age={student2.age}/>
</div>, window.root);

// 注意 ： <Achievement age=9/> 这样传递参数的时候，在组件内部调用this.props.age 的时候是字符串类型而不是数字类型

---


```

列表类的输出方式

```javascript

let students = [
    {name: '李雷', sex: '男'},
    {name: '李华', sex: '男'},
    {name: '小红', sex: '女'}
];

ReactDOM.render(
(
    <ul>
        {lessons.map((item, index) => (
                <li key={index}>姓名：{item.name};性别：{item.sex}</li>
            )
        )}
    </ul>
), document.getElementById('root'));
```

一定要注意的是 如果你有代码的格式洁癖，请用() 将代码包裹起来，防止react不能正常解析这段代码，在react中循环输出的时候一定要在输出的元素上添加 key 属性，否则会出现警报，虽然这个警报不会影响你代码的执行，但是会影响性能，因为在react 中保证性能的 DOM Diff 会使用到这个属性 ,不是本篇文章的讨论范围

---


属性上的渲染

```javascript
let style = {
    backgroundColor : 'red',
    color : 'blue',
    fontSize : '20px'
};
ReactDOM.render(
(
    <ul style={style}>
        <li className='aa'>1</li>
        <li><label htmlFor='aa'>-</label><input id='aa'/></li>
        <li dangerouslySetInnerHTML={{__html:'<span>123</span>'}}></li>
    </ul>
)
,window.root);
// 这里需要注意的是 这里的style是对象数据类型
```

要了解是react中dom的属性有一些已经变成react中的关键字，所以写法上会和以前的写法不同，例如：class -> className; for -> htmlFor ...

如果你必不可免的要在一个结构中插入许多标签，可以使用dangerouslySetInnerHTML（安全插入），因为有可能不合时宜的使用innerHTML会导致XSS攻击；

---

事件上的绑定

```javascript
// 我们以input 来举例， 顺便也说以下react 实现双向数据绑定的方法

// 写法一
class Input extends React.Component{
    constructor(){
        super();
        this.state = {val:''}
    }
    handleFn(e){
        this.setState({val:e.target.value});
    }
    render(){
        return (
            <div>
                <input type="text" value={this.state.val} onChange={this.handleFn.bind(this)}/>
                {this.state.val}
            </div>
        )
    }
}

// 写法二
class Input extends React.Component{
    constructor(){
        super();
        this.state = {val:''}
    }
    handleFn = (e)=>{
        this.setState({val:e.target.value});
    }
    render(){
        return (
            <div>
                <input type="text" value={this.state.val} onChange={this.handleFn}/>
                {this.state.val}
            </div>
        )
    }
}

ReactDOM.render(<Input/>,window.root);
```

上面有两种写法，当然第二种使用了es6和es7的语法所以写起来很简洁，也不用担心this错误的问题，所以在babel可以解析的语法，我们都可以在项目中使用

我们简单说一下 react 的双向数据绑定，大家都知道react 是 MVC框架（单向数据绑定），所以我们想实现双向数据绑定就要借助其他的方法，react 能引起视图渲染的方法只有修改state和props，所以我们想要是想双向数据绑定，我们就要去操作state和props，那我们实现一下

```javascript

class Parent extends React.Component {
  constructor() {
    super();
    this.state = {val: ''}
  }
  changeFn = (e) => {
    this.setState({val: e.target.value});
  };
  render() {
    return (
      <Input value={this.state.val} changeFn={this.changeFn}/>
    )
  }
}

class Input extends React.Component {
  constructor() {
    super();
    this.state = {val: ''}
  }
  handleFn = (e) => {
    this.setState({val: e.target.value});
  };
  render() {
    let {value,changeFn} = this.props;
    return (
      <div>
        <input type="text" value={this.state.val} onChange={this.handleFn}/>
        {this.state.val}
        <input type="text" value={value} onChange={changeFn}/>
        {value}
      </div>
    )
  }
}

ReactDOM.render(<Parent/>,window.root)

```

需要用事件来触发state或者props的修改来让视图重新渲染，来实现双向绑定

还有一种不提倡的方法，就是直接通过ref 来操作DOM也可以实现，不过既然使用react了，还在操作DOM 那就太low了。所以我们要习惯这种数据驱动视图的思想。




