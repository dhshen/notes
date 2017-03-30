# CI入门随笔

目录结构：

```
myshop  
|-----system                框架程序目录  
    |-----core          框架的核心程序  
        |-----CodeIgniter.php   引导性文件  
        |-----Common.php    加载基类库的公共函数  
        |-----Controller.php    基控制器类文件：CI_Controller  
        |-----Model.php     基模型类文件：CI_Model  
        |-----Config.php    配置类文件：CI_Config  
        |-----Input.php     输入类文件：CI_Input  
        |-----Output.php    输出类文件：CI_Output  
        |-----URL.php       URL类文件：CI_URl  
        |-----Router.php    路由类文件：CI_Router  
        |-----Loader.php    加载类文件：CI_Loader  
    |-----helpers           辅助函数  
        |-----url_helper.php    url相关的辅助函数，如：创建url的辅助函数  
        |-----captcha_helper.php创建图形验证码的辅助函数  
    |-----libraries         通用类库  
        |-----Pagination.php    通用分页类库  
        |-----Upload.php    通用文件上传类库  
        |-----Image_lib.php 通用图像处理类库  
        |-----Session.php   通用session类库  
    |-----language          语言包  
    |-----database          数据库操作相关的程序  
        |-----DB_active_rec.php 快捷操作类文件(ActiveRecord)  
    |-----fonts         字库  
      
|-----application           项目目录  
    |-----core          项目的核心程序  
    |-----helpers           项目的辅助函数  
    |-----libraries         通用类库  
    |-----language          语言包  
    |-----config            项目相关的配置  
        |-----config.php    项目相关的配置文件     
        |-----database.php  数据库相关的配置文件  
        |-----autoload.php  设置自动加载类库的配置文件  
        |-----constants.php 常量配置文件  
        |-----routes.php    路由配置文件  
    |-----controllers       控制器目录  
        |-----welcome.php   控制器文件，继承CI_Controller  
    |-----models            模型目录  
        |-----welcome_model.php 模型文件，继承CI_Model  
    |-----views         视图目录  
        |-----welcome.php   视图模板文件，默认后缀名为.php  
    |-----cache         存放数据或模板的缓存文件  
    |-----errors            错误提示模板  
    |-----hooks         钩子，在不修改系统核心文件的基础上扩展系统功能  
    |-----third_party       第三方库  
    |-----logs          日志  
  
|-----index.php             入口文件 
```



URL格式：

```
http://example.com/[controller-class]/[controller-method]/[arguments]
```

