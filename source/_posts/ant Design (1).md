title: ant Design（1）
date: 2018-07-17
tags: js
toc: true
categories: ant Design
---

## ant Design

### 栅格布局 (24 栅格布局)

#### 概述

> 布局的栅格化系统，我们是基于行（row）和列（col）来定义信息区块的外部框架，以保证页面的每个区域能够稳健地排布起来。下面简单介绍一下它的工作原理：
> - 通过row在水平方向建立一组column（简写col）
> - 你的内容应当放置于col内，并且，只有col可以作为row的直接元素
> - 栅格系统中的列是指1到24的值来表示其跨越的范围。例如，三个等宽的列可以使用.col-8来创建
> - 如果一个row中的col总和超过 24，那么多余的col会作为一个整体另起一行排列

#### 使用方式
```
import React from 'react'
import ReactDOM from 'react-dom'
import { Row, Col } from 'antd';

// (n 是自然数)
// 栅格常常需要和间隔进行配合，你可以使用 Row 的 gutter 属性，我们推荐使用 (16+8n)px 作为栅格间隔。
// Col 的 span 属性是指 Col 在 Row 中 n 份
// Col 的 offset 属性是指元素向右偏移 n 列的宽度

ReactDOM.render(
  <div>
    <Row gutter={16}> // 样例
      <Col span={12}>col-12</Col>
      <Col span={6} offset={6}>col-12</Col>
    </Row>
    <Row>
      <Col span={8}>col-8</Col>
      <Col span={8}>col-8</Col>
      <Col span={8}>col-8</Col>
    </Row>
    <Row>
      <Col span={6}>col-6</Col>
      <Col span={6}>col-6</Col>
      <Col span={6}>col-6</Col>
      <Col span={6}>col-6</Col>
    </Row>
  </div>,
  mountNode
);
```
##### Row 和 Col 的参数

> Row
> - align：flex 布局下的垂直对齐方式：top middle bottom
>  + 类型：string
>  + 默认值： top
> - gutter：栅格间隔；默认值：0
>  + 类型：number
>  + 默认值： 0
> - justify ：flex 布局下的水平排列方式：start end center space-around space-between
>  + 类型：string
>  + 默认值： start
> - type：布局模式，可选 flex，现代浏览器下有效
>  + 类型：string

> Col
> - offset：栅格左侧的间隔格数，间隔内不可以有栅格
>  + 类型：number
>  + 默认值： 0
> - order：栅格栅格顺序，flex 布局模式下有效
>  + 类型：number
>  + 默认值： 0
> - pull：栅格栅格向左移动格数
>  + 类型：number
>  + 默认值： 0
> - push：栅格向右移动格数
>  + 类型：number
>  + 默认值： 0
> - span：栅格占位格数，为 0 时相当于 display: none
>  + 类型：number
> - xs：<768px 响应式栅格，可为栅格数或一个包含其他属性的对象
>  + 类型：number | object
> - sm：≥768px 响应式栅格，可为栅格数或一个包含其他属性的对象
>  + 类型：number
> - md：≥992px 响应式栅格，可为栅格数或一个包含其他属性的对象
>  + 类型：number | object
> - lg：≥1200px 响应式栅格，可为栅格数或一个包含其他属性的对象
>  + 类型：number | object
> - xl：≥1600px 响应式栅格，可为栅格数或一个包含其他属性的对象
>  + 类型：number | object

---

### Layout 布局

#### 设计规划

> 尺寸
> - 顶部导航（大部分系统）：一级导航高度 64px，二级导航 48px
> - 顶部导航（展示类页面）：一级导航高度 80px，二级导航 56px
> - 顶部导航高度的范围计算公式为：48+8n
> - 侧边导航宽度的范围计算公式：200+8n
> 
> 交互
> - 一级导航和末级的导航需要在可视化的层面被强调出来；
> - 当前项应该在呈现上优先级最高；
> - 当导航收起的时候，当前项的样式自动赋予给它的上一个层级；
> - 左侧导航栏的收放交互同时支持手风琴和全展开的样式，根据业务的要求进行适当的选择。
> 
> Layout 及 组件成员
> - Layout：布局容器，其下可嵌套 Header Sider Content Footer 或 Layout 本身，可以放在任何父容器中。
> - Header：顶部布局，自带默认样式，其下可嵌套任何元素，只能放在 Layout 中。
> - Sider：侧边栏，自带默认样式及基本功能，其下可嵌套任何元素，只能放在 Layout 中。
> - Content：内容部分，自带默认样式，其下可嵌套任何元素，只能放在 Layout 中。
> - Footer：底部布局，自带默认样式，其下可嵌套任何元素，只能放在 Layout 中。

