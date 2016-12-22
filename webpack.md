#Webpack入门实践

##一些基本的概念
** Webpack是一种模块化的解决方案 **
webpack是以CommonJS的形式来书写脚本的。
能被Webpack模块化的不仅仅是JS，还有css以及图片等。

>WebPack可以看做是模块打包机：它做的事情是，分析你的项目结构，找到JavaScript模块以及其它的一些浏览器不能直接运行的拓展语言（Scss，TypeScript等），并将其打包为合适的格式以供浏览器使用。
***
>Webpack的工作方式是：把你的项目当做一个整体，通过一个给定的主文件（如：index.js），Webpack将从这个文件开始找到你的项目的所有依赖文件，使用loaders处理它们，最后打包为一个浏览器可识别的JavaScript文件。

```
$ webpack --config XXX.js   //使用另一份配置文件（比如XXX.js）来打包
$ webpack --watch   //监听变动并自动打包
$ webpack -p    //压缩混淆脚本，这个非常非常重要！
$ webpack -d    //生成map映射文件，告知哪些模块被最终打包到哪里了
```

###安装
```
npm install webpack -g	//全局安装
npm install webpack --save-dev	//安装到项目目录
```

### 生成Source Maps
在webpack的配置文件中配置source maps，需要配置devtool，它有以下四种不同的配置选项，各具优缺点，描述如下：
* source-map： 在一个单独的文件中产生一个完整且功能完全的文件。这个文件具有最好的source map，但是它会减慢打包文件的构建速度；
* cheap-module-source-map：在一个单独的文件中生成一个不带列映射的map，不带列映射提高项目构建速度，但是也使得浏览器开发者工具只能对应到具体的行，不能对应到具体的列（符号），会对调试造成不便；
* eval-source-map：使用eval打包源文件模块，在同一个文件中生成干净的完整的source map。这个选项可以在不影响构建速度的前提下生成完整的sourcemap，但是对打包后输出的JS文件的执行具有性能和安全的隐患。不过在开发阶段这是一个非常好的选项，但是在生产阶段一定不要用这个选项；
* cheap-module-eval-source-map：这是在打包文件时最快的生成source map的方法，生成的Source Map 会和打包后的JavaScript文件同行显示，没有列映射，和eval-source-map选项具有相似的缺点；

上述选项由上到下打包速度越来越快，不过同时也具有越来越多的负面作用，较快的构建速度的后果就是对打包后的文件的的执行有一定影响。
在学习阶段以及在小到中性的项目上，eval-source-map是一个很好的选项，不过记得只在开发阶段使用它



