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


## 5.正则的扩展




## 6. 数值的扩展
### 二进制和八进制数值表示法
ES6分别用前缀0b（或0B）表示二进制数值，用0o(或0O)表示二进制数值。

### 6.2 Number.isFinite(),NUmber.isNaN()
ES6在Number对象上增加了这两个方法

### 6.3 Number.parsezInt()，Number.parseFloat()
ES6将全局方法parseInt()和parseFloat()移植到了NUmber对象上，行为完全保持一致，这样做的目的是为了逐步减少全局性方法，是语言逐步模块化。

### 6.4 Number.isInteger()
用来判断一个值是否为整数，在Javascript内部，整数和浮点数是同样的存储方法，所以3和3.0被视为同一个值。

### 6.7 Math对象的扩展
Math.trunc() 去除一个数的小数部分，返回整数部分。对于空值和无法截取整数的值，返回NaN。
Math.sign() 用于判断一个数到底是正数、负数，还是零。

## 7.数组的扩展
### Array.from()
用于将两类对象转为真正的数组：类似数组的对象 和 可遍历对象(包括Set和Map)
常见的类似数组的对象是DOM操作返回的NodeList集合，以及函数内部argument对象。
所谓类似数组的对象，本质特征只有一点，即必须有length属性。
只要是部署了Iterator接口的数据结构，Array.from都能将其转化为数组。

### Array.of()
用于将一组值转换为数组
```
Array.of(2,3,4)  //[2,3,4]
```
这个方法的主要目的是为了弥补数组构造函数Array()的不足。
比如：
```
Array(3)	//[,,,]
Array(3,11,8) //[3,11,8]
```
即当Array只有一个参数n时其结果是初始化成有n个元素的数组，而不是[n]

### 7.3 数组示例的copyWithin()
```
Array.prototype.copyWithin(target, start = 0, end = this.length)
target: 从该位置开始替换数据 
start: 从该位置开始读取数据，默认为0，负数则表示倒数
end:到该位置前停止读取数据，默认等于数组的长度，如果为负值表示倒数。
```

### 7.4 数组实例的find()和findIndex()
find()方法用于找出第一个符合条件的数组成员。
```
['a','b','c'].find(function( value, index, arr ){
	if(value == 'b'){
		return true;	
	}
	return false;
})

[1,4,-5,23].find((n => n < 0 ));
```
它的参数是一个回调函数，所有数组成员依次执行该回调函数，直到找出第一个返回值为true的成员，然后返回该成员。如果没有符合条件的成员，则返回undefined。

findIndex()方法与find方法类似，只是它是返回第一个符合条件的数组成员的位置，如果所有成员都不符合条件，则返回-1。

### 7.5 数组实例的fill
fill()方法使用给定值填充数据
```
['a','b','c'].fill(7);	//[7,7,7]
```
fill方法还可以接受第二个和第三个参数，用于指定填充的起始位置和结束位置。

### 7.6 数组实例的 entries()、keys()和values()
这三个方法用于遍历数组。它们都返回一个遍历器对象。可用for...of循环遍历。

keys() 是对键名的遍历
values() 是对键值的遍历
entries() 是对键值对的遍历

```
for( let index of ['a','b'].keys()){
	console.log(index);
}
//0
//1

for( let value of ['a','b'].values()){
	console.log(value);
}
//'a'
//'b'

for( let [index,elem] of ['a','b'].entries()){
	console.log(index,elem);
}
//0 'a'
//1 'b'
```

### 7.7 数组实例的includes()
Array.prototype.includes方法返回一个布尔值，表示某个数组是否包含给定的值。(该方法属于ES7)
```
[1,2,3].includes(2);	//true
```
第2个参数表示搜索的起始位置，默认为0，如果第二个参数为负数，则表示倒数的位置，若此位置超出数组长度，则会重置从0开始。

###　7.8 数组的空位
数组的空位指数组的某一个位置没有任何值，比如Array(3)	//[,,,]
注意，空位不是undefined，一个位置等于undefined依然是有值的。空位没有任何值。
ES5对空位的处理很不一致，大部分情况下会忽略空位，如forEach()、every()等都会跳过空位。
ES6明确将空位转换为undefined。

## 8. 函数的扩展
### 8.1 函数参数的默认值
####*ES6*允许为函数的参数设置默认值，即直接写在参数定义的后面。####
```
function example( x, y = 0 ){
	...
}
```
参数变量是默认声明的，所以不能用let或const再次声明。

####与解构复制默认值结合使用
```
function foo({x, y = 5}){
	console.log(x,y);
}

foo({})	//undefined,5
foo({x1,y:7})	//5，7
```

####参数默认值的位置
定义默认值的参数应该是函数的尾参数。

#### 函数的length属性
指定了默认值以后，函数的length属性将返回没有指定默认值的参数个数。
这是因为length属性的含义是：该函数预期传入的参数的个数。

#### 作用域
如果参数默认值是一个变量，则该变量所处的作用域与其他变量的作用域规则是一样的，即先是当前函数的作用域，然后才是全局作用域。
```
var x = 1;
function foo(x, y = x){
	console.log(y);
}
foo(2)	//2
```

### 8.2 rest参数
ES6引入了**rest参数**，形式为 **...变量名**
用于获取函数的多余参数，这样就不需要使用arguments对象了。
rest参数搭配的变量是一个**数组**，该变量将多余的参数放入其中。
注意：rest参数之后不能再有其他参数(即只能是最后一个参数)，否则会报错。
函数的length属性不包括rest参数。
```
function foo( x, ...others ){
	console.log(others);
}
```

### 8.3 扩展运算符
扩展运算符是三个点(**...**)，它好比**rest参数的逆运算**，将一个数组转为用逗号分隔的参数序列。