---

#### 用法

```
// 最基本的上中下布局

import { Layout, Menu, Breadcrumb } from 'antd';
const { Header, Content, Footer } = Layout;

ReactDOM.render(
  <Layout className="layout">
    <Header>
      <div className="logo" />
      <Menu
        theme="dark"
        mode="horizontal"
        defaultSelectedKeys={['2']}
        style={{ lineHeight: '64px' }}
      >
        <Menu.Item key="1">nav 1</Menu.Item>
        <Menu.Item key="2">nav 2</Menu.Item>
        <Menu.Item key="3">nav 3</Menu.Item>
      </Menu>
    </Header>
    <Content style={{ padding: '0 50px' }}>
      <Breadcrumb style={{ margin: '16px 0' }}>
        <Breadcrumb.Item>Home</Breadcrumb.Item>
        <Breadcrumb.Item>List</Breadcrumb.Item>
        <Breadcrumb.Item>App</Breadcrumb.Item>
      </Breadcrumb>
      <div style={{ background: '#fff', padding: 24, minHeight: 280 }}>Content</div>
    </Content>
    <Footer style={{ textAlign: 'center' }}>
      Ant Design ©2016 Created by Ant UED
    </Footer>
  </Layout>
, mountNode);
```

#### Layout 参数

> className ：容器 className
> - 类型： string
> 
> style ：指定样式
> - 类型： object

####  Layout.Sider
> 
> breakpoint：触发响应式布局的断点
> - 类型： Enum { 'xs', 'sm', 'md', 'lg', 'xl' }
> 
> className：容器 className
>  - 类型： string
>  
> collapsed：当前收起状态
>  - 类型： boolean
>  
> collapsedWidth：收缩宽度，设置为 0 会出现特殊 trigger
>  - 类型： number
>  
> collapsible：是否可收起
>  - 类型： boolean
>  
> defaultCollapsed：是否默认收起
>  - 类型： boolean
>  
> reverseArrow：翻转折叠提示箭头的方向，当 Sider 在右边时可以使用
>  - 类型： boolean
>  
> style：指定样式
>  - 类型： object
>  
> trigger：自定义 trigger，设置为 null 时隐藏 trigger
>  - 类型： string | ReactNode
>  
> width：宽度
>  - 类型： number | string
>  
> onCollapse：展开-收起时的回调函数，有点击 trigger 以及响应式反馈两种方式可以触发
>  - 类型：(collapsed, type) => {}

---

###  Affix 固钉

#### 使用方法

```
import { Affix, Button } from 'antd';

ReactDOM.render(
  <div>
    <Affix offsetTop={10} onChange={affixed => console.log(affixed)}>
      <Button type="primary">Affix top</Button>
    </Affix>
    <br />
    <Affix offsetBottom={0}>
      <Button type="primary">Affix bottom</Button>
    </Affix>
  </div>,
  mountNode
);

// 官方滚动容器demo是有一些小问题的，当容器内滚动定位后，再滚动页面的滚动条后，定位的元素会脱离滚动容器的限制，问题原因是因为使用了fix定位导致的，当窗口是一屏的时候不会出现这个问题，但是如果页面超过一屏的时候，fix 相对于浏览器窗口定位的问题就出现了，使用的时候要慎重使用
// 官方给出的解决方案是<Affix style={{ position: 'absolute', top: y, left: x}}></Affix>；我们可以手动将 fix 定位改成 absolute 定位
```

#### Affix  参数

