#Nginx 配置相关总结
## 1. Nginx基本命令
```
start nginx				//启动
nginx -s quit			  //停止
nginx -s reload			//重新载入
```

## 2.Nginx基本配置说明
[摘自博客园](http://www.cnblogs.com/xiaogangqq123/archive/2011/03/02/1969006.html)
```
#运行用户
user www-data;    
#启动进程,通常设置成和cpu的数量相等
worker_processes  1;

#全局错误日志及PID文件
error_log  /var/log/nginx/error.log;
pid        /var/run/nginx.pid;

#工作模式及连接数上限
events {
    use   epoll;             #epoll是多路复用IO(I/O Multiplexing)中的一种方式,但是仅用于linux2.6以上内核,可以大大提高nginx的性能
    worker_connections  1024;#单个后台worker process进程的最大并发链接数
    #multi_accept on; 
}

#设定http服务器，利用它的反向代理功能提供负载均衡支持
http {
    #设定mime类型,类型由mime.type文件定义
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    #设定日志格式
    access_log    /var/log/nginx/access.log;

    #sendfile 指令指定 nginx 是否调用 sendfile 函数（zero copy 方式）来输出文件，对于普通应用，
    #必须设为 on,如果用来进行下载等应用磁盘IO重负载应用，可设置为 off，以平衡磁盘与网络I/O处理速度，降低系统的uptime.
    sendfile        on;
    #tcp_nopush     on;

    #连接超时时间
    #keepalive_timeout  0;
    keepalive_timeout  65;
    tcp_nodelay        on;
    
    #开启gzip压缩
    gzip  on;
    gzip_disable "MSIE [1-6]\.(?!.*SV1)";	#IE6及以下浏览器不支持gzip，这里可以设置IE6以下的浏览器不压缩

    #设定请求缓冲
    client_header_buffer_size    1k;
    large_client_header_buffers  4 4k;

    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-enabled/*;

    #设定负载均衡的服务器列表
    upstream mysvr {
    	server 192.168.8.2:80  weight=1;	
    	server 192.168.8.3:80  weight=6;	#weigth参数表示权值，权值越高被分配到的几率越大
    }


   server {
        listen       80;				#侦听80端口
        server_name  www.xx.com;		#定义使用www.xx.com访问
        access_log  logs/www.xx.com.access.log  main;	#设定本虚拟主机的访问日志

	    #默认请求
	    location / {
	          root   /root;      #定义服务器的默认网站根目录位置
	          index index.php index.html index.htm;   #定义首页索引文件的名称
	
	          fastcgi_pass  www.xx.com;
	          fastcgi_param  SCRIPT_FILENAME  $document_root/$fastcgi_script_name; 
	          include /etc/nginx/fastcgi_params;
        }

	    # 定义错误提示页面
	    error_page   500 502 503 504 /50x.html;  
	        location = /50x.html {
	        root   /root;
	    }
	
	    #静态文件，nginx自己处理
	    location ~ ^/(images|javascript|js|css|flash|media|static)/ {
	        root /var/www/virtual/htdocs;
	        expires 30d;	#过期30天，静态文件不怎么更新，过期可以设大一点，如果频繁更新，则可以设置得小一点。
	    }
	    #PHP 脚本请求全部转发到 FastCGI处理. 使用FastCGI默认配置.
	    location ~ \.php$ {
	        root /root;
	        fastcgi_pass 127.0.0.1:9000;
	        fastcgi_index index.php;
	        fastcgi_param SCRIPT_FILENAME /home/www/www$fastcgi_script_name;
	        include fastcgi_params;
	    }
	    #设定查看Nginx状态的地址
	    location /NginxStatus {
	        stub_status            on;
	        access_log              on;
	        auth_basic              "NginxStatus";
	        auth_basic_user_file  conf/htpasswd;
	    }
	    #禁止访问 .htxxx 文件
	    location ~ /\.ht {
	        deny all;
	    }
     
     }
}

```
负载均衡和反向代理配置
```
#设定http服务器，利用它的反向代理功能提供负载均衡支持
http {
    #设定负载均衡的服务器列表
    upstream mysvr {
	    server 192.168.8.2x:80  weight=1;
	    server 192.168.8.3x:80  weight=6;	#weigth参数表示权值，权值越高被分配到的几率越大
    }

    upstream mysvr2 {
	    server 192.168.8.x:80  weight=1;
	    server 192.168.8.x:80  weight=6;
    }

   #第一个虚拟服务器
   server {
        listen       80;			#侦听192.168.8.x的80端口
        server_name  192.168.8.x;

      	#对aspx后缀的进行负载均衡请求
    	location ~ .*\.aspx$ {
	    	root   /root;
		    index index.php index.html index.htm;
	
		    proxy_pass  http://mysvr ;#请求转向mysvr定义的服务器列表
	
		    #以下是一些反向代理的配置可删除.
		    proxy_redirect off;
		    #后端的Web服务器可以通过X-Forwarded-For获取用户真实IP
		    proxy_set_header Host $host;
		    proxy_set_header X-Real-IP $remote_addr;
		    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
		    client_max_body_size 10m;    #允许客户端请求的最大单文件字节数
		    client_body_buffer_size 128k;  #缓冲区代理缓冲用户端请求的最大字节数，
		    proxy_connect_timeout 90;  #nginx跟后端服务器连接超时时间(代理连接超时)
		    proxy_send_timeout 90;        #后端服务器数据回传时间(代理发送超时)
		    proxy_read_timeout 90;         #连接成功后，后端服务器响应时间(代理接收超时)
		    proxy_buffer_size 4k;             #设置代理服务器（nginx）保存用户头信息的缓冲区大小
		    proxy_buffers 4 32k;               #proxy_buffers缓冲区，网页平均在32k以下的话，这样设置
		    proxy_busy_buffers_size 64k;    #高负荷下缓冲大小（proxy_buffers*2）
		    proxy_temp_file_write_size 64k;  #设定缓存文件夹大小，大于这个值，将从upstream服务器传
       }
   }
}
```
以上配置说明[摘自博客园](http://www.cnblogs.com/xiaogangqq123/archive/2011/03/02/1969006.html)


## 3.Nginx搭建反向代理服务器(以及负载均衡服务器)
案例：
一台服务器上分别部署了两个应用：app-A和app-B，如何使用不同的域名来访问这两个应用呢？
www.example-a.com访问app-A，www.example-b.com访问app-B
```
http {
	# 省略其它配置
	
	#设定负载均衡的服务器列表,可以配置多台服务器
    upstream appa{
		server 127.0.0.1:8080;
    }
    
    upstream appb{
		server 127.0.0.1:8081;
    }
    
	
    server {
        listen       80;	
        server_name  www.example-a.com;		#定义使用www.example-a.com访问时
				
        location / {
            proxy_pass http://appa; 
        }
   }
   
   server {
		listen 80;
		server_name www.example-b.com;		#定义使用www.example-b.com访问时
   		
        location / {
            proxy_pass http://appb; 
        }
   } 
}

```

应用：在本地使用nginx做反向代理服务器访问两个应用
可以分别使用本地地址(内网IP)和localhost分别来访问这两个应用
```
http {
	# 省略其它配置
	
	#设定负载均衡的服务器列表,可以配置多台服务器
    upstream appa{
		server 127.0.0.1:8080;
    }
    
    upstream appb{
		server 127.0.0.1:8081;
    }
    
	
    server {
        listen       80;	
        server_name  172.24.7.11;		#使用本地地址访问时
				
        location / {
            proxy_pass http://appa; 
        }
   }
   
   server {
		listen 80;
		server_name localhost;		#定义使用localhost访问时
   		
        location / {
            proxy_pass http://appb; 
        }
   } 
}
```


## 附录：nginx默认的配置文件
```
#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    server {
        listen       80;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   html;
            index  index.html index.htm;
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }


    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}

}

```