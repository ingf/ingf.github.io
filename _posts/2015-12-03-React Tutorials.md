---
layout: post
title: "React Tutorials"
description: "React Tutorials"
category: doc
tags: [React]
---

这是我在公司内网写的文章，现在也调到博客上面来，有些地址是内网地址，不能访问哦，但是都给提供了可替代的实现。

### 特性
- 用 JSX 语法取代 HTML 模板，在 JavaScript 里声明式地描述 UI 
- 虚拟 DOM 取代物理 DOM 作为操作对象，封装了 DOM 的事件系统
- 单向数据流动
- 组件和基于组件的设计流程

### ReactDOM.render()
ReactDOM.render 是 React 的最基本方法，用于将模板转换成 HTML 语言，并插入指定的 DOM 节点。代码可以在 [pepper](http://gitlab.intra.wepiao.com/FEI/pepper/tree/master) 里面执行，也可以插入 [jsfiddle](http://jsfiddle.net/ingf/69z2wepo/21967/) 执行。

    ReactDOM.render(
        <h1>Greeting, world!</h1>,
        document.getElementById('app')
    );

运行效果是酱紫：

    <div id="app">
        <h1 data-reactid=".0">Greeting, world!</h1>
    </div>

### JSX 语法
上一节的代码， HTML 语言直接写在 JavaScript 语言之中，不加任何引号，这就是 JSX 的语法，它允许 HTML 与 JavaScript 的混写。

    var movies = ['007：幽灵党','饥饿游戏3','我的少女时代'];

    ReactDOM.render(
        <div>
        {
            movies.map(function (movie) {
                return <div>{movie}</div>
            })
        }
        </div>,
        document.getElementById('app')
    );
上面代码体现了 JSX 的基本语法规则：遇到 HTML 标签（以 &lt; 开头），就用 HTML 规则解析；遇到代码块（以 { 开头），就用 JavaScript 规则解析。上面代码的运行结果如下。

    <div id="app">
        <div data-reactid=".0">
            <div data-reactid=".0.0">007：幽灵党</div>
            <div data-reactid=".0.1">饥饿游戏3</div>
            <div data-reactid=".0.2">我的少女时代</div>
        </div>
    </div>

### 组件
React 允许将代码封装成组件（component），然后像插入普通 HTML 标签一样，在网页中插入这个组件。React.createClass 方法就用于生成一个组件类。

    var Details = React.createClass({
        render: function() {
            return <div>Details For {this.props.movieId}</div>
        }
    });
    var id = 8888;

    ReactDOM.render(
        <Details movieId={id}/>,
        document.getElementById('app')
    );

上面代码中，我们把电影详情封装成一个组件 Details，然后就想插入普通 HTML 标签一样，在网页中插入这个组件。所有的组件都必须实现 render 方法，该方法返回一颗 React 组件树，这棵树最终将会渲染成 HTML，render 方法是一个 pure function，只负责纯粹的渲染逻辑，不涉及具体的业务逻辑和其他其他事情，这样可以使服务器端渲染更加切实可行，也使组件更容易被理解。

组件的用法和 HTML 标签完全一致，可以加入任意属性，比如我们给 Details 组件加入一个 movieId 属性，他的值为 id，这个值可以在组件内从 this.props 对象上面读取，上面的代码运行效果如下：

    <div id="app">
        <div data-reactid=".0">
            <span data-reactid=".0.0">Details For </span>
            <span data-reactid=".0.1">8888</span>
        </div>
    </div>
添加组件属性，有一个地方需要注意，就是 class 属性需要写成 className ，for 属性需要写成 htmlFor ，这是因为 class 和 for 是 JavaScript 的保留字。

### 获取真实的DOM节点
组件并不是真实的 DOM 节点，而是存在于内存之中的一种数据结构，叫做虚拟 DOM （virtual DOM）。只有当它插入文档以后，才会变成真实的 DOM 。根据 React 的设计，所有的 DOM 变动，都先在虚拟 DOM 上发生，然后再将实际发生变动的部分，反映在真实 DOM上，这种算法叫做 DOM diff ，它可以极大提高网页的性能表现。
但是，有时需要从组件获取真实 DOM 的节点，这时就要用到 ref 属性。

    var Greeting = React.createClass({
        handleClick: function() {
            alert('Greeting, ' + this.refs.username.value);
        },
        render: function() {
            return (
                <div>
                    <input type="text" ref="username" />
                    <input type="button" value="submit" onClick={this.handleClick} />
                </div>
            );
        }
    });

    ReactDOM.render(
        <Greeting />,
        document.getElementById('app')
    );
上面代码中，组件 Greeting 的子节点有一个文本输入框，用于获取用户的输入。这时就必须获取真实的 DOM 节点，虚拟 DOM 是拿不到用户输入的。为了做到这一点，文本输入框必须有一个 ref 属性，然后 this.refs.[refName] 就会返回这个真实的 DOM 节点。
需要注意的是，由于 this.refs.[refName] 属性获取的是真实 DOM ，所以必须等到虚拟 DOM 插入文档以后，才能使用这个属性，否则会报错。

上面代码中，通过为组件指定 Click 事件的回调函数，确保了只有等到真实 DOM 发生 Click 事件之后，才会读取 this.refs.[refName] 属性。React 组件支持很多事件，除了 Click 事件以外，还有 KeyDown 、Copy、Scroll、Change、Input 等，完整的事件清单请查看[官方文档](http://facebook.github.io/react/docs/events.html#supported-events)。

React 里只需把事件处理器（event handler）以骆峰命名（camelCased）形式当作组件的 props 传入即可，就像使用普通 HTML 那样。React 内部创建一套合成事件系统来使所有事件在 IE8 和以上浏览器表现一致。也就是说，React 知道如何冒泡和捕获事件，而且你的事件处理器接收到的 events 参数与 [W3C](http://www.w3.org/TR/DOM-Level-3-Events/) 规范一致，无论你使用哪种浏览器。
其实，当 React 启动的时候，它在最外层使用唯一一个事件监听器处理所有事件。当组件被加载和卸载时，只是在内部映射里添加或删除事件处理器。当事件触发，React 根据映射来决定如何分发。

上面这种绑定事件的方式是不是很熟悉，因为上世纪90年代就是这么做的：

    <input type="button" value="submit" onclick="alert('你点了我')" />

顺便我们在看一下 jQuery 时代的做法：

    <input type="button" value="submit" id="foo">foo</input>

    $('#foo').click(function(e) {alert('你点了我')});

我们在看看 React 的实现：

    <input type="button" value="submit" onClick={this.handleClick} />

是不是很简单，返璞归真！

### this.state
React 把用户界面当作简单状态机。把用户界面想像成拥有不同状态然后渲染这些状态，可以轻松让用户界面和数据保持一致。
React 里，只需调用 setState()来更新组件的 state，然后 React 根据新的 state 重新渲染用户界面（不要操作 DOM）。React 来决定如何最高效地更新 DOM。

    var LikeButton = React.createClass({
        getInitialState: function() {
            return {
                liked: false
            };
        },
        handleClick: function(event) {
            this.setState({
                liked: !this.state.liked
            });
        },
        render: function() {
            var text = this.state.liked ? 'like' : 'haven\'t liked';
            return (
                <p onClick={this.handleClick}>
                    You {text} this. Click to toggle.
                </p>
            );
        }
    });

    ReactDOM.render(
        <LikeButton />,
        document.getElementById('app')
    );

上面代码是一个 LikeButton 组件，它的 getInitialState 方法用于定义初始状态，也就是一个对象，这个对象可以通过 this.state 属性读取。当用户点击组件，导致状态变化，this.setState 方法就修改状态值，每次修改以后，自动调用 this.render 方法，再次渲染组件。
·
由于 this.props 和 this.state 都用于描述组件的特性，可能会产生混淆。一个简单的区分方法是，this.props 表示那些一旦定义，就不再改变的特性，而 this.state 是会随着用户互动而产生变化的特性。

### 组件的详细说明和生命周期
组件的生命周期分成三个状态：

- Mounting：已插入真实 DOM
- Updating：正在被重新渲染
- Unmounting：已移出真实 DOM

React 为每个状态都提供了两种处理函数，will 函数在进入状态之前调用，did 函数在进入状态之后调用，三种状态共计五种处理函数。

- componentWillMount()
- componentDidMount()
- componentWillUpdate(object nextProps, object nextState)
- componentDidUpdate(object prevProps, object prevState)
- componentWillUnmount()

此外，React 还提供两种特殊状态的处理函数。

- componentWillReceiveProps(object nextProps)：已加载组件收到新的参数时调用
- shouldComponentUpdate(object nextProps, object nextState)：组件判断是否重新渲染时调用

![lifecycle](/img/lifecycle.png)

图中的方法几乎已经包括了 React 的所有 API，自定义组件时根据需要在组件生命周期的不同阶段实现不同的逻辑。除了必须实现 render 方法之外，其它常用的方法包括：

- getDefaultProps: 在组件挂载之前调用一次。返回值将会作为 this.props 的初始值。
- getInitialState: 在组件挂载之前调用一次。返回值将会作为 this.state 的初始值。
- componentDidMount: 在组件第一次render之后调用，这时组件对应的DOM节点已被加入到浏览器。在这个方法里可以去实现一些初始化逻辑。
- componentWillUnmount: 在DOM节点移除之后被调用，这里可以做一些相关的清理工作。

### 组件化的开发思路
虚拟 DOM 不仅带来了简单的 UI 开发逻辑，同时也带来了组件化开发的思想，所谓组件，即封装起来的具有独立功能的 UI 部件。React 推荐以组件的方式去重新思考 UI 构成，将 UI 上每一个功能相对独立的模块定义成组件，然后将小的组件通过组合或者嵌套的方式构成大的组件，最终完成整体 UI 的构建。例如，Facebook 的 [Instagram](http://instagram.com/) 整站都采用了 React 来开发，整个页面就是一个大的组件，其中包含了嵌套的大量其它组件，大家有兴趣可以看下它背后的代码。

如果说 MVC 的思想让你做到视图-数据-控制器的分离，那么组件化的思考方式则是带来了 UI 功能模块之间的分离。

对于 MVC 开发模式来说，开发者将三者定义成不同的类，实现了表现，数据，控制的分离，开发者更多的是从技术的角度来对 UI 进行拆分，实现松耦合。对于 React 而言，则完全是一个新的思路，开发者从功能的角度出发，将 UI 分成不同的组件，每个组件都独立封装。组件的封装方式和单向数据流动能够极大地简化前端架构的理解难度。下面我们通过组件化的思路来构建我们的电影详情页（为了 DEMO，请原谅我把产品原型修改的这么丑）。

![detail](/img/detail.png)

在 React 中，你按照界面模块自然划分的方式来组织和编写你的代码，对于详情界面而言，整个 UI 是一个通过小组件构成的大组件，每个组件只关心自己部分的逻辑，彼此独立。所以界面组织架构如下：

- Details
    - Header
    - Story
    - Poster
    - CommentBox
        - CommentList
        - CommentForm

这样最外层的 Details 界面和 CommentBox 的 Render 只需要如下代码：

    var Details = React.createClass({
        render: function() {
            return (
                <div>
                    <Header />
                    <Story />
                    <Poster />
                    <CommentBox />
                </div>
            )
        }
    });

    var CommentBox = React.createClass({
        render: function() {
            return (
                <div>
                    评论
                    <CommentList />
                    <CommentForm />
                </div>
            )
        }
    });

具体到每个组件，Header 和 Story 都比较简单，只是呈现数据。

    var Header = React.createClass({
        getDefaultProps: function() {
            return {
                data: {}
            };
        },
        render: function() {
            return (
                <header>
                    <h1>{this.props.data.name}</h1>
                    <div>导演：{this.props.data.director}</div>
                </header>
            )
        }
    });
    
    var Story = React.createClass({
        getDefaultProps: function() {
            return {
                data: ''
            };
        },
        render: function() {
            return (
                <div>{this.props.data}</div>
            )
        }
    });

Poster 稍微复杂一点，我们使用 iScroll 插件实现。

    var Poster = React.createClass({
        getDefaultProps: function() {
            return {
                data: []
            };
        },
        render: function() {
            return <div scrollX={true} className={styles.slider}>
                {
                    this.props.data.map(function(item, index){
                        return <a href='javascript:void(0)' key={index}>
                            <img src={item.display_url}/>
                        </a>
                    })
                } 
            </div>
        }
    });

评论列表和评论框也会复杂一点。

    var CommentList = React.createClass({
        render: function() {
            return (
                <div className={styles['comment-list']}>
                    {this.props.data.map(function(item, i) {
                        return (
                            <div key={i}>
                                {item}
                            </div>
                        )
                    })}
                </div>
            )
        }
    });

    var CommentForm = React.createClass({
        onClick: function(e) {
            e.preventDefault();
            var comment = this.refs.commentInput.value;
            this.props.onSubmit(comment);
            this.refs.commentInput.value = '';
        },
        render: function() {
            return (
                <div>
                    <div className={styles['input-box']}>        
                        <textarea ref="commentInput" rows="6" placeholder="简单说几句"></textarea>
                    </div>
                    <div className={styles['btn-box']}>
                        <a href="#" onClick={this.onClick}>提 交</a>   
                    </div>
                </div>
            )
        }
    });

完整的代码在[这里](http://gitlab.intra.wepiao.com/FEI/pepper/blob/tutorials/src/pages/details/index.jsx)。

通过这种方式，每个组件的UI和逻辑都定义在组件内部，和外部完全通过 API 来交互，通过组合的方式来实现复杂的功能。React 认为一个组件应该具有如下特征：

- 可组合（Composeable）：一个组件易于和其它组件一起使用，或者嵌套在另一个组件内部。如果一个组件内部创建了另一个组件，那么说父组件拥有（own）它创建的子组件，通过这个特性，一个复杂的 UI 可以拆分成多个简单的 UI 组件；
- 可重用（Reusable）：每个组件都是具有独立功能的，它可以被使用在多个 UI 场景；
- 可维护（Maintainable）：每个小的组件仅仅包含自身的逻辑，更容易被理解和维护；
- 可测试（Testable）：因为每个组件都是独立的，那么对于各个组件分别测试显然要比对于整个 UI 进行测试容易的多。

### 工具
[React Developer Tools](https://github.com/facebook/react-devtools)

### 最后
React 是一个全新思路的前端 UI 框架，它完全接管了 UI 开发中最为复杂的局部更新部分，擅长在在复杂场景下保证高性能；同时，它引入了基于组件的开发思想，从另一个角度来重新审视 UI 的构成。通过这种方法，不仅能够提高开发效率，而且可以让代码更容易理解，维护和测试。

### 将来
至此，React 的基本点都讲完了，但是社区还有大量的知识点需要学习，比如react-router, react-hot-reload, Redux 或者 Flux 的 N 种实现，CSS in JS 及其 N 种实现，Immutable-js，webpack...

虽然有点多，但是每项都可以学出来装逼用，而且目前大家不用担心，因为大部分我们都已经打包到 [pepper](http://gitlab.intra.wepiao.com/FEI/pepper/tree/master) 里面了。在后面的分享里面我们会逐步深入进去，欢迎大家关注这个项目，有任何问题都可以在 gitlab 里面提 issue，然后欢迎所有同学都可以一起来分享在 React 上的一些事情。
