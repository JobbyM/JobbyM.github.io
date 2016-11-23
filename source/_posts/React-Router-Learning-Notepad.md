---
title: React Router Learning Notepad
date: 2016-11-21 14:23:31
tags:
  - React
  - React Router
  - 技术
categories: 技术
comments: false
---

## 介绍

1. React Router 是完整的React 路由解决方案
2. React Router 保持UI 与URL 同步。它拥有简单的API 与强大的功能，例如：代码缓冲加载、动态路由匹配、以及建立正确的位置过渡处理。你第一个念头想到的应该是URL，而不是事后再想起。

<!--more-->

## 简介

1. React Router 是一个基于React 之上的强大路由库，它可以让你像应用快速地添加视图和数据流，同时保持页面与URL 间的同步。

## 安装

1. 首先，通过npm 安装
```bash
$ npm install --save react-router
```

### 使用React Router

1. React Route 知道如何为我们搭建嵌套的UI，因此我们不用手动找出需要渲染哪些`<Child>` 组件。举个例子，对于一个完整的`/about` 路径，React Router 会搭建出 `<App><About /></App>`。
2. 在内部，router 会将你树级嵌套格式的`<Route>` 转变为路由配置。

### 添加更多的UI

1. 现在我们准备在inbox UI 内嵌套inbox messages
```jsx
class Message extends Component {
  render(){
    return (
      <h3>Message</h3>
    )
  }
}

ReactDOM.render((
  <Router history={browserHistory}>
    <Route path="/" component={App}>
      <Route path="about" component={About} />
      <Route path="inbox" component={Inbox}>
        {/* 添加一个路由，嵌套进我们想要嵌套的UI 里 */}
        <Route path="messages/:id" component={Message} />
      </Route>
    </Route>
  </Router>
), document.getElementById('app')
)
```
2. 现在访问URL `inbox/messages/Jkei3c32` 将会匹配到一个新的路由，并且它成功指向了`App -> Inbox -> Message` 这个UI 的分支
3. 你也可以通过query 字符串来访问参数。比如你访问`/foo?bar=baz`，你可以通过访问`this.props.localtion.query.bar` 从Route 组件中获得"baz" 的值。
4. 这就是React Router 的奥秘。应用的UI 以盒子中嵌套盒子的方式来表现；然后你可以让这些盒子与URL 始终保持同步，而且很容易地把它们链接起来。

## 基础
### 路由配置

1. 路由配置是一组指令，用来告诉router 如何配置URL 以及配置后如何执行代码。
2. 通过上面的配置，这个应用知道如何渲染下面四个URL

URL | 组件
---|---|---
/ | App
/about | App -> About
/inbox | App -> Inbox
/inbox/message/:id | App -> Inbox -> Message

#### 添加首页

1. 想象一下当URL 为`/` 时，我们想要渲染一个在`App` 中的组件。不过在此时，`App` 的`render` 中的`this.props.children` 还是`undefined` 。这种情况我们可以使用`IndexRoute` 来设置一个默认页面
```jsx
import { Router, Route, Link, browserHistory, IndexRoute } from 'react-router';

class Dashboard extends Component {
  render(){
    return (
      <div>Welcome to the App!</div>
    )
  }
}

ReactDOM.render((
  <Router history={browserHistory}>
    <Route path="/" component={App}>
      {/* 当Url为/ 时渲染Dashboard */}
      <IndexRoute component={Dashboard}/>
      <Route path="about" component={About} />
      <Route path="inbox" component={Inbox}>
        {/* 添加一个路由，嵌套进我们想要嵌套的UI 里 */}
        <Route path="messages/:id" component={Message} />
      </Route>
    </Route>
  </Router>
), document.getElementById('app')
```
2. 现在，`App` 的`render` 中的 `this.props.children` 将会是`<Dashboard>`这个元素。上述功能是在当请求的URL 匹配某个目录时，允许你指定一个类似`index.html`的人口文件

URL | 组件
---|---|---
/ | App -> Dashboard
/about | App -> About
/inbox | App -> Inbox
/inbox/message/:id | App -> Inbox -> Message

#### 让UI 从URL 中解耦出来

1. 如果我们可以将`/inbox` 从`/inbox/messages/:id`中去除，并且还能够让 `Message`嵌套在 `App->Inbox` 中渲染，那会非常赞。绝对路径可以让我们做到这一点。
2. 在多从嵌套路由中使用绝对路径的能力让我们对URL 拥有绝对的掌控。我们无需在URL 中添加更多的层级，从而可以使用更简洁的URL。