##从一个简单的DEMO开始
1. 安装webpack:`npm install webpack --save-dev`
2. 在项目根目录下新建`webpack.config.js`文件
3. 目录结构
![目录结构](http://i.imgur.com/WztPyfG.jpg)

index.html文件中引入最终要生成的js文件
```
//index.html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>Webpack</title>
</head>
<body>
	<div id="root"></div>
	<script src="../dist/js/bundle.js"></script>
</body>
</html>
```

index.js是项目的打包的入口文件,其中引入了testmodule模快
```
//index.js
var test = require('./testmodule.js');
test.print();
var node = document.querySelector('#root');
node.innerHTML = test.txt;
```

testmodule.js
```
//testmodule.js
module.exports={
	print:function(){
		console.log('there is testmodule print');
	},
	txt: 'hello,webpack'
}
```
webpack.config.js配置文件中的内容,就是简单的定义了入口文件和最终生成的文件。
```
module.exports={
	entry: __dirname + '/src/js/index.js',
	output:{
		path:__dirname + '/dist/js/',
		filename:'bundle.js'
	}
}
```
在命令行运行
```
webpack
```
/dist/js/目录下就生成了bundle.js文件了。

## 第2个DEMO：多个入口文件
此时webpack.config.js中的内容是：
```
module.exports={
	devtool: 'eval-source-map',
	entry: {
		index: './src/js/index',
		nav: './src/js/nav'
	},
	output:{
		path:'./dist/js/',
		filename:'[name].bundle.js'
	}
}
```
index.html和nav.html两个html分别包含了两个不同的js文件index.js以及nav.js，因此配置了两个入口，分别生成index.bundle.js以及nav.bundle.js两个js文件。
关于entry的配置：
```
1. entry的值是字符串，这个字符串对应的模块会在启动的时候加载
2. entry的值是数组，这个数组内所有模块会在启动的时候加载，数组的最后一个元素作为export
3. entry的值是对象，可以构建多个bundle
```

## 关于webpack-dev-server
[webpack-dev-server 使用方法，看完还不会的来找我~](http://www.tuicool.com/articles/BZrQ3mv)

##Loaders
通过使用不同的loader，webpack调用外部的脚本或工具可以对各种各样的格式的文件进行处理，比如说分析JSON文件并把它转换为JavaScript文件，或者说把下一代的JS文件(ES6/ES7)转换为现代浏览器可以识别的JS文件。对于React的开发而言，合适的loader可以把React的JSX文件转换为JS文件。

Loaders需要单独安装并且需要在Webpack.config.js下的modules关键字下进行配置，Loaders是一个数组，其中的每一个loader的配置选项包括以下几个方面：
* test：一个匹配loaders所处理的文件的扩展名的正则表达式(必须)
* loader：loader的名称(XXX-loader，-loader可省略)，多个loader之间用“!”连接起来。(必须)
* include/exclude：手动添加必须处理的文件(文件夹)或屏蔽不需要处理的文件(文件夹)(可选)；
* query：为loaders提供额外的设置选项(可选)

loader需要单独安装，一般的安装命令如下：
```
npm install xxx-loader --save-dev
```
然后在webpack.config.js中的引用：
```
以解析JSON为JavaScript对象的loader为例

//安装
npm install json-loader --save-dev

//在webpack.config.js中的相关配置
module.exports = {

	module:{
		loaders:[
			{
				test: /\.json$/,
				loader: 'json'		
			}
		]	
	}
}
```
经过上面这样配置之后，项目中所有以.json结尾的json文件加载时都会转换为JS对象。

##Webpack与Babel
### Babel的安装与配置
Babel其实是几个模块化的包，其核心功能位于称为babel-core的npm包中，不过webpack把它们整合在一起使用，但是对于每一个你需要的功能或拓展，你都需要安装单独的包（用得最多的是解析Es6的babel-preset-es2015包和解析JSX的babel-preset-react包）。

一次性安装这些依赖包
```
npm install --save-dev babel-core babel-loader babel-preset-es2015 babel-preset-react
```
在webpack.config.js中配置babel：
```
module.exports = {
	module:{
		loaders:[
			{
				test:/\.js$/,
				exclude:'/node_modules/',
				loader:'babel',
				query:{
					presets:['es2015','react']
				}
			}
		]	
	}
}
```
现在webpack的配置已经允许你使用ES6以及JSX的语法了.不过使用react还需要安装React和React-DOM

## DEMO：ES6模块化实践
```
//webpack.config.js
module.exports = {
	devtool:'eval-source-map',
	entry:__dirname + '/js/index.js',
	output:{
		path: __dirname + '/dist/',
		filename: 'index.bundle.js'
	},
	module:{
		loaders:[
			{
				test:/\.js$/,
				exclude:'/node_modules/',
				loader:'babel',
				query:{
					presets:['es2015']
				}
			}
		]
	}
}
```

```
//a.js
import B from './b'

class A extends B{
	constructor(){
		super();
		console.log('A init.');
	}

	sayHi(){
		console.log('A:hi');
	}
}

export default A;
```

```
//b.js
class B {
	constructor(){
		console.log('B init.');
	}

	printf(str){
		console.log('B.print:'+str);
	}

}

export default B;
```

```
//index.js
import A from './a'

let obj = new A();
obj.printf('test');
obj.sayHi();

//B init.
//A init.
//B.print:test
//A:hi
```

```
//index.html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>ES6-Webpack</title>
</head>
<body>
	<div id="root">
		this is a test for es6+webpack
	</div>
	<script src="dist/index.bundle.js"></script>
</body>
</html>
```


## Webpack与React
使用React需要安装React和React-DOM
```
npm install --save-dev react react-dom
```
webpack.config.js的配置见上一节
然后我们就可以使用ES6的语法来编写React应用了
```
//Greeter,js
import React, {Component} from 'react'
import config from './config.json';

class Greeter extends Component{
  render() {
    return (
      <div>
        {config.greetText}
      </div>
    );
  }
}
export default Greeter
```

## DEMO: React




## CSS
webpack提供两个工具处理样式表，css-loader 和 style-loader，二者处理的任务不同，css-loader使你能够使用类似@import 和 url(...)的方法实现 require()的功能,style-loader将所有的计算后的样式加入页面中，二者组合在一起使你能够把样式表嵌入webpack打包后的JS文件中。

```
//安装
npm install --save-dev style-loader css-loader
```
使用：
```
module.exports = {
	module:{
		loaders:[
			{
				test:/\.css$/,
				loader:'style!css'	//添加对样式表的处理
			}
		]
	}
}
```
webpack只有单一的入口，其它的模块需要通过 import, require, url等导入相关位置，为了让webpack能找到css文件，我们把它导入入口js文件中
```
import './main.css';	//使用require导入css文件
```



###参考链接
[入门 Webpack，看这篇就够了](https://segmentfault.com/a/1190000006178770#articleHeader0)
[一小时包教会 —— webpack 入门指南](http://www.w2bc.com/Article/50764)