# shell

## 了解shell

Bourne Shell (sh)

Bourne-Again Shell (bash)是linux系统中最常用的Shell。

查看系统中所有可用的shell：

```
cat /etc/shells
```

用户登录到Linux系统时，由`/etc/passwd`这个文件决定用户将要使用哪种shell。

查看当前用户使用的shell类型：`echo $SHELL`

Shell脚本的第一行告诉shell使用什么程序解释脚本。

```
#! /bin/bash
```



`/etc/profile` 系统级的初始化文件，定义了一些环境变量，由登陆shell调用执行。

### shell中的变量

Shell中有两种变量的类型：系统变量(环境变量)和用户自定义的变量。

在Shell中，当你第一次使用某变量名时，实际上就定义了这个变量。创建和设置变量的语法：

```shell
varName=varValue
```

**在赋值操作符“=”的周围不能有任何空格！**

1.将字符串赋值给变量：

```
username=kangkang
或者
username="kangkang"
```

2.将一个数字赋值给变量

```
var=1
```

需要注意shell的默认赋值是字符串赋值，比如：

```bash
var=1+1
echo $var		#最终会输出1+1
```

在Bash中，如果要将算术表达式的数值赋值给一个变量，可以使用let命令：

```shell
let var=1+1
echo $var		#2
```

将一个变量的值直接赋值给另一个变量，如下所示：

```shell
a=3
b=$a
```

**将命令的执行结果赋值给变量**，如下所示：

```bash
var=$(pwd)
echo $var
```

将Bash的内置命令read读入的内容赋值给变量：

```bash
read var		#等待用户输入
echo $var
```

变量命名规则与其它语言差不多。变量名的大小写是敏感的。



**使用echo和printf打印变量的值**：

`printf`提供了一个类似于C语言printf()的打印格式化文本的方法。

```
var=kangkang
printf "%s\n" $var
```

与printf命令不同，echo命令没有提供格式化选项，因此echo命令也比printf命令更简单易用。

如何使用echo命令打印变量的值：

```bash
var=10
echo "The number is $var"  #The number is 10
```

有时，需要使用`${}`避免一些歧义，比如：

```bash
LOGDIR="/var/log/"
echo "The log file is $LOGDIRmessage"   #The log file is
```

Bash将尝试查找一个叫做LOGDIRmessage的变量，而不是LOGDIR，为了避免这种歧义我们就需要使用${}语法：

```bash
LOGDIR="/var/log/"
echo "The log file is ${LOGDIR}message"   #The log file is /var/log/message
```



#### 变量的引用

* 当引用一个变量时，通常最好是用双引号将变量名括起来。这样可以防止被引用的变量值中的特殊字符被解释为其他错误含义。
* 使用双引号可以防止变量值中由多个单词组成的字符串分离。

```bash
LIST="one two three"
for var in $LIST   #将变量LIST的值分成了3个参数传递给了for循环
do
	echo "$var"
done
```

```bash
LIST="one two three"
for var in "$LIST"   #将变量LIST的值作为一个整体的传入了for循环
do
	echo "$var"
done
```

注意：只有在变量的值中包含空格或要保留其中的空格时，将变量用双引号括起才是必要的。

**单引号的操作类似于双引号，但是它不允许引用变量，因为在单引号中字符"$"的特殊含义将会失效。每个特殊的字符，除了字符 '，都将按字面含义解释。**



#### export语句的使用

默认情况下，所有用户定义的变量只在当前shell内有效，他们不能被后续的shell引用，要使某个变量可以被子shell引用，可以使用export命令将变量进行输出。

Bash的内置命令export会将指定给它的变量或函数自动输出到后续命令的执行环境。

注意：系统变量会自动输出到后续命令的执行环境。



#### 如何删除变量

Bash下使用`unset`命令来删除响应的变量或函数。unset命令会把变量从当前Shell和后续命令的环境中删除。

```bash
unset LOGDIR
```



#### 如何检查变量是否存在

${varName?Error:The variable is not defined}

${varName:?Error:The variable is not defined}

```bash
#! /bin/bash
echo $varName
ls -al
```

不会报错,后面的`ls -al`会执行

```bash
#! /bin/bash
${varName:?Error:undefined}
echo $varName
ls -al
#./my.sh: line 3: varName: Error:undefined
```

会报错，后面的`ls -al`不会执行

Bash还可以使用if语句判断变量是否存在，后面会提到。

##### shell的扩展之--大括号扩展:

```bash
echo a{b,c,d}e
#abe ace ade
```

```bash
echo {a..z}
#a b c d e f g h i j k l m n o p q r s t u v w x y z
```

```bash
echo {1..3}{a..c}
1a 1b 1c 2a 2b 2c 3a 3b 3c
```

大括号扩展和命令配合使用可以使命令更简化：

```bash
mkdir ~/{dir1,dir2,dir3}  #在home下创建三个子目录
```



#### 创建和使用别名

Bash的内置命令alias用于创建一个别名，创建别名的语法如下：

```bash
alias name='command'
```

例如：

```
alias ll='ls -l'
```

使用命令`unalias`删除一个别名



















