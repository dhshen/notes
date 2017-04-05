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



PHP对象的写法：

```
<?php
class Car
{
  var $color;
  function Car($color="green") {
    $this->color = $color;
  }
  function what_color() {
    return $this->color;
  }
}
?>
```

NULL 值指明一个变量是否为空值。 同样可用于数据空值和NULL值的区别。

可以通过设置变量值为 NULL 来清空变量数据：



#### 常量

使用 define() 函数来设置常量，函数语法如下：

```php
bool define(name,value [,case_insensitive=false]) 
```

* name：常量名称，即标识符。
* value：常量的值。
* case_insensitive：可选参数，如果设置为true，则大小写不敏感。默认为false，即大小写敏感。

常量定以后，默认是全局变量。



#### 字符串

在php中，只有一个字符串运算符，即并置运算符(.)

并置运算符用于把两个字符串值连接起来。

strlen() 函数返回字符串的长度。

strpos() 函数用于在字符串内查找一个字符或一段指定的文本。如果在字符串中找到匹配，该函数会返回第一个匹配的字符位置。如果未找到匹配，则返回 FALSE。

例：在字符串 "Hello world!" 中查找文本 "world"

```
<?php 
	echo strpos("Hello world!","world"); 
?>
```



#### 运算符

需要注意的是`.=`用于连接两个字符串。

数组运算符：

| 运算符     | 名称   | 描述                                    |
| ------- | ---- | ------------------------------------- |
| x + y   | 集合   | x 和 y 的集合                             |
| x == y  | 相等   | 如果 x 和 y 具有相同的键/值对，则返回 true           |
| x === y | 恒等   | 如果 x 和 y 具有相同的键/值对，且顺序相同类型相同，则返回 true |
| x != y  | 不相等  | 如果 x 不等于 y，则返回 true                   |
| x <> y  | 不相等  | 如果 x 不等于 y，则返回 true                   |
| x !== y | 不恒等  | 如果 x 不等于 y，则返回 true                   |



#### 数组

在php中，`array()`函数用于创建数组。

在php中，有三种类型的数组：

* 数值数组 ：带有数字 ID 键的数组，即一般的正常数组。
* 关联数组：带有指定的键的数组，每个键关联一个值。
* 多维数组：包含一个或多个数组的数组。

`count()` 函数：获取数组的长度。

**关联数组**

关联数组是使用您分配给数组的指定的键的数组。

有两种创建关联数组的方法：

```
$age=array("Peter"=>"35","Ben"=>"37","Joe"=>"43");
```

or：

```
$age['Peter']="35";
$age['Ben']="37";
$age['Joe']="43";
```

实例：

```
<?php
  $age=array("Peter"=>"35","Ben"=>"37","Joe"=>"43");
  echo "Peter is " . $age['Peter'] . " years old.";
?>
```

遍历关联数组：使用foreach循环

```
<?php
  $age=array("Peter"=>"35","Ben"=>"37","Joe"=>"43");
  foreach($age as $x=>$x_value)
  {
    echo "Key=" . $x . ", Value=" . $x_value;
    echo "<br>";
  }
?>
```



#### 数组排序

sort() - 对数组进行升序排列

rsort() - 对数组进行降序排列

asort() - 根据关联数组的值，对数组进行升序排列

ksort() - 根据关联数组的键，对数组进行升序排列

arsort() - 根据关联数组的值，对数组进行降序排列

krsort() - 根据关联数组的键，对数组进行降序排列



#### 超级全局变量

PHP中预定义了几个超级全局变量：

- $GLOBALS
- $_SERVER
- $_REQUEST
- $_POST
- $_GET
- $_FILES
- $_ENV
- $_COOKIE
- $_SESSION



**$GLOBALS**

是一个包含了全部变量的全局组合数组。变量的名字就是数组的键。

**$_SERVER**

是一个包含了诸如头信息(header)、路径(path)、以及脚本位置(script locations)等等信息的数组。这个数组中的项目由 Web 服务器创建。不能保证每个服务器都提供全部项目；

**$_REQUEST**

用于收集HTML表单提交的数据。

```
<html>
<body>

<form method="post" action="<?php echo $_SERVER['PHP_SELF'];?>">
  Name: <input type="text" name="fname">
  <input type="submit">
</form>

<?php 
  $name = $_REQUEST['fname']; 
  echo $name; 
?>

</body>
</html>
```

**$_POST**

在HTML form标签的指定该属性："method="post"。然后就可以用$_POST来获取表单信息了。

**$_GET**

同理，也是用来获取表单信息的。

