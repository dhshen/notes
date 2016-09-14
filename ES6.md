# ES6 读书笔记 #
## 1.ECMAScript 6 简介 ##
Babel是一个ES6转码器，可以将ES6代码转为ES5代码，从而在浏览器或其他环境执行。

在浏览器环境使用babel时需要将ES6代码写在`<script type="text/babel">`中。


## 2. let和const命令 ##

### 2.1 let命令
<font color=red>let命令</font>用于声明变量。其所声明的变量只在let命令所在的代码块内有效。

let不像var那样会发生“变量提升”现象。所以变量一定要在声明后使用，否则报错。

暂时性死区：在代码块内，使用let命令声明变量之前，该变量都是不可用的。在语法上称为暂时性死区。

ES6明确规定，如果区块中存在let和const命令，则这个区块对这些命令声明的变量从一开始就形成封闭作用域。只要在声明之前就使用这些变量，就会报错。

let不允许在相同作用域内重复声明一个变量。

### 2.2 块级作用域
块级作用域的写法：

	{
		let tmp = ...;
		...
	}
ES6规定，函数本身的作用域在其所在的块级作用域之内。

### 2.3 const命令
***const命令***用来声明常量。一旦声明，其值就不能改变。这意味着const一旦声明常量，就必须立即初始化，不能留到以后赋值。

const的作用域与let命令相同，只在声明所在的块级作用域内有效。

const声明的常量同样存在暂时性死区，只在声明后使用。

const也不可重复声明常量。

**const命令只是保证变量名指向的地址不变，并不保证改地址的数据不变，所以将一个对象变量声明为常量必须非常小心，因为对象本身是可变的。**

如果想将对象冻结，应该使用 `object.freeze`方法。

### 2.4 跨模块常量 
```
A.js	//设置跨模块的常量：export const
export const A = 1;
export const B = 2;
export const C = 3;
```
```
B.js	//引入其它模块定义的常量(第1种方式)
import * as constants from './constants';	//引入其所有常量并封装成一个对象constants。
console.log(constants.A);
console.log(constants.B);  
```
```
C.js	//引入其它模块定义的常量(第2种方式)
import {A,B} from './constants';
console.log(A);
console.log(B);
```

### 2.5 全局对象的属性
在ES6中，var命令和function命令声明的全局变量依旧是全局对象的属性；
let命令、const命令和class命令声明的全局变量不属于全局对象的属性。

## 3.变量的解析结构赋值 ##
ES6允许按照一定模式，从数组和对象中提取值，对变量进行赋值，这被称为解构。
### 3.1数组的解构赋值
####基本用法

```
例如：
var a = 3,
	b = 4,
	c = 5;
可以写成：
var [a,b,c] = [3,4,5];
```

如果解构不成功，变量的值就等于undefined。

#### 默认值
解构赋值允许指定默认值。

```
var [foo = true] = [];		   //foo = true;
如果对应的赋值是undefined时默认值生效，否则赋值生效，例如：
var [x = 1] = [undefined]		//x = 1
var [x = 1] = [null]			 //x = null
var [x = 1] = [2]				//x = 2
```

默认值可以引用解构赋值的其它变量，但该变量必须已经声明。

```
let [x = 1, y = x] = []		//x = 1; y = 1
let [x = 1, y = x] = [2]		//x = 2; y = 2
let [x = 1, y = x] = [1，2]		//x = 1; y = 2
```

### 3.2 对象的解构赋值

```
var { foo, bar } = { foo: "aaa", bar: "bbb"};	//foo = "aaa"; bar = "bbb"
var {变量名,变量名,...} = {属性名:属性值,属性名:属性值,...}
```
对象的解构与数组有一个重要的不同。数组的元素是按次序排列的，变量的取值由它的位置决定；而对象的属性没有次序，**变量名必须与属性同名，才能取到正确的值**。

如果变量名与属性名不一致，则必须写成下面这样（上面的写法实际上是一种简写的方式）：
```
var {属性名: 变量名,属性名: 变量名,...} = {属性名: 属性值, 属性名: 属性值, ...}
var {foo: baz} = {foo:"aaa",bar:"bbb"}		//baz = "aaa"
```
解构赋值的变量都会重新声明！
对象的解构也可以指定默认值，默认值生效的条件也是对象的属性值严格等于undefined

