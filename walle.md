## Walle部署系统部署

1.执行LAMP环境搭建命令

```bash
yum -y install httpd mysql mysql-libs mysql-server php php-cli php-common php-fpm php-gd php-imap php-ldap php-mbstring php-snmp php-xml php-mysql 
```

2.查看以上命令安装的php版本

```bash
[root@iZ941fxzd86Z ~]# yum list installed | grep php
php.x86_64          5.3.3-48.el6_8      @updates
php-cli.x86_64      5.3.3-48.el6_8      @updates
php-common.x86_64   5.3.3-48.el6_8      @updates
php-fpm.x86_64      5.3.3-48.el6_8      @updates
php-gd.x86_64       5.3.3-48.el6_8      @updates
php-imap.x86_64     5.3.3-48.el6_8      @updates
php-ldap.x86_64     5.3.3-48.el6_8      @updates
php-mbstring.x86_64 5.3.3-48.el6_8      @updates
php-mysql.x86_64    5.3.3-48.el6_8      @updates
php-pdo.x86_64      5.3.3-48.el6_8      @updates
php-snmp.x86_64     5.3.3-48.el6_8      @updates
php-xml.x86_64      5.3.3-48.el6_8      @updates
```

版本太旧，walle系统的要求是php5.4+

3.删除安装的php

```bash
[root@iZ941fxzd86Z ~]# yum remove php.x86_64 php-cli.x86_64 php-common.x86_64 php-fpm.x86_64 php-gd.x86_64 php-imap.x86_64 php-ldap.x86_64 php-mbstring.x86_64 php-mysql.x86_64 php-pdo.x86_64 php-snmp.x86_64 php-xml.x86_64
```

查看系统是多少位的

```bash
[root@iZ941fxzd86Z ~]# uname -a
Linux iZ941fxzd86Z 2.6.32-431.23.3.el6.x86_64 #1 SMP Thu Jul 31 17:20:51 UTC 2014 x86_64 x86_64 x86_64 GNU/Linux
```

由x86_64看出是64位的

### 开始安装PHP5.6

