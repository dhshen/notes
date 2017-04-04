# PHP入门笔记

## 基础

#### 基本的PHP语法

```
<?php
	//php代码
?>
```

* PHP 中的每个代码行都必须以分号结束。
* PHP中的注释同JavaScript。
* PHP是区分大小写的。
* PHP是一门弱类型语言。

#### php变量

php变量规则：

* 变量以$符号开始，后面跟着变量的名称。
* 变量名是区分大小写的。
* 变量必须以字母或者下划线字符开始。
* 变量名只能包含字母数字字符以及下划线。
* 变量名不能包含空格。

**php变量作用域:**

php拥有4种不同的变量作用域：

```
local
global
static
parameter
```

**局部和全局作用域：**

与C或JavaScript类似，不同的是，在函数内访问全局变量时需要使用`global`关键字

```
<?php
$x=5;
$y=10;

function myTest()
{
global $x,$y;
$y=$x+$y;
}

myTest();
echo $y; // 输出 15
?>
```

PHP 将所有全局变量存储在一个名为 `$GLOBALS[index] `的数组中。 *index* 保存变量的名称。这个数组可以在函数内部访问，也可以直接用来更新全局变量。

因此，上例也可写成：

```
<?php
$x=5;
$y=10;

function myTest()
{
$GLOBALS['y']=$GLOBALS['x']+$GLOBALS['y'];
} 

myTest();
echo $y;
?>
```

**static作用域**

当一个函数完成时，它的所有变量通常都会被删除。然而，有时候希望某个局部变量不要被删除。要做到这一点，在第一次声明变量时使用 **static** 关键字：

```
<?php

function myTest()
{
static $x=0;
echo $x;
$x++;
}

myTest();
myTest();
myTest();
//输出012
?>
```

然后，每次调用该函数时，该变量将会保留着函数前一次被调用时的值。

**注意：**该变量仍然是函数的局部变量。

**参数作用域**

参数是通过调用代码将值传递给函数的局部变量。



#### echo/print

是php中两个基本的输出方式。

区别:

​	echo：可以输出一个或多个字符串。没有返回值。输出速度比print快。

​	print：只允许输出一个字符串，返回值总为1

两者都可以带括号或者不带括号。

输出变量和字符串：

```
<?php
$txt1="学习 PHP";
$txt2="RUNOOB.COM";
$cars=array("Volvo","BMW","Toyota");
 
echo "<h2>PHP 很有趣!</h2>";
echo $txt1;						//输出变量
echo "<br>";
echo "在 $txt2 学习 PHP ";			//在字符串中直接使用变量
echo "<br>";
echo "我车的品牌是 {$cars[0]}";		//输出数组的元素
?>
```



#### 数据类型

String（字符串）
Integer（整型）
Float（浮点型）
Boolean（布尔型）
Array（数组）
Object（对象）
NULL（空值）





















