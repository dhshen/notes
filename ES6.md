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
```
例如：
var a = 3,
	b = 4,
	c = 5;
可以写成：
var [a,b,c] = [3,4,5];
```
如果解构不成功，变量的值就等于undefined。