URL | 组件
---|---|---
/ | App -> Dashboard
/about | App -> About
/inbox | App -> Inbox
/message/:id | App -> Inbox -> Message
3. 提醒：绝对路径可能在动态路由中无法使用

#### 兼容旧的URL

1. 在上面，我们改变了一个URL。现在仍何人访问`/inbox/messages/5` 都会啊看一个错误。我们可以使用`<Redirect>` 是这个URL 重新正常工作。
```jsx
import { Router, Route, Link, browserHistory, IndexRoute, Redirect } from 'react-router';

ReactDOM.render((
  <Router history={browserHistory}>
    <Route path="/" component={App}>
      {/* 当Url为/ 时渲染Dashboard */}
      <IndexRoute component={Dashboard}/>
      <Route path="about" component={About} />
      <Route path="inbox" component={Inbox}>
        {/* 使用/messages/:id 替换 messages/:id */}
        <Route path="/messages/:id" component={Message} />

        {/* 跳转 messages/:id 到 /messages/:id */}
        <Redirect from="messages/:id" to="/messages/:id"/>
      </Route>
    </Route>
  </Router>
), document.getElementById('app')
)
```
2. 现在当有人点击`/inbox/messages/5` 这个链接，他们会自动跳转到 `/messages/5`。

#### 进入和离开的Hook

1. Route 可以定义`onEnter`和 `onLeave` 两个hook，这些hook 会在页面跳转确认时触发一次。这些hook 对于一些情况非常有用，例如权限验证或者在路由跳转前将一些数据持久化保存起来。
2. 在路由跳转过程中，`onLeave hook` 会在所有将离开的路由中触发，从最下层的子路由开始直到最外层父路由结束。然后`onEnter hook`会从最外层的父路由开始直到最下层子路由结束。
3. 继续我们上面的例子，如果一个用户点击链接，从`/messages/5` 跳转到`/about` ，下面这些hook 的执行顺序
	1. `/messages/:id` 的`onLeave`
	2. `/inbox` 的`onLeave`
	3. `/about` 的`onEnter`

#### 替换的配置方式

1. 因为route 一般被嵌套使用，所以使用JSX 这种天然具有简介嵌套型语法的结构来描述它们的关系非常方便。然后，如果你不想使用JSX ，也可以直接使用原生route 数组对象。
```jsx
const routeConfig = [
  {
    path: '/',
    component: App,
    indexRoute: { component: Dashboard },
    childRoutes: [
      { path: 'about', component: About },
      { path: 'inbox',
        component: Inbox,
        childRoutes: [
          { path: 'messages/:id',
            onEnter: ({ params }, replace)=>replace(`/messages/${params.id}`)
          }
        ]
      },
      {
        component: Inbox,
        childRoutes: [
          { path: 'messages/:id', component: Message}
        ]
      }
    ]
  }
]

ReactDOM.render(
  <Router
    history={browserHistory}
    routes={routeConfig} />,
  document.getElementById('app')
)
```

### 路由配置原理

1. 路由拥有三个属性来决定是否“匹配”一个URL：
	1. **嵌套关系** 和
	2. 它的 **路径语法**
	3. 它的 **优先级**

#### 嵌套关系

1. React Router 使用路由嵌套的概念让你定义view 的嵌套集合，当一个给定的URL 被调用时，整个集合中（命中的部分）都会被渲染。嵌套路由被描述成一种树形结构。React Router 会深度优先遍历整个路由配置来寻找一个与给定的URL 相匹配的路由。

#### 路径语法

1. 路由路径是匹配一个（或一部分）URL 的一个字符串模式。大部分的路由路径都可以直接按照字面量理解，除了以下结构特殊的符号：
	1. `:paramName`- 匹配一段位于`\`、`?`或者`#`之后的URL。命中的部分将被作为一个参数
	2. `()` - 在它内部的内容被认为是可选的
	3. `*` - 匹配任意字符（非贪婪模式）直到命中下一个字符或者整个URL 的末尾，并创建一个`splat`参数
```jsx
<Route path="/hello/:name" >  // 匹配 /hello/michael 和 /hello/ryan
<Route path="/hello(/:name)" > // 匹配 /hello, /hello/michael 和 /hello/ryan
<Route path="/files/*.*" > // 匹配 /files/hello.jpg 和 /files/path/to/hello.jpg
```

#### 优先级

1. 最后，路由算法会根据定义的顺序自顶向下匹配路由。因此，当你拥有两个兄弟路由节点配置时，你必须确认前一个路由不会匹配后一个路由中的路径。例如，千万不要这么做：
```jsx
<Route path="/comments" .../>
<Redirect from="/comments" .../>
```