```
var x;
{x} = {x:1}
```
上面这样写会报错，因为js引擎会将{x}理解成一个代码块，从而发生语法错误。只有不将大括号写在行首，才能解决这个问题，如将整个结构赋值语句放在了一个圆括号里面：
```
({x} = {x: 1});
```
对象的解构可以很方便地将现有的方法赋值到某个变量：
`var {log,sin,cos} = Math`
上面的代码将Math的3个方法赋值到了对应的变量上。

### 3.3 字符串的解构赋值

```
const [a,b,c,d,e] = 'hello';
a = 'h';
b = 'e';
c = 'l';
d = 'l';
e = 'o';
```
类似数据的对象都有一个length属性，因此可以针对length属性解构赋值：
`let [length: len] = "hello"	//len = 5;`

### 3.4 数值和布尔值的解构赋值
解构赋值的规则是：只要等号右边的值不是对象，就先将其转为对象。由于undefined和null无法转为对象，所以对它们进行解构赋值都会报错。
```
let {toString: s} = 123;
s === Number.prototype.toString 	//true
```

### 3.5 函数参数的解构赋值
```
function add([x,y]){
	return x + y;
}
add([1, 2])		//3
```

### 3.6 圆括号问题
不能使用圆括号的情况：
1. 变量声明语句中，模式不能带有圆括号
2. 函数参数中，模式不能带有圆括号
3. 不能将整个模式或嵌套模式中的一层放在圆括号中

### 3.7 用途
交换变量的值：
```
[x, y] = [y, x]
```
从函数返回多个值：
```
//返回一个数组
function example(){
	return [1,2,3];
}
var [a,b,c] = example();

//返回一个对象
function example(){
	return {
		foo: 1,
		bar: 2		
	};
}
var {foo, bar} = example();
```
函数参数的定义：
解构赋值可以很方便地将一组参数与变量名对应起来：
```
function f({x, y,z}){ ... }
f({z: 2,y: 3,x: 1})
```
提取JSON数据
函数参数的默认值
遍历Map解构


## 4.字符串的扩展
### 字符串的遍历器接口
ES6为字符串增加了遍历器接口，使字符串可以由 ` for...of ` 循环遍历。
```
for( let codePoint of 'foo'){
	console.log(codePoint)
}
// f
// o
// o
```

### includes()、startsWith()、endsWith()
传统上JavaScript只有indexOf方法可用来确定一个字符串是否包含在另一个字符串中。
ES6又提供了3种新的方法：
* includes(): 返回true/false,表示是否找到了参数字符串
* startsWith(): 返回true/false,表示参数字符串是否在源字符串的头部
* endsWith(): 返回true/false,表示参数字符串是否在源字符串的尾部

这三个方法都支持第二个参数，表示开始搜索的位置。不过endsWith的第2个参数n表示它针对前n个字符。

### repeat()
返回一个新字符串，表示将原字符串重复n次。
参数为0为''
参数是小数，会被取整
参数为负数会报错
参数为NaN等同于0
如果repeat的参数是字符串，则会先转换成数字。

### ES7的字符串补全长度
* padStart() 头部补齐
* padEnd() 尾部补齐

```
'x'.padStart(5,'ab')	//'ababx'
'x'.padEnd(5,'ab')		//'xabab'
```
如果省略第二参数则空格补齐
如果原字符串的长度等于或大于指定的最小长度，则返回原字符串。

### 模板字符串
传统的JavaScript输出模板通常是：

```
var name = 'xiaoming';
$('#target').append('<div>hello,'+name+'</div>');
```
上面的写法相当繁琐不便，ES6引入模板字符串来解决这个问题。

```
$('#target').append(`<div>hello,${name}</div>`);
```
模板字符串是增强版的字符串，用**反引号(`)**标识。它可以**当做普通字符串使用**，**也可以用来定义多行字符串**，或者**在字符串中嵌入变量**。
```
//普通字符串
`In Javascript '\n' is a line-feed`

//多行字符串
`In Javascript this is
 not legal`

//字符串中嵌入变量
var name = "Bob", time = "today";
`Hello ${name},how are you ${time}?`
```
如果使用模板字符串表示多行字符串，所有的空格和缩进都会保存在输出中。(类似Markdown中)
在模板字符串中嵌入变量，需要将变量名写在`${}`中，大括号中也可以放表达式，可以进行运算。

模板字符串中还可以调用函数
```
function fn(){
	return "Hello,World!";
}
`foo ${fn()} bar`		// foo Hello World bar
```
如果大括号中的值不是字符串，将按照一般的规则(toString)转为字符串。












