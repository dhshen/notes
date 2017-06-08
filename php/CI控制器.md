# CI

### 1.控制器

控制器文件名必须是大写字母开头，如：'Hello.php'

控制器类名必须大写字母开头。



通过URI分段向方法传递参数：

* 如果URI 多于两个段，多余的段将作为参数传递到方法中。
* 如果使用了URI路由，传递到方法的参数将是路由后的参数。

```php
<?php
class Products extends CI_Controller {

    public function shoes($sandals, $id)
    {
        echo $sandals;
        echo $id;
    }
}
```



定义默认控制器：

CI可以设置一个默认的控制器，当URI没有分段参数时加载。在**application/config/routes.php** 文件中添加：

```
$route['default_controller'] = 'welcome';
```



重映射方法：

URI的第二段通常决定控制器的哪个方法被调用。但是如果控制器中包含一个`_remap()`方法，那么无论第二段无论是什么参数都会调用`_remap()`方法。它允许你定义你自己的路由规则，重写默认的使用 URI 中的分段来决定调用哪个方法这种行为。

```
public function _remap($method,$params)
{
    
}
```



### 私有方法

如果希望某些方法不能被公开访问，只要将方法声明为`private`或`protected`，这样这个方法就不能被URL访问到了。为了向后兼容原有的功能，在方法名前加上一个下划线前缀也可以让该方法无法访问。



### 将控制器放入子目录中

当使用该功能时，URI 的第一段必须指定目录



### 构造函数

如果打算在控制器中使用构造函数，则必须将下面代码放在里面：

```php
parent::__construct();
```

因为你的构造函数会覆盖父类的构造函数，所以我们要手动调用它。



