#### foreach循环

foreach循环用于遍历数组。

```
<html>
<body>
<?php
  $x=array("one","two","three");
  foreach ($x as $value)
  {
  echo $value . "<br>";
  }
?>
</body>
</html>
```



#### 函数

PHP提供了超过1000个内建的函数。

PHP函数准则：

* 函数的名称应该提示出它的功能
* 函数名称以字母或下划线开头(不能以数字开头)



#### 魔术变量

PHP 向它运行的任何脚本提供了大量的预定义常量。

有八个魔术常量它们的值随着它们在代码中的位置改变而改变。

`__LINE__` 文件中的当前行号

`__FILE__` 文件的完整路径和文件名

`__DIR__  `  文件所在的目录

`__FUNCTION__`  函数名称

`__CLASS__`  类的名称

`__TRAIT__`  Trait 的名字（PHP 5.4.0 新加）

`__METHOD__`  类的方法名（PHP 5.0.0 新加）。返回该方法被定义时的名字（区分大小写）。

`__NAMESPACE__`  当前命名空间的名称(区分大小写)



#### 命名空间

命名空间解决以下两类问题：

1. 用户编写的代码与PHP内部的类/函数/常量或第三方类/函数/常量之间的名字冲突。
2. 为很长的标识符名称(通常是为了缓解第一类问题而定义的)创建一个别名（或简短）的名称，提高源代码的可读性



#### 面向对象

大部分内容同C++、Java。

类的变量使用 **var** 来声明, 变量也可以初始化值。

```
<?php
class Site {
  /* 成员变量 */
  var $url;
  var $title;
  
  /* 成员函数 */
  function setUrl($par){
     $this->url = $par;
  }
  
  function getUrl(){
     echo $this->url . PHP_EOL;
  }
  
  function setTitle($par){
     $this->title = $par;
  }
  
  function getTitle(){
     echo $this->title . PHP_EOL;
  }
}
?>
```

**PHP_EOL** 为换行符。

变量 **$this** 代表自身的对象。

**构造函数**

```
function __construct( 参数列表 ) {
   //code
}
```

**析构函数**

```
function __destruct(){
  
}
```

**继承**

PHP 使用关键字 **extends** 来继承一个类，PHP 不支持多继承，格式如下：

```
class Child extends Parent {
   // 代码部分
}
```

**方法重写**

如果从父类继承的方法不能满足子类的需求，可以对其进行改写，这个过程叫方法的覆盖（override），也称为方法的重写。

**访问控制**

- **public（公有）：**公有的类成员可以在任何地方被访问。
- **protected（受保护）：**受保护的类成员则可以被其自身以及其子类和父类访问。
- **private（私有）：**私有的类成员则只能被其定义所在的类访问。

类属性必须定义为公有，受保护，私有之一。如果用 var 定义，则被视为公有。

```
class MyClass
{
    public $public = 'Public';
    protected $protected = 'Protected';
    private $private = 'Private';

    function printHello()
    {
        echo $this->public;
        echo $this->protected;
        echo $this->private;
    }
}
```

类中的方法可以被定义为公有，私有或受保护。如果没有设置这些关键字，则该方法默认为公有。

```
class MyClass
{
    // 声明一个公有的构造函数
    public function __construct() { }

    // 声明一个公有的方法
    public function MyPublic() { }

    // 声明一个受保护的方法
    protected function MyProtected() { }

    // 声明一个私有的方法
    private function MyPrivate() { }

    // 此方法为公有
    function Foo()
    {
        $this->MyPublic();
        $this->MyProtected();
        $this->MyPrivate();
    }
}
```

**接口**

使用接口（interface），可以指定某个类必须实现哪些方法，但不需要定义这些方法的具体内容。

接口是通过 **interface** 关键字来定义的，就像定义一个标准的类一样，但其中定义所有的方法都是空的。

接口中定义的所有方法都必须是公有，这是接口的特性。

要实现一个接口，使用 **implements** 操作符。类中必须实现接口中定义的所有方法，否则会报一个致命错误。

类可以实现多个接口，用逗号来分隔多个接口的名称。

```php
<?php

// 声明一个'iTemplate'接口
interface iTemplate
{
    public function setVariable($name, $var);
    public function getHtml($template);
}


// 实现接口
class Template implements iTemplate
{
    private $vars = array();
  
    public function setVariable($name, $var)
    {
        $this->vars[$name] = $var;
    }
  
    public function getHtml($template)
    {
        foreach($this->vars as $name => $value) {
            $template = str_replace('{' . $name . '}', $value, $template);
        }
 
        return $template;
    }
}
```