> offsetBottom ： 距离窗口底部达到指定偏移量后触发
>  - 类型 ：number
>  
> offsetTop ： 距离窗口顶部达到指定偏移量后触发
>  - 类型 ：number
>  
> target ： 设置 Affix 需要监听其滚动事件的元素，值为一个返回对应 DOM 元素的函数
> - 类型 ： () => HTMLElement
> - 默认值 ： () => window
> 
> onChange ： 固定状态改变时触发的回调函数
> - 类型 ： Function(affixed)

---

### Breadcrumb面包屑

#### 使用方法

```
import { Breadcrumb } from 'antd';

ReactDOM.render(
  <Breadcrumb>
    <Breadcrumb.Item>Home</Breadcrumb.Item>
    <Breadcrumb.Item><a href="">Application Center</a></Breadcrumb.Item>
    <Breadcrumb.Item><a href="">Application List</a></Breadcrumb.Item>
    <Breadcrumb.Item>An Application</Breadcrumb.Item>
  </Breadcrumb>
, mountNode);
```

```
// 配合react-router@3 搭建面包屑
import { Router, Route, Link, hashHistory } from 'react-router';
import { Breadcrumb, Alert } from 'antd';

const Apps = () => (
  <ul className="app-list">
    <li>
      <Link to="/apps/1">Application1</Link>：<Link to="/apps/1/detail">Detail</Link>
    </li>
    <li>
      <Link to="/apps/2">Application2</Link>：<Link to="/apps/2/detail">Detail</Link>
    </li>
  </ul>
);

const Home = ({ routes, params, children }) => (
  <div className="demo">
    <div className="demo-nav">
      <Link to="/">Home</Link>
      <Link to="/apps">Application List</Link>
    </div>
    {children || 'Home Page'}
    <Alert style={{ margin: '16px 0' }} message="Click the navigation above to switch:" />
    <Breadcrumb routes={routes} params={params} />
  </div>
);

ReactDOM.render(
  <Router history={hashHistory}>
    <Route name="home" breadcrumbName="Home" path="/" component={Home}>
      <Route name="apps" breadcrumbName="Application List" path="apps" component={Apps}>
        <Route name="app" breadcrumbName="Application:id" path=":id">
          <Route name="detail" breadcrumbName="Detail" path="detail" />
        </Route>
      </Route>
    </Route>
  </Router>
, mountNode);
```

```
// 配合react-router@4 搭建面包屑
import { HashRouter as Router, Route, Switch, Link, withRouter } from 'react-router-dom';
import { Breadcrumb, Alert } from 'antd';

const Apps = () => (
  <ul className="app-list">
    <li>
      <Link to="/apps/1">Application1</Link>：<Link to="/apps/1/detail">Detail</Link>
    </li>
    <li>
      <Link to="/apps/2">Application2</Link>：<Link to="/apps/2/detail">Detail</Link>
    </li>
  </ul>
);

const breadcrumbNameMap = {
  '/apps': 'Application List',
  '/apps/1': 'Application1',
  '/apps/2': 'Application2',
  '/apps/1/detail': 'Detail',
  '/apps/2/detail': 'Detail',
};
const Home = withRouter((props) => {
  const { location } = props;
  const pathSnippets = location.pathname.split('/').filter(i => i);
  const extraBreadcrumbItems = pathSnippets.map((_, index) => {
    const url = `/${pathSnippets.slice(0, index + 1).join('/')}`;
    return (
      <Breadcrumb.Item key={url}>
        <Link to={url}>
          {breadcrumbNameMap[url]}
        </Link>
      </Breadcrumb.Item>
    );
  });
  const breadcrumbItems = [(
    <Breadcrumb.Item key="home">
      <Link to="/">Home</Link>
    </Breadcrumb.Item>
  )].concat(extraBreadcrumbItems);
  return (
    <div className="demo">
      <div className="demo-nav">
        <Link to="/">Home</Link>
        <Link to="/apps">Application List</Link>
      </div>
      <Switch>
        <Route path="/apps" component={Apps} />
        <Route render={() => <span>Home Page</span>} />
      </Switch>
      <Alert style={{ margin: '16px 0' }} message="Click the navigation above to switch:" />
      <Breadcrumb>
        {breadcrumbItems}
      </Breadcrumb>
    </div>
  );
});

ReactDOM.render(
  <Router>
    <Home />
  </Router>
, mountNode);
```