### History

1. React Router 是建立在history 之上的。简而言之，一个history知道如何去监听浏览器地址栏的变化，并解析这个URL 转化为`location` 对象，然后router 使用它匹配到路由，最后正确地渲染对应的组件。
2. 常用的history 有三种形式，但是你也可以使用React Router 实现自定义的history。
	1. `historyHistory`
	2. `hashHistory`
	3. `createMemoryHistory`

#### browserHistory

1. Browser history 是使用React Router 的应用推荐的history。它使用浏览器中的History API 用于处理URL，创建一个像`example.com/some/path` 这样真实的URL

##### 服务器配置

1. 服务器需要做好处理URL 的准备。处理应用启动最初的`/` 这样的请求应该没问题，但当用户来回跳转并在`/account/123` 刷新时，服务器就会收到来自`/account/123`的请求，这时你需要处理这个URL 并在响应中包含JavaScript 应用代码。

#### hashHistory

1. Hash histor 使用URL 中的hash（#）部分去创建形如`example.com/#/some/path` 的路由。
2. 我应该使用createHashHistory 吗？
  1. Hash History 不行也要服务器任何配置就可以运行，如果你刚刚入门，那就使用它吧。但是我们不推荐在实际线上环境中用它，因为每一个web 应用都应该渴望使用`browserHistory`

#### createMemoryHistory

1. Memory history 不会在地址栏被操作或读取。这就解释了我们是如何实现服务器渲染的。同时它也非常适合测试和其他的渲染环境（像React Native）
2. 和另外两种history 的一点不同是你必须创建它，这种方式便于测试。
```jsx
cosnt history = createMemoryHistory(location)
```

### 默认路由（IndexRoute）与IndexLink
#### 默认路由（IndexRoute）

```jsx
<Router>
  <Route path="/" component={App} >
    <IndexRoute component={Home} />
    <Route path="accouts" component={Accounts} />
    <Route path="statements" component={Statements} />
  </Route>
</Router>
```

#### Index Links

1. 如果你在这个app 中使用`<Link to="/">Home</Link>` ，它会一直处于激活状态，因为所有的URL 的开头都是`/` 。这确实是一个问题，因为我们仅仅希望在`Home` 被渲染后，激活并链接到它。
2. 如果需要在`Home` 路由被渲染后才激活的指向`/` 的链接，请使用`<IndexLink to="/">Home</IndexLink>`

## 高级用法
### 动态路由

1. React Route 即适用与小型网站也适用于大型网站
2. 对于大型应用来说，一个首当其冲的问题就是所需加载的JavaScript 的大小。程序应当只加载当前渲染页所需的JavaScript。有些开发者将这种方式称之为“代码分析”-- 将所有的代码分拆成多个小包，在用户浏览过程中按需加载。
3. 对于底层细节的修改不应该需要它上面每一层都进行修改。举个例子，为一个照片浏览页添加一个路径不应该影响到首页加载的JavaScript 的大小。也不能因为多个团队共用一个大型的路由配置文件而造成合并时冲突。
4. 路由是个非常适于做代码分拆的地方：它的责任就是配置好每个view
5. React Router 里的路径匹配以及组件加载都是异步完成的，不仅允许你延迟加载组件，并且可以延迟加载路由配置。在首次加载包中你只需要有一个路径定义，路由会自动解析剩下的路径。
6. Route 可以定义`getChildRoutes`，`getIndexRoute`和`getComponents` 这几个函数。它们都是异步执行，并且只有在需要时才被调用。我们将这种方式称之为“逐渐匹配”。React Router 会逐渐的匹配URL 并只加载该URL 对应页面所需的路径配置和组件。

### 跳转前确认

1. React Router 提供一个`routerWillLeave 生命周期钩子`，这使得React 组件可以拦截正在发生的跳转，或在离开`route`前提示用户。`routerWillLeave` 返回值有一下两种：
	1. `return false` 取消此次跳转
	2. `return` 返回提示信息，在离开route 前提示用户进行确认

### 服务端渲染

1. 服务端渲染与客户端渲染会有些许不同，因为你需要：
	1. 发生错误时发送一个`500` 的响应
	2. 需要重定向时发送一个`30x` 的响应
	3. 在渲染之前获得数据（用router 帮你完成这点）
2. 为了迎合这一需求，你要在`<Router>` API 下一层使用：
	1. 使用`match` 在渲染之前根据location 匹配route
	2. 使用`RoutingContext` 同步渲染route 组件
