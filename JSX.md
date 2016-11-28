# JSX

React认为一个组件可以是一个完全独立的没有任何其他依赖的模快文件。一个组件中可以有自己的样式（Inline Style）和结构（JSX编写的HTML）

```
<!DOCTYPE html>
<html>
  <head>
    <script src="../build/react.js"></script>
    <script src="../build/react-dom.js"></script>
    <script src="../build/browser.min.js"></script>
  </head>
  <body>
    <div id="example"></div>
    <script type="text/babel">
      // ** Our code goes here! **
    </script>
  </body>
</html>

<script> 标签的 type 属性为 text/babel,凡是使用 JSX 的地方，都要加上 type="text/babel";
react.js是React的核心库
react-dom.js是提供与DOM相关的功能
browser.js的作用是将JSX语法转为JavaScript语法，这一步比较耗时，实际上线时应将其放到服务器完成。
```


** JSX使用大小写来区分本地组件的类和HTML标签 **
创建一个HTML标签，首字母小写；创建一个Component，首字母大写。

## 独立文件
React JSX 代码可以放在一个独立文件上
```
<script type="text/babel" src="helloworld_react.js"></script>
//在引入的时候将type设置成"text/babel"即可
```

## JavaScript表达式
我们可以在JSX中使用JavaScript表达式。表达式写在花括号{}中。

## 样式
React 推荐使用内联样式。我们可以使用 camelCase 语法来设置内联样式. React 会在指定元素数字后自动添加 px 。以下实例演示了为 h1 元素添加 myStyle 内联样式：
```
var myStyle = {
	fontSize: 100,
	color: '#FF0000'
};
ReactDOM.render(
	<h1 style = {myStyle}>菜鸟教程</h1>,
	document.getElementById('example')
);
```

## 注释
注释需要写在花括号中
```
ReactDOM.render(
	<div>
    <h1>菜鸟教程</h1>
    {/*注释...*/}
 	</div>,
	document.getElementById('example')
);
```

## 数组
JSX允许在模板中插入数组，数组会自动展开所有成员：
```
var arr = [
  <h1>菜鸟教程</h1>,
  <h2>学的不仅是技术，更是梦想！</h2>,
];
ReactDOM.render(
  <div>{arr}</div>,
  document.getElementById('example')
);
```

##



JSX允许HTML与JavaScript的混写。
JSX的基本语法规则：
遇到HTML标签(以`<`开头) ，就用HTML规则解析；
遇到代码块(以`{`开头)，就用JavaScript规则解析。