```
// 配合 browserHistory 搭建面包屑导航
import { Link } from 'react-router';

const routes = [{
  path: 'index',
  breadcrumbName: '首页'
}, {
  path: 'first',
  breadcrumbName: '一级面包屑'
}, {
  path: 'second',
  breadcrumbName: '当前页面'
}];
function itemRender(route, params, routes, paths) {
  const last = routes.indexOf(route) === routes.length - 1;
  return last ? <span>{route.breadcrumbName}</span> : <Link to={paths.join('/')}>{route.breadcrumbName}</Link>;
}

return <Breadcrumb itemRender={itemRender} routes={routes}/>;
```

#### Breadcrumb 参数

> itemRender ： 自定义链接函数，和 react-router 配置使用
> - 类型 ：(route, params, routes, paths) => ReactNode
> 
> params ： 路由的参数
> - 类型 ： object
> 
> routes ： router 的路由栈信息
> - 类型 ： object[]
> 
> separator ： 分隔符自定义
> - 类型 ： string | ReactNode
> - 默认值 ： ' / '


### Dropdown下拉菜单

#### 使用方法

```
import { Menu, Dropdown, Icon } from 'antd';

const onClick = function ({ key }) {
  message.info(`Click on item ${key}`);
};

// 如果需要在点击选项的时候触发处理一些事情可以在 Menu 组件的 onClick 中处理
const menu = (
  <Menu onClick={onClick}>
    <Menu.Item key='1'>
      <a target="_blank" rel="noopener noreferrer" href="http://www.alipay.com/">1st menu item</a>
    </Menu.Item>
    <Menu.Item key='2'>
      <a target="_blank" rel="noopener noreferrer" href="http://www.taobao.com/">2nd menu item</a>
    </Menu.Item>
    <Menu.Divider /> // 下拉分割线
    <Menu.Item key='3'>
      <a target="_blank" rel="noopener noreferrer" href="http://www.tmall.com/">3rd menu item</a>
    </Menu.Item>
    <SubMenu title="sub menu"> // 多级菜单
      <Menu.Item>3rd menu item</Menu.Item>
      <Menu.Item>4th menu item</Menu.Item>
    </SubMenu>
  </Menu>
);

ReactDOM.render(
  <Dropdown overlay={menu}>
    <a className="ant-dropdown-link" href="#">
      Hover me <Icon type="down" />
    </a>
  </Dropdown>
, mountNode);
```

#### Dropdown  参数

> disabled ： 菜单是否禁用
>  - 类型 ： boolean
>  
> getPopupContainer ： 菜单渲染父节点。默认渲染到 body 上，如果你遇到菜单滚动定位问题，试试修改为滚动的区域，并相对其定位
>  - 类型 ： Function(triggerNode)
>  - 默认值 ： () => document.body
>  
> overlay ： 菜单
>   - 类型 ： Menu
>   
> placement ： 菜单弹出位置：bottomLeft bottomCenter bottomRight topLeft topCenter topRight
>   - 类型 ： String
>   - 默认值 ： bottomLeft
>   
> trigger ： 触发下拉的行为
>   - 类型 ： Array<'click'|'hover'>
>   
> visible ： 菜单是否显示
>   - 类型 ： boolean
>   - 默认值 ： 'hover'
>   
> onVisibleChange ： 菜单显示状态改变时调用，参数为 visible
>   - 类型 ： Function(visible)

#### Dropdown.Button

> disabled ： 菜单是否禁用
>  - 类型 ： boolean
>  
> overlay： 菜单
>  - 类型 ： Menu
>  
> placement： 菜单弹出位置：bottomLeft bottomCenter bottomRight topLeft topCenter topRight
>  - 类型 ： String
>  - 默认值 ： bottomLeft
>  
> size： 按钮大小，和 Button 一致
>  - 类型 ： String
>  - 默认值 ： 'default'
>  
> trigger： 按触发下拉的行为
>  - 类型 ： Array<'click'|'hover'>
>  - 默认值 ： ''hover'
>  
> type： 按钮类型，和 Button 一致
>  - 类型 ： String
>  - 默认值 ： 'default'
>  
> visible： 菜单是否显示
>  - 类型 ： boolean
>  
> onClick： 点击左侧按钮的回调，和 Button 一致
>  - 类型 ： Function
>  
> onVisibleChange	： 菜单显示状态改变时调用，参数为 visible
>  - 类型 ： Function