参考链接 [PHP 5.6 on CentOS/RHEL 7.2 and 6.8 via Yum](https://webtatic.com/packages/php56/)

不要随便找一个php5.6 yum安装的页面去进行php的安装，否则因为系统版本的不同，更新了不匹配的yum源，导致yum安装出现错误。本例google的关键词为：`centos 6.8 php5.6`

1.更新源

```
rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-6.noarch.rpm
rpm -Uvh https://mirror.webtatic.com/yum/el6/latest.rpm
```

注意：安装软件源的时候一定要根据centos的版本来安装，如果6.X的系统装了7.X的源，会出现各种错误，这是比较坑的地方。

2.安装PHP

```
yum install php56w php56w-opcache php56w-fpm php56w-gd php56w-mysql php56w-gd php56w-snmp php56w-xml php56w-imap php56w-ldap php56w-mbstring
```

安装完成后，查看安装的php

```
[root@iZ941fxzd86Z ~]# yum list installed | grep php
php56w.x86_64                      5.6.30-1.w6                         @webtatic
php56w-cli.x86_64                  5.6.30-1.w6                         @webtatic
php56w-common.x86_64               5.6.30-1.w6                         @webtatic
php56w-fpm.x86_64                  5.6.30-1.w6                         @webtatic
php56w-gd.x86_64                   5.6.30-1.w6                         @webtatic
php56w-imap.x86_64                 5.6.30-1.w6                         @webtatic
php56w-ldap.x86_64                 5.6.30-1.w6                         @webtatic
php56w-mbstring.x86_64             5.6.30-1.w6                         @webtatic
php56w-mysql.x86_64                5.6.30-1.w6                         @webtatic
php56w-opcache.x86_64              5.6.30-1.w6                         @webtatic
php56w-pdo.x86_64                  5.6.30-1.w6                         @webtatic
php56w-snmp.x86_64                 5.6.30-1.w6                         @webtatic
php56w-xml.x86_64                  5.6.30-1.w6                         @webtatic
```

上述LAMP环境搭建命令安装了的包都有了。

### walle代码检出

```
mkdir -p /data/www/walle-web && cd /data/www/walle-web  # 新建目录
git clone git@github.com:meolu/walle-web.git .          # 代码检出
```

### 设置Mysql连接

在第一步我们除了安装php还安装了mysql，先测试连接一下mysql

```bash
[root@iZ941fxzd86Z walle-web]# mysql
ERROR 2002 (HY000): Can't connect to local MySQL server through socket '/var/lib/mysql/mysql.sock' (2)
```

这是由于Mysql服务未正常启动导致的

```
[root@iZ941fxzd86Z walle-web]# lsof -i:3306				//查看mysql端口状况
[root@iZ941fxzd86Z walle-web]# service mysqld status	//查看mysql服务状态
mysqld is stopped
```

启动mysql服务

```
$ service mysqld start
```

第一次登陆mysql的时候是没有密码的，我们使用root账户登陆mysql

```
$ mysql -u root
```

为root账号设置密码

```
mysql> use mysql;
mysql> update user SET Password = PASSWORD('密码') where User = 'root';
```

刷新权限

```
mysql> FLUSH PRIVILEGES;
mysql> exit;
```

退出后重新进入mysql时就需要输入密码了

````
$ mysql -u root -p
````

还需要做一步操作：配置mysql可被远程访问，这样我们就可以在自己的电脑上通过db工具连接数据库了

```
$ mysql -u root -p
mysql> GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'root密码' WITH GRANT OPTION;
mysql> FLUSH PRIVILEGES;
mysql> exit;
```

#### walle的mysql配置修改

```
vi config/local.php +14
'db' => [
    'dsn'       => 'mysql:host=127.0.0.1;dbname=walle', # 新建数据库walle
    'username'  => 'root',                          	# 连接的用户名
    'password'  => 'coincard2017',                      # 连接的密码
],
```



### 安装composer，如果已安装跳过

```
curl -sS https://getcomposer.org/installer | php
mv composer.phar /usr/local/bin/composer                # PATH目录
```



### 安装vendor

```
cd walle-web
composer install --prefer-dist --no-dev --optimize-autoloader -vvvv
```



### 初始化项目

在进行下列操作之前需要在mysql中新建一个名叫"walle"的数据库，否则初始化数据的时候会报错。

```
cd walle-web
./yii walle/setup # 需要你的yes
```

输入yes后初始化成功!



### 配置nginx

首先我们可以用`whereis`命令查看nginx在哪些位置

```bash
[root@iZ941fxzd86Z walle-web]# whereis nginx
nginx: /usr/sbin/nginx /etc/nginx /usr/lib64/nginx /usr/share/nginx /usr/share/man/man8/nginx.8.gz /usr/share/man/man3/nginx.3pm.gz
```

nginx的配置文件在/etc/nginx目录下,nginx.conf是配置入口文件，在测试服务器的此配置中包含了conf.d目录下所有的以`.conf`结尾的配置，我们在conf.d目录下新建一个walle.conf文件，填写如下配置

```
server {
    listen       80;
    server_name  120.76.97.128; # 改你的host
    root /data/www/walle-web/web; # 根目录为web
    index index.php;

    # 建议放内网
    # allow 192.168.0.0/24;
    # deny all;

    location / {
        try_files $uri $uri/ /index.php$is_args$args;
    }

    location ~ \.php$ {
        try_files $uri = 404;
        fastcgi_pass   127.0.0.1:9000;
        fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
        include        fastcgi_params;
    }
}
```

启动php

```
查看php-fpm的位置
[root@iZ941fxzd86Z php]# whereis php-fpm
php-fpm: /usr/sbin/php-fpm /etc/php-fpm.conf /etc/php-fpm.d /usr/share/man/man8/php-fpm.8.gz
启动：
$ ./usr/sbin/php-fpm
```

启动或重载nginx，同理找到nginx的目录，执行下面命令

```
./nginx
或
./nginx -s reload
```

## 完成安装

在浏览器上输入`120.76.97.128`我们就可以看到walle部署系统了。



## walle的使用与项目的配置

因为运行php的用户`apache`(具体的用户根据`ps aux|grep php`命令来查看)是被设置成不能登录的，因此设置ssh key时需要修改一下`/etc/passwd`这个配置

```bash
vi /etc/passwd
把apache用户后面的shell环境路径/sbin/nologin要临时改成/bin/bash
```

在home目录下新建一个apache目录，并将该目录的所有者设置成apache

```bash
mkdir /home/apache
chown apache:apache /home/apache
```

切换到apache用户，并进入其主目录，因为ssh key是生成在`~/.ssh`目录下的

```bash
[root@iZ941fxzd86Z .ssh]# su apache
bash-4.1$ cd ~
bash-4.1$ pwd
/var/www
```

#### 执行生成ssh key的命令

```bash
ssh-keygen -t rsa -C "xxxxxx@qq.com"
```

发现报错：

```bash
bash-4.1$ ssh-keygen -t rsa -C "lisonallen@qq.com"
Generating public/private rsa key pair.
Enter file in which to save the key (/var/www/.ssh/id_rsa):
Could not create directory '/var/www/.ssh'.
```

这是因为.ssh目录没有创建且apache用户没有权限去创建，切回到root用户，执行如下操作：

```bash
mkdir /var/www/.ssh
chown apache:apache /var/www/.ssh
```

再切到apache账户：

```bash
[root@iZ941fxzd86Z .ssh]# su apache
bash-4.1$ ssh-keygen -t rsa -C "lisonallen@qq.com"
```

apache用户的ssh key就生成了，在`/var/www/.ssh`下。

将`/var/www/.ssh`目录下`id_rsa.pub`的内容复制到github的信任key。

第一次添加key到github后需要自己手动的去拉一次代码，因为需要在是否接收服务器的ssh key那里填一下`yes`。





















