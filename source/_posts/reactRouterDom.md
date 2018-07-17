title: react-router-dom
date: 2017-08-12
tags: react
toc: true
categories: 前端框架
---

** 本文中使用的参数举例为官方文档案例

** 本篇文章只将组件和方法的参数展示，针对使用方法和常用的参数进行阐述，如果想了解的更多，可以去查看[React-Router 4.x官方文档](https://reacttraining.com/react-router/web/api/BrowserRouter/basename-string)

在react中想使用路由功能，基本是可选的插件只有react-router，我们想使用路由功能首先要安装
```
$ npm install react-router-dom --save-dev;
```

安装成功之后我们就可以在我们的项目中使用了
```
import React from 'react';
import ReactDom from 'react-dom';
import {BrowserRouter,HashRouter,Link,NavLink,Redirect,Route,Switch} from 'react-router-dom';
// react-router-dom中提供的组件供我们使用
```

## react-router 提供的组件

### BrowserRouter

```
<BrowserRouter
  basename={optionalString}
  forceRefresh={optionalBool}
  getUserConfirmation={optionalFunc}
  keyLength={optionalNumber}
>
  <App/>
</BrowserRouter>
```

[注意]：需要一个根节点包裹

```
import Address from '../component/Address';

<BrowserRouter basename='/base'>
  <div>
    <Route path='/address' component={Address}/>
  </div>
</BrowserRouter>
<Link to='/address'> // render ： <a href='/base/address'>
```

----
### HashRouter

[注意]：需要一个根节点包裹
```
import Address from '../component/Address';

<HashRouter basename='/base'>
  <App>
    <Route path='/address' exact component={Address}/>
  <App/>
</HashRouter>

<Link to="/address"/> // renders <a href="#/base/address">
```

----
### Link

```
<Link to="/about">About</Link> // renders <a href="#/about">
```

__exact__ : bool
如果为true，路径为精准匹配

__to__ : string
连接到指定的路径

__to__ : object
要连接的位置

```
<Link to={{
  pathname: '/address', // 跳转路径
  search: '?sort=name',
  hash: '#the-hash',
  state: { fromDashboard: true }
}}/>
// 对面里面可以添加跳转携带的参数
```

__replace__ : bool
如果为true，单击链接将替换历史堆栈中的当前条目，而不是添加新条目。

----
### NavLink
一种特殊版本的<Link>，用法和Link一致，不过在与当前URL匹配时，将向渲染元素添加样式属性。

```
<NavLink
  to="/faq" // 跳转路径
  activeClassName="selected" // 当于路径匹配成功时，追加的类名
>FAQs</NavLink>

// 如果我们不适用样式表，也可以写成行内样式，如下
<NavLink
  to="/faq"
  activeStyle={{ // 对象数据类型 写法如js中设置属性时的写法
    fontWeight: 'bold',
    color: 'red'
  }}
>FAQs</NavLink>
```

----
### Redirect (配合Switch使用)
路径重定向，当匹配不到符合路径是，会跳转到Redirect设置的路径

__to__ : string
重定向路径

__to__ : object
要重定向位置

```
<Redirect to={{
  pathname: '/login',
  search: '?utm=your+face',
  state: { referrer: currentLocation }
}}/>
```

__push__: bool
当为true时，重定向会将新条目推入历史记录，而不是替换当前条目。

__from__: string
要重定向的路径名。这只能用于在<Switch>内部呈现<Redirect>时匹配位置。

```
<Switch>
  <Redirect from='/old-path' to='/new-path'/>
  <Route path='/new-path' component={Address}/>
</Switch>
```

----
### Route
路由组件可能是React Router中了解和学习使用的最重要的组件。其最基本的责任是在位置与路线的路径匹配时呈现一些UI。

```
<BrowserRouter>
  <div>
    <Route exact path="/" component={Header}/>
    <Route path="/news" component={Footer}/>
  </div>
</BrowserRouter>
```

__component__
当您使用组件（而不是下面的渲染或子项）时，路由器使用React.createElement从给定组件创建一个新的React元素。
这意味着如果您向组件属性提供内联函数，则可以在每个渲染中创建一个新组件.这将导致现有组件卸载和新组件安装，而不是仅更新现有组件

__render__ ： function
这允许方便的在线呈现和包装，而不需要上述的不期望的重新安装。您可以使用组件支持为您创建一个新的React元素，而不必在位置匹配时传入要调用的函数。
渲染道具接收与组件渲染道具相同的所有route props。

警告：&lt;Route component&gt;取决于&lt;Route render&gt;，所以不要在同一个&lt;Route&gt;中使用两者

```
// 内联呈现
<Route path="/home" render={() => <div>Home</div>}/>

// 包装/合成
const FadingRoute = ({ component: Component, ...rest }) => (
<Route {...rest} render={props => (
  <FadeIn>
    <Component {...props}/>
  </FadeIn>
)}/>
)

<FadingRoute path="/cool" component={Something}/>
```

__children__ ： function
有时您需要渲染路径是否匹配该位置。在这些情况下，可以使用函数child prop。它的工作原理就像渲染，除了它被调用是否有匹配
children 渲染prop接收与组件和渲染方法相同的所有route props，除非路由未能匹配URL，则match为null。
这允许您根据路线是否匹配来动态调整用户界面。在这里，如果路由匹配，我们添加一个活动类

警告：&lt;Route component&gt;和&lt;Route render&gt;优先级高于&lt;Route children&gt;，所以不要在同一个&lt;Route&gt;中使用多个


----
### Switch

解决路匹配机制问题的组件

当我们定义了很多路由的时候
```
<Route path="/about" component={About}/>
<Route path="/:user" component={User}/>
<Route component={NoMatch}/>
```
路由的匹配机制并不是我们所想象的，匹配到符合的路径后就会停止，而是会继续向后面匹配，如果匹配到多个符合的路径时，会将这些路径对应的组件都渲染，这就让我们难接受了

```
<Switch>
  <Route exact path="/" component={Home}/>
  <Route path="/about" component={About}/>
  <Route path="/:user" component={User}/>
  <Route component={NoMatch}/>
</Switch>
```
所有我们要是用Switch 这个路由组件，来帮助我们解决这个问题，将所有的路由包裹在Switch组件中，路由会根据路径从上至下的匹配，如果匹配到符合的路径，就会立即停止匹配，然后渲染对应组件


## react-router 提供的属性和方法

所有通过路由渲染的组件都会携带 Route props
    1、history
    2、location
    3、match

### history

history对象通常具有以下属性和方法：
    length : （number）历史堆栈中的条目数
    action : （string）当前动作（PUSH，REPLACE或POP）
    location : (object) 当前位置。具有以下属性：
    pathname: URL的路径
    search: URL查询字符串
    hash: URL哈希片段
    state: 位置特定的状态被提供给例如。当这个位置被推到堆栈上时，push（路径，状态）。仅在浏览器和内存历史记录中可用。
    push : (path, [state]) - (function) 将新条目推入历史堆栈
    replace : (path, [state]) - (function) 替换历史堆栈上的当前条目
    go(n) : (function) 将历史堆栈中的指针移动n个条目
    goBack() : (function) 相当于 go(-1)
    goForward() : (function) 相当于 go(1)
    block : (function) 防止导航 (https://github.com/ReactTraining/history#blocking-transitions)

![](http://ww1.sinaimg.cn/large/e315dd2ely1fpkkba3om4j20ef08jwep.jpg)

### location

location对象的常用属性：
    key: （在HashHistory中没有这个属性）
    pathname: URL的路径
    search: URL查询字符串
    hash: URL哈希片段
    state: 位置特定的状态被提供给例如。当这个位置被推到堆栈上时，push（路径，状态）。仅在浏览器和内存历史记录中可用。

![](http://ww1.sinaimg.cn/large/e315dd2ely1fpkkba4e23j20eh02qt8m.jpg)

### match

match对象的常用属性：
    params - （object）从对应于路径的动态段的URL解析的键/值对
    isExact - （boolean）true如果整个URL匹配（没有尾随字符
    path - （string）用于匹配的路径模式。作用于构建嵌套的<Route>
    url - （string）URL的匹配部分。作用于构建嵌套的<Link> s

![](http://ww1.sinaimg.cn/large/e315dd2ely1fpkkba5osxj20el03yglk.jpg)

### withRouter

也是比较常用的一个路由方法，withRouter其实是一个react的高阶组件（什么是高阶组件？高阶组件是一个函数，能够接受一个组件并返回一个新的组件）

在项目中我们有一些组件并不是通过路由跳转，但是我们还想使用路由中的方法，我们就需要通过withRouter来将我们的组件包装一下

```
class ShowTheLocation extends React.Component {
  static propTypes = {
    match: PropTypes.object.isRequired,
    location: PropTypes.object.isRequired,
    history: PropTypes.object.isRequired
  }

  render() {
    const { match, location, history } = this.props

    return (
      <div>You are now at {location.pathname}</div>
    )
  }
}

export default withRouter(ShowTheLocation)

// 如果这个组件中使用了redux

export default withRouter(connect(state,action)(ShowTheLocation));
```

如果有任何疑问或者文章内有错误，可以发邮件到1043951234@qq.com 进行交流，如果有错误我会第一时间更正，感谢您的赐教