---

### Menu 

#### 使用方法

```
import { Menu, Icon } from 'antd';
const SubMenu = Menu.SubMenu;
const MenuItemGroup = Menu.ItemGroup;

class App extends React.Component {
  state = {
    current: 'mail',  // 当前选中项的 key 值
  }
  handleClick = (e) => {
    console.log('click ', e);
    this.setState({
      current: e.key,
    });
  }
  render() {
    return (
      <Menu
        onClick={this.handleClick}
        selectedKeys={[this.state.current]} // 选中项
        mode="horizontal"
      >
        <Menu.Item key="mail">
          <Icon type="mail" />Navigation One
        </Menu.Item>
        <Menu.Item key="app" disabled>
          <Icon type="appstore" />Navigation Two
        </Menu.Item>
        <SubMenu title={<span><Icon type="setting" />Navigation Three - Submenu</span>}>
          <MenuItemGroup title="Item 1"> // 多级餐单分类
            <Menu.Item key="setting:1">Option 1</Menu.Item>
            <Menu.Item key="setting:2">Option 2</Menu.Item>
          </MenuItemGroup>
          <MenuItemGroup title="Item 2">
            <Menu.Item key="setting:3">Option 3</Menu.Item>
            <Menu.Item key="setting:4">Option 4</Menu.Item>
          </MenuItemGroup>
        </SubMenu>
        <Menu.Item key="alipay">
          <a href="https://ant.design" target="_blank" rel="noopener noreferrer">Navigation Four - Link</a>
        </Menu.Item>
      </Menu>
    );
  }
}

ReactDOM.render(<App />, mountNode);
```