3. 它看起像一个虚拟机的JavaScript 服务器
```jsx
import { renderToString } from 'react-dom/server'
import { match, RoutingContext } from 'react-router'
import routes from './routes'

serve((req, res) => {
  // 注意！ 这里的req.url 应该是从初始请求中获得的
  // 完整的URL 路径，包括查询字符串
  match({ routes, location: req.url }, (error, redirectLocation, renderProps) => {
    if(error){
      res.send(500, error.message)
    }else if(redirectLocation){
      res.redirect(302, redirectLocation.pathname + redirectLocation.search)
    }else if(renderProps) {
      res.send(200, renderToString(<RoutingContext{...renderProps}/>))
    }else {
      res.send(404, 'Not found')
    }
  })
})
```
4. 至于加载数据，你可以用`renderProps` 去构建任何你想要的形式 -- 例如在route 组件中添加一个静态`load` 方法，或如在`route` 中添加数据加载的方法。

### 组件生命周期

1. 在开发应用时，理解路由组件的声明周期是非常重要的。后面我们会以获取数据这个最常见的场景为例，介绍一下路由改变时，路由组件声明周期的变化情况。
2. 路由组件的声明周期和React 组件相比并没有什么不同。所以让我们先忽略路由部分，只考虑在不同URL 下，这些组件是如何被渲染的。
3. 路由配置如下：
```jsx
<Route path="/" conmponent={App}>
  <IndexRoute component={Home} />
  <Route path="invoices/:invoiceId" component={Invoice} />
  <Route path="accounts/:accountId" component={Account} />
</Route>
```

#### 路由切换时，组件生命周期的变化情况

1. 当用户打开应用的'/' 页面

组件 | 生命周期
---|---|---
App | componentDidMount
Home | componentDidMount
Invoice | N/A
Account | N/A

2. 当用户从`/` 跳转到`/invoice/123`

组件 | 生命周期
---|---|---
App | componentWillReceiveProps, componentDidUpdate
Home | componentWillUnmount
Invoice | componentDidmount
Account | N/A


	* `App` 从router 中接到新的`props` （例如`children`、`params`、`location`等数据 ）所以`App`触发了`componentWillReceiveProps`和`componentDidUpdate`两个生命周期
	* `Home`不再被渲染，所以它将被移除
	* `Invoice`首次被挂载

3. 当用户从`/invoice/123`跳转到`/invoice/789`

组件 | 生命周期
---|---|---
App | componentWillReceiveProps, componentDidUpdate
Home | N/A
Invoice | componentWillReceiveProps, componentDidUpdate
Account | N/A

  所有的组件之前都已经被挂载，所以只是从`router`更新了`props`
4. 当从`/invoice/789`跳转到`/accounts/123`

组件 | 生命周期
---|---|---
App | componentWillReceiveProps, componentDidUpdate
Home | N/A
Invoice | componentWillUnmount
Account | componentDidMount

#### 获取数据

1. 虽然还有其他通过router 获取数据的方法，但是最简单的方法是通过组件生命周期Hook 来实现。前面我们已经理解了当路由改变时组件声明周期的变化，我们可以在`Invoice` 组件里实现一个简单的数据获取功能。
```jsx
class Invoice extends Component {
  constructor(props){
    super(props)
    this.state = {
      invoice: null
    }
  }

  componentDidMount(){
    // 上面的步骤2，在此进行初始化数据
    this.fetchInvoice()
  }

  componentDidUpdate(preProps){
    // 上面的步骤3，通过参数更新数据
    let oldId = preProps.params.invoiceId
    let newId = this.props.params.invoiceId
    if(newId !== oldId){
      this.fetchInvoice()
    }
  }

  componentWillUnmount(){
    // 上面的步骤4，在组件移除前忽略掉正在进行中的请求
    this.ignoreLastFetch = true
  }

  fetchInvoice(){
    let url = `/api/invoices/${this.props.params.invoiceId}`
    this.request = fetch(url, (err, data)=>{
      if(!this.ignoreLastFetch){
        this.setState({ invoice: data.invoice })
      }
    })
  }

  render(){
    return <InvoiceView invoice={this.state.invoice} />
  }
}
```

### 组件外部跳转

1. 虽然在组件内部可以使用`this.context.router` 来实现导航，但许多应用想要在组件外部使用导航。使用Router 组件上被赋予的history 可以在组件外部实现导航。

## 参考文档

1. [react router 中文](https://react-guide.github.io/react-router-cn/)
2. [react router 官网](https://github.com/ReactTraining/react-router/blob/master/docs/guides/RouteConfiguration.md )
