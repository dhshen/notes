# docker实践之搭建php环境

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

4. 提交镜像

   ```
   docker commit 386e dhshen/php_env
   ```

5. 将镜像push到dockerhub

   ```
   docker push dhshen/php_env
   ```

6. 使用镜像运行容器

   ```
   docker run -d -p 8080:80 --name dh.phpenv -it -v /Users/admin/PhpstormProjects:/data dhshen/php-nginx /bin/bash
   ```

7. 进入容器启动nginx和php-fpm

   ```
   docker exec -i -t df5e /bin/bash
   # nginx
   # ./usr/sbin/php-fpm
   # exit
   ```

   环境搭建完成。




### 其它
















