> 参考网址 ：[Menu ](http://2x.ant.design/components/menu-cn/) 

####  Menu 参数

> defaultOpenKeys ：初始展开的 SubMenu 菜单项 key 数组
> 
> defaultSelectedKeys ：初始选中的菜单项 key 数组
>  - 类型 ：string[]
>  
>  inlineCollapsed ：inline 时菜单是否收起状态
>  - 类型 ：boolean
>  
> inlineIndent ：inline 模式的菜单缩进宽度
>  - 类型 ：number
>  - 默认值 ：24
>  
>  mode ：菜单类型，现在支持垂直、水平、和内嵌模式三种
>  - 类型 ：string （vertical horizontal inline）
>  - 默认值 ：vertical
>  
>  multiple ：是否允许多选
>  - 类型 ：boolean
>  - 默认值 ：false
>  
>  openKeys：当前展开的 SubMenu 菜单项 key 数组
>  - 类型 ：string[]
>  selectable：是否允许多选
>  - 类型 ：boolean
>  - 默认值 ：true
>  
>  selectedKeys ：当前选中的菜单项 key 数组
>  - 类型 ：string[]
>  
>  style ： 根节点样式
>  - 类型 ：object
>  
>  theme ： 主题颜色
>  - 类型 ：string （ light dark）
>  - 默认值 ：light
>  
>  onClick： 点击 MenuItem 调用此函数
>  - 类型 ：function({ item, key, keyPath })
>  
>  onDeselect： 取消选中时调用，仅在 multiple 生效
>  - 类型 ：function({ item, key, selectedKeys })
>  
>  onOpenChange： SubMenu 展开/关闭的回调
>  - 类型 ：function(openKeys: string[])
>  - 默认值 ：noop
>  
>  onSelect： 被选中时调用
>  - 类型 ：function({ item, key, selectedKeys })

#### Menu.Item 参数

> disabled ： 是否禁用
>  - 类型 ：boolean
>  - 默认值 ：false
>  
> key ： item 的唯一标志
>  - 类型 ：string


#### Menu.SubMenu 参数

> children  ：子菜单的菜单项
>  - 类型 ：Array<MenuItem|SubMenu>
>  
> disabled ：是否禁用
>  - 类型 ：boolean
>  - 默认值 ：false
>  
> key ：唯一标志
>  - 类型 ：string
>  
> title ：子菜单项值
>  - 类型 ：string|ReactNode
>  
> onTitleClick ：点击子菜单标题
>  - 类型 ：function({ key, domEvent })

#### Menu.ItemGroup 参数

> children ：分组的菜单项
>  - 类型 ： MenuItem[]
>  title ： 分组标题
>  - 类型 ：string | ReactNode

---

### Pagination分页

#### Pagination 用法

```
import { Pagination } from 'antd';

ReactDOM.render(<Pagination defaultCurrent={1} total={50} />, mountNode);
```

#### Pagination  参数

> current ：当前页数
>  - 类型 ：number
>  
> defaultCurrent：当前页默认的当前页数
>  - 类型 ：number
>  - 默认值 ： 1
>  
> defaultPageSize：默认的每页条数
>  - 类型 ：number
>  - 默认值 ： 10
>  
> itemRender：用于自定义页码的结构，可用于优化 SEO
>  - 类型 ：(page, type: 'page' | 'prev' | 'next', originalElement) => React.ReactNode
> pageSize：每页条数
>  - 类型 ：number
>  
> pageSizeOptions：指定每页可以显示多少条
>  - 类型 ：string[]
>  - 默认值 ： '10', '20', '30', '40'
>  
> showQuickJumper：是否可以快速跳转至某页
>  - 类型 ：boolean
>  - 默认值 ：false
>  
> showSizeChanger：是否可以改变 pageSize
>  - 类型 ：boolean
>  - 默认值 ：false
>  
> showTotal：用于显示数据总量和当前数据顺序
>  - 类型 ：Function(total, range) / range ：Array
>  
> simple：当添加该属性时，显示为简单分页
>  - 类型 ：boolean
>  
> size：当为「small」时，是小尺寸分页
>  - 类型 ：string
>  - 默认值 ：“”
> total：数据总数
>  - 类型 ：number
>  - 默认 ： 0
>  
> onChange：页码改变的回调，参数是改变后的页码及每页条数
>  - 类型 ：Function(page, pageSize)
>  - 默认 ： noop
>  
> onShowSizeChange：页码pageSize 变化的回调
>  - 类型 ：Function(current, size)
>  - 默认 ： noop

---

### Steps步骤条

#### Steps 使用方法

```
import { Steps } from 'antd';
const Step = Steps.Step;

ReactDOM.render(
  <Steps current={1}>
    <Step title="Finished" description="This is a description." />
    <Step title="In Progress" description="This is a description." />
    <Step title="Waiting" description="This is a description." />
  </Steps>
, mountNode);
```

#### Steps 参数

> current ： 指定当前步骤，从 0 开始记数。在子 Step 元素中，可以通过 status 属性覆盖状态
>  - 类型 ：number
>  - 默认值 ：0
>  
> direction：指定步骤条方向 horizontal vertical
>  - 类型 ：string
>  - 默认值 ：horizontal
>  
> progressDot：点状步骤条，可以设置为一个 function
>  - 类型 ：Boolean or (iconDot, {index, status, title, description}) => ReactNode
>  - 默认值 ：false
>  
> size：指定大小，目前支持普通 default 和迷你 small
>  - 类型 ：string
>  - 默认值 ：default
>  
> status：指指定当前步骤的状态，可选 wait process finish error
>  - 类型 ：string
>  - 默认值 ：process

#### Steps.Step 参数

> description ：步骤的详情描述，可选
>  - 类型 ：string | ReactNode
>  
> icon ：步骤图标的类型，可选
>  - 类型 ：string | ReactNode
>  
> status：指定状态。当不配置该属性时，会使用 Steps 的 current 来自动指定状态。可选：wait process finish error
>  - 类型 ：sstring
>  - 默认值 ：wait
>  
> title：标题
>  - 类型 ：string | ReactNode

---

### AutoComplete 输入框自动完成

#### 用法

```
import { AutoComplete } from 'antd';

function onSelect(value) {
  console.log('onSelect', value);
}

class Complete extends React.Component {
  state = {
    dataSource: [],
  }

  handleSearch = (value) => {
    this.setState({
      dataSource: !value ? [] : [
        value,
        value + value,
        value + value + value,
      ],
    });
  }

  render() {
    const { dataSource } = this.state;
    return (
      <AutoComplete
        dataSource={dataSource}
        style={{ width: 200 }}
        onSelect={onSelect}
        onSearch={this.handleSearch}
        placeholder="input here"
      />
    );
  }
}

ReactDOM.render(<Complete />, mountNode);
```


#### AutoComplete  参数

> allowClear ： 支持清除, 单选模式有效
>  - 类型 ：boolean
>  - 默认值：false
> 
> backfill ： 使用键盘选择选项的时候把选中项回填到输入框中	
>  - 类型 ：boolean
>  - 默认值：false
> 
> children ： 自动完成的数据源
>  - 类型 ：React.ReactElement / Array<React.ReactElement>
> 
> children  ： 自定义输入框
>  - 类型 ：HTMLInputElement / HTMLTextAreaElement / React.ReactElement
>  - 默认值：<Input />
> 
> dataSource： 自动完成的数据源
>  - 类型 ：DataSourceItemType[]
> 
> defaultActiveFirstOption：是否默认高亮第一个选项。
>  - 类型 ：boolean
>  - 默认值：true
> 
> defaultValue ： 指定默认选中的条目
>  - 类型 ：string|string[] | { key: string, label: string | ReactNode } | Array<{ key: string, label: string | ReactNode}>
> 
> disabled ： 是否禁用
>  - 类型 ：boolean
>  - 默认值：false
> 
> filterOption ： 是否根据输入项进行筛选。当其为一个函数时，会接收 inputValue option 两个参数，当 option 符合筛选条件时，应返回 true，反之则返回 false
>  - 类型 ：boolean or function(inputValue, option)
>  - 默认值：true
> 
> optionLabelProp ： 回填到选择框的 Option 的属性值，默认是 Option 的子元素。比如在子元素需要高亮效果时，此值可以设为 value
>  - 类型 ：string
> 
> placeholder： 输入框提示	
>  - 类型 ：boolean
>  - 默认值：false
> 
> value： 指定当前选中的条目	
>  - 类型 ：string|string[] | { key: string, label: string|ReactNode } | Array<{ key: string, label: string | ReactNode }>
> 
> onChange： 选中 option，或 input 的 value 变化时，调用此函数	
>  - 类型 ：function(value)
> 
> onSearch ： 搜索补全项的时候调用	
>  - 类型 ：function(value)
>  - 默认值：false
> 
> onSelect ： 被选中时调用，参数为选中项的 value 值	
>  - 类型 ：function(value, option)

---

### Cascader 

#### Cascader 使用方法

```
import { Cascader } from 'antd';

const options = [{
  value: 'zhejiang',
  label: 'Zhejiang',
  children: [{
    value: 'hangzhou',
    label: 'Hangzhou',
    children: [{
      value: 'xihu',
      label: 'West Lake',
    }],
  }],
}, {
  value: 'jiangsu',
  label: 'Jiangsu',
  children: [{
    value: 'nanjing',
    label: 'Nanjing',
    children: [{
      value: 'zhonghuamen',
      label: 'Zhong Hua Men',
    }],
  }],
}];

function onChange(value) {
  console.log(value);
}

ReactDOM.render(
  <Cascader options={options} onChange={onChange} placeholder="Please select" />
, mountNode);
```

#### Cascader 参数

> allowClear ： 是否支持清除
> - 类型：boolean
> - 默认值：true
> 
> changeOnSelect：当此项为 true 时，点选每级菜单选项值都会发生变化
> - 类型：boolean
> - 默认值：true
> 
> className： 自定义类名
> - 类型：string
> 
> defaultValue： 默认的选中项
> - 类型：CascaderOptionType[]
> - 默认值：[]
> 
> disabled： 是否禁用
> - 类型：boolean
> - 默认值：false
> 
> displayRender： 选择后展示的渲染函数
> - 类型：(label, selectedOptions) => ReactNode
> - 默认值：label => label.join(' / ')
> 
> expandTrigger： 次级菜单的展开方式，可选 'click' 和 'hover'
> - 类型：string
> - 默认值：'click'
> 
> getPopupContainer： 菜单渲染父节点。默认渲染到 body 上，如果你遇到菜单滚动定位问题，试试修改为滚动的区域，并相对其定位
> - 类型：Function(triggerNode)
> - 默认值：() => document.body
> 
> loadData： 用于动态加载选项，无法与 showSearch 一起使用
> - 类型：(selectedOptions) => void
> 
> notFoundContent ： 当下拉列表为空时显示的内容
> - 类型：string
> - 默认值：'Not Found'
> 
> placeholder： 输入框占位文本
> - 类型：string
> - 默认值：'请选择'
> 
> popupClassName： 自定义浮层类名
> - 类型：string
> 
> popupPlacement： 浮层预设位置：bottomLeft bottomRight topLeft topRight
> - 类型：Enum
> - 默认值：bottomLeft
> 
> showSearch ： 在选择框中显示搜索框
> - 类型：boolean
> - 默认值：false
> 
> size： 输入框大小，可选 large default small
> - 类型：string
> - 默认值：default
> 
> style：自定义样式
> - 类型：object
> 
> value ： 指定选中项
> - 类型：CascaderOptionType[]
> 
> onChange： 选择完成后的回调
> - 类型：(value, selectedOptions) => void
> 
> onPopupVisibleChange：显示/隐藏浮层的回调
> - 类型：(value) => void
> 
> popupVisible： 控制浮层显隐
> - 类型：boolean
> 
> ##### showSearch 为对象时，其中的字段
> filter ： 接收 inputValue path 两个参数，当 path 符合筛选条件时，应返回 true，反之则返回 false。
>  - 类型 ： function(inputValue, path): boolean
> matchInputWidth ： 搜索结果列表是否与输入框同宽
>  - 类型 ： boolean
> render ： 用于渲染 filter 后的选项
>  - 类型 ：function(inputValue, path): ReactNode	
> sort ： 用于排序 filter 后的选项
>  - 类型 ： function(a, b, inputValue)

---

### Checkbox 多选框

#### Checkbox 使用方法

```
import { Checkbox } from 'antd';
const CheckboxGroup = Checkbox.Group;

const plainOptions = ['Apple', 'Pear', 'Orange'];
const defaultCheckedList = ['Apple', 'Orange'];

class App extends React.Component {
  state = {
    checkedList: defaultCheckedList,
    indeterminate: true,
    checkAll: false,
  };
  render() {
    return (
      <div>
        <div style={{ borderBottom: '1px solid #E9E9E9' }}>
          <Checkbox
            indeterminate={this.state.indeterminate}
            onChange={this.onCheckAllChange}
            checked={this.state.checkAll}
          >
            Check all
          </Checkbox>
        </div>
        <br />
        <CheckboxGroup options={plainOptions} value={this.state.checkedList} onChange={this.onChange} />
      </div>
    );
  }
  onChange = (checkedList) => {
    this.setState({
      checkedList,
      indeterminate: !!checkedList.length && (checkedList.length < plainOptions.length),
      checkAll: checkedList.length === plainOptions.length,
    });
  }
  onCheckAllChange = (e) => {
    this.setState({
      checkedList: e.target.checked ? plainOptions : [],
      indeterminate: false,
      checkAll: e.target.checked,
    });
  }
}

ReactDOM.render(<App />, mountNode);
```

####  Checkbox 参数

> checked ：指定当前是否选中
>  - 类型 ：boolean
>  - 默认值 ： false
>  
> defaultChecked ：初始是否选中
>  - 类型 ：boolean
>  - 默认值 ： false
>  
> indeterminate：设置 indeterminate 状态，只负责样式控制
>  - 类型 ：boolean
>  - 默认值 ： false
>  
> onChange：变化时回调函数
>  - 类型 ：Function(e：Event)

#### Checkbox Group 参数

> defaultValue ：默认选中的选项
>  - 类型 ：string[]
>  - 默认值 ： []
>  
> options：指定可选项
>  - 类型 ：string[]
>  - 默认值 ： []
>  
> value ：指定选中的选项
>  - 类型 ：string[]
>  - 默认值 ： []
>  
> onChange ：变化时回调函数
>  - 类型 ：Function(checkedValue)