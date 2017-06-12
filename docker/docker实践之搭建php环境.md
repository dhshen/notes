# docker实践之搭建php环境

## 制作镜像

1. 下载centos镜像

   ```
   docker pull centos:latest
   ```

   下载的centos版本为7.3

2. 由centos镜像启动一个容器

   ```
   docker run -i -t centos /bin/bash
   ```

3. 在容器中安装所需环境

   参考：[centos 7.x 下安装php环境](https://webtatic.com/packages/php56/)

   ```
   # 更新源
   rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
   rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm
   ```

   ```
   # 安装php
   yum install php56w php56w-opcache php56w-fpm php56w-gd php56w-mysql php56w-gd php56w-snmp php56w-xml php56w-imap php56w-ldap php56w-mbstring
   ```

   ```
   # 安装完成后，查看安装的php
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

   ```
   # 安装nginx
   yum install nginx
   ```

   ```
   # 配置nginx
   # 规划映射到外部的目录为/data
   # /data/conf 目录存放nginx的配置文件
   # /data/log 目录存放nginx的日志
   # php项目路径为/data

   vi nginx.conf
   # 添加 include /data/conf/*.conf;
   ```

4. 提交镜像 【将容器提交成镜像】

   ```
   docker commit 386e dhshen/php_env
   ```
   到这一步镜像就制作好了，可以直接使用了，但是我们可以将镜像push到docker.hub，这样下次在其它电脑上下载下来直接使用就可以了。当然，push上去的镜像是公开的，所有人都可以下载。

5. 将镜像push到dockerhub

   ```
   docker push dhshen/php_env
   ```
   [当然，首先得去docker.io注册账号，类似github，首先得有一个登陆的过程，此处略过]


## 启动容器

1. 下载镜像(如果镜像在本地制作的就不必下载了)

   ```
   docker pull dhshen/php_env
   ```

   下载完成后使用如下命令查看镜像

   ```
   docker images
   ```

2. 运行容器

   ```dockerfile
   docker run -d -p 80:80 --name my_php_env -it -v /Users/admin/PhpstormProjects:/data dhshen/php_env /bin/bash
   ```

   将本地的80端口映射到容器的80端口，将本地的`/Users/admin/PhpstormProjects`目录映射为容器的`/data`目录。

   在`/Users/admin/PhpstormProjects`下创建`conf`和`log`文件，一个用来放nginx配置文件，另一个用来存放nginx日志。

   在`conf`目录下创建local.conf，示例为CI框架的PHP应用。

   ```nginx
   server{
   	listen 80;
   	server_name localhost;
   	index index.php index.html index.htm;

   	error_log /data/log/err.log;
   	access_log /data/log/access.log;

   	location / {
   		root /data/example;
   		try_files $uri $uri/ /example/index.php?$query_string;
   	}

   	location ~ \.php($|/) {
   		root /data/example;
   		include fastcgi_params;
   		fastcgi_pass 127.0.0.1:9000;
   		#fastcgi_pass unix:/tmp/php-cgi.sock;
   		fastcgi_index index.php;
   		include fastcgi.conf;
   		fastcgi_split_path_info ^(.+\.php)(.*)$;
   		fastcgi_param PATH_INFO $fastcgi_path_info;
   		fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
   		fastcgi_param CI_ENV  'development';
   	}

   }
   ```

3. 进入容器启动nginx和php-fpm

   ```
   docker exec -i -t df5e /bin/bash
   # nginx
   # ./usr/sbin/php-fpm
   # exit
   ```

4. 在浏览器中输入`localhost`，如果看到CI框架的welcome页面，说明一切OK了。












































