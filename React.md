## React 入门实例教程

* <script> 标签的 type 属性为 text/babel 。这是因为 React 独有的 JSX 语法，跟 JavaScript 不兼容。凡是使用 JSX 的地方，都要加上 type="text/babel"

* react.js 、react-dom.js 和 Browser.js ，它们必须首先加载。react.js 是 React 的核心库，react-dom.js 是提供与 DOM 相关的功能，Browser.js 的作用是将 JSX 语法转为 JavaScript 语法，这一步很消耗时间，实际上线的时候，应该将它放到服务器完成。

* HTML 语言直接写在 JavaScript 语言之中，不加任何引号，这就是 JSX 的语法，它允许HTML与Javascript混写

* JSX的基本语法规则：遇到HTML标签（以<开头），就用HTML规则解析；遇到代码块（以{开头），就用javascript规则解析

* JSX允许直接在模板插入javascript变量，如果这个变量是一个数组，则会展开这个数组的所有成员

* React允许将代码封装成组件（Component）,然后像插入普通HTML标签一样，在网页中插入这个组件。React.createClass方法用于生成一个组件类。所有组件类都必须有自己的render方法，用于输出组件。

  ```
  组件实例：
  var MyComponent = React.createClass({
  	render:function(){
    		return <h1>Hello,world</h1>;
  	}
  });
  变量MyComponent就是一个组件类。模版插入<MyComponent />时，会自动生成一个MyComponent的实例
  ```

* 组件类的第一个字母必须大写，且组件类只能包含一个顶层标签。

* 组件的用法与原生的HTML标签完全一致，可以任意加入属性。组件的属性可以在组件类的this.props对象上获取，比如：

  ```
  var MyComponent = React.createClass({
   	render:function(){
     		 return <h1>Hello,{this.props.name}</h1>;
   	}
  });
  ReactDOM.render(<MyComponent name="John" />,document.getElementById('root'));
  最终渲染出来的是:<h1>Hello,John</h1>
  ```

* this.props 对象的属性与组件的属性一一对应，但是有一个例外，就是 this.props.children 属性。它表示组件的所有子节点

* 需要注意的是： this.props.children 的值有三种可能：如果当前组件没有子节点，它就是 undefined ;如果有一个子节点，数据类型是 object ；如果有多个子节点，数据类型就是 array 。所以，处理 his.props.children 的时候要小心。

  React 提供一个工具方法 React.Children 来处理 this.props.children 。我们可以用 React.Children.map 来遍历子节点，而不用担心 this.props.children 的数据类型。

  ```
  var NotesList = React.createClass({
    render: function() {
      return (
        <ol>
        {
          React.Children.map(this.props.children, function (child) {
            return <li>{child}</li>;
          })
        }
        </ol>
      );
    }
  });

  ReactDOM.render(
    <NotesList>
      <span>hello</span>
      <span>world</span>
    </NotesList>,
    document.body
  );
  ```

* 组件的属性可以接受任意值，字符串、对象、函数等都可以。有时，我们需要一种机制，验证别人使用组件时，提供的参数是否符合要求。

  组件类的PropTypes属性，就是用来验证组件实例的属性是否符合要求的。

  ```
  例：
  var MyTitle = React.createClass({
     propTypes: {
      title: React.PropTypes.string.isRequired,
     },
     render: ...
  });
  上面的Mytitle组件有一个title属性。PropTypes 告诉 React，这个 title 属性是必须的，而且它的值必须是字符串，如果不符合要求，初始化的时候就会报错。
  ```

* 组件的getDefaultProps方法可以用来设置组件属性的默认值

  ```
  var MyTitle = React.createClass({
   	getDefaultProps: function(){
  		return {
  			title:'Hello,World'
  		};
  	}
  });
  ```

* 组件并不是真实的DOM节点，而是存在于内存之中的一种数据结构，叫做虚拟DOM。只有当它插入文档以后，才会变成真实的DOM。根据React的设计，所有的DOM变动，都先在虚拟DOM上发生，然后再实际发生变动的部分，反映在真实DOM上，这种算法叫做DOM diff。

* 有时候我们需要从组件获取真实DOM节点，这时就需要用到ref属性

  ```
  var MyComponent = React.createClass({
    handleClick: function() {
      this.refs.myTextInput.focus();
    },
    render: function() {
      return (
        <div>
          <input type="text" ref="myTextInput" />
          <input type="button" value="Focus the text input" onClick={this.handleClick} />
        </div>
      );
    }
  });

  ReactDOM.render(
    <MyComponent />,
    document.getElementById('example')
  );
  ```

  上面代码中，组件 `MyComponent` 的子节点有一个文本输入框，用于获取用户的输入。这时就必须获取真实的 DOM 节点，虚拟 DOM 是拿不到用户输入的。为了做到这一点，文本输入框必须有一个 `ref` 属性，然后 `this.refs.[refName]` 就会返回这个真实的 DOM 节点。

  需要注意的是，由于 `this.refs.[refName]` 属性获取的是真实 DOM ，所以必须等到虚拟 DOM 插入文档以后，才能使用这个属性，否则会报错。上面代码中，通过为组件指定 `Click` 事件的回调函数，确保了只有等到真实 DOM 发生 `Click` 事件之后，才会读取 `this.refs.[refName]` 属性。

* 组件免不了要与用户互动，React 的一大创新，就是将组件看成是一个状态机，一开始有一个初始状态，然后用户互动，导致状态变化，从而触发重新渲染 UI

  ```
  var LikeButton = React.createClass({
    getInitialState: function() {
      return {liked: false};
    },
    handleClick: function(event) {
      this.setState({liked: !this.state.liked});
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
    document.getElementById('example')
  );

  上面代码是一个 LikeButton 组件，它的 getInitialState 方法用于定义初始状态，也就是一个对象，这个对象可以通过 this.state 属性读取。当用户点击组件，导致状态变化，this.setState 方法就修改状态值，每次修改以后，自动调用 this.render 方法，再次渲染组件
  ```

* 用户在表单填入的内容，属于用户跟组件的互动，所以不能用 this.props

  ```
  var Input = React.createClass({
    getInitialState: function() {
      return {value: 'Hello!'};
    },
    handleChange: function(event) {
      this.setState({value: event.target.value});
    },
    render: function () {
      var value = this.state.value;
      return (
        <div>
          <input type="text" value={value} onChange={this.handleChange} />
          <p>{value}</p >
        </div>
      );
    }
  });
  ReactDOM.render(<Input/>, document.body);

  上面代码中，文本输入框的值，不能用 this.props.value 读取，而要定义一个 onChange 事件的回调函数，通过 event.target.value 读取用户输入的值。textarea 元素、select元素、radio元素都属于这种情况
  ```

* 组件的生命周期

  ```
  组件的生命周期分成三个状态：
    Mounting:已插入真实DOM
    Updating:正在被重新渲染
    Unmounting:已移出真实DOM

  React 为每个状态都提供了两种处理函数，will 函数在进入状态之前调用，did 函数在进入状态之后调用：
    componentWillMount()
    componentDidMount()
    componentWillUpdate(object nextProps, object nextState)
    componentDidUpdate(object prevProps, object prevState)
    componentWillUnmount()
  ```

* 组件的style属性的设置方式，不能写成style="opacity:{this.state.opacity};"而要写成 style={{ opacity: this.state.opacity }}，因为React组件样式是一个对象，所以第一重大括号表示这是JavaScript语法，第二重大括号表示样式对象。

* 组件的数据来源，通常是通过 Ajax 请求从服务器获取，可以使用 componentDidMount 方法设置 Ajax 请求，等到请求成功，再用 this.setState 方法重新渲染 UI