**常量**

可以把在类中始终保持不变的值定义为常量。在定义和使用常量的时候不需要使用 $ 符号。

```php
<?php
class MyClass
{
    const constant = '常量值';

    function showConstant() {
        echo  self::constant . PHP_EOL;
    }
}

echo MyClass::constant . PHP_EOL;

$classname = "MyClass";
echo $classname::constant . PHP_EOL; // 自 5.3.0 起

$class = new MyClass();
$class->showConstant();

echo $class::constant . PHP_EOL; // 自 PHP 5.3.0 起
?>
```

**抽象类**

任何一个类，如果它里面至少有一个方法是被声明为抽象的，那么这个类就必须被声明为抽象的。

定义为抽象的类不能被实例化。

被定义为抽象的方法只是声明了其调用方式（参数），不能定义其具体的功能实现。

继承一个抽象类的时候，子类必须定义父类中的所有抽象方法；另外，这些方法的访问控制必须和父类中一样（或者更为宽松）。例如某个抽象方法被声明为受保护的，那么子类中实现的方法就应该声明为受保护的或者公有的，而不能定义为私有的。此外方法的调用方式必须匹配，即类型和所需参数数量必须一致。例如，子类定义了一个可选参数，而父类抽象方法的声明里没有，则两者的声明并无冲突。

```
<?php
abstract class AbstractClass
{
 // 强制要求子类定义这些方法
    abstract protected function getValue();
    abstract protected function prefixValue($prefix);

    // 普通方法（非抽象方法）
    public function printOut() {
        print $this->getValue() . PHP_EOL;
    }
}

class ConcreteClass1 extends AbstractClass
{
    protected function getValue() {
        return "ConcreteClass1";
    }

    public function prefixValue($prefix) {
        return "{$prefix}ConcreteClass1";
    }
}

class ConcreteClass2 extends AbstractClass
{
    public function getValue() {
        return "ConcreteClass2";
    }

    public function prefixValue($prefix) {
        return "{$prefix}ConcreteClass2";
    }
}

$class1 = new ConcreteClass1;
$class1->printOut();
echo $class1->prefixValue('FOO_') . PHP_EOL;

$class2 = new ConcreteClass2;
$class2->printOut();
echo $class2->prefixValue('FOO_') . PHP_EOL;
?>
```

**static关键字**

声明类属性或方法为 static(静态)，就可以不实例化类而直接访问。

静态属性不能通过一个类已实例化的对象来访问（但静态方法可以）。

由于静态方法不需要通过对象即可调用，所以伪变量 $this 在静态方法中不可用。

静态属性不可以由对象通过 -> 操作符来访问。

```php
<?php
class Foo {
  public static $my_static = 'foo';
  
  public function staticValue() {
     return self::$my_static;
  }
}

print Foo::$my_static . PHP_EOL;
$foo = new Foo();

print $foo->staticValue() . PHP_EOL;
?>	
```

**final关键字**

如果父类中的方法被声明为 final，则子类无法覆盖该方法。如果一个类被声明为 final，则不能被继承。

```php
<?php
class BaseClass {
   public function test() {
       echo "BaseClass::test() called" . PHP_EOL;
   }
   
   final public function moreTesting() {
       echo "BaseClass::moreTesting() called"  . PHP_EOL;
   }
}

class ChildClass extends BaseClass {
   public function moreTesting() {
       echo "ChildClass::moreTesting() called"  . PHP_EOL;
   }
}
// 报错信息 Fatal error: Cannot override final method BaseClass::moreTesting()
?>
```

**调用父类构造方法**

PHP 不会在子类的构造方法中自动的调用父类的构造方法。要执行父类的构造方法，需要在子类的构造方法中调用 。

**parent::__construct()** 。

```php
<?php
class BaseClass {
   function __construct() {
       print "BaseClass 类中构造方法" . PHP_EOL;
   }
}
class SubClass extends BaseClass {
   function __construct() {
       parent::__construct();  // 子类构造方法不能自动调用父类的构造方法
       print "SubClass 类中构造方法" . PHP_EOL;
   }
}
class OtherSubClass extends BaseClass {
    // 继承 BaseClass 的构造方法
}

// 调用 BaseClass 构造方法
$obj = new BaseClass();

// 调用 BaseClass、SubClass 构造方法
$obj = new SubClass();

// 调用 BaseClass 构造方法
$obj = new OtherSubClass();
?>
```







## PHP函数

var_dump() 返回变量的数据类型和值





















































