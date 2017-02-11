## docker常用命令

### 关于镜像Image

1.获取镜像

```
docker pull NAME[:TAG]
```

2.查看镜像列表

```
docker images [OPTION]
```

3.删除镜像

```
docker rmi IMAGE [IMAGE...]
```



### 关于容器Container

1.启动Container

```
docker run [OPTION] IMAGE [COMMAND] [ARG...]

关于OPTIONS
-i 让容器的标准输入保持打开(Keep STDIN open even if not attached)
-t 表示分配一个伪终端并绑定到容器的标准输入上
-v 把主机文件映射到Container中，左侧部分是本地主机路径，右侧部分是对应的Container的路径
	如果想要挂载后的文件是只读的，则加上:ro，例：-v /etc/:/opt/etc/:ro
--rm=true 表示这个Container运行结束后自动删除
-p 端口映射
-d 让Docker在后台运行而不是直接把执行命令的结果输出在当前宿主机下
--name	给这个容器取一个名字
-w	表示命令执行的当前目录


```

```
docker run -idt -p 80:80 --name dh-nginx -v /Users/dhshen/studyspace/webServer/:/usr/share/nginx/html/ nginx:latest /bin/bash
```

2.查看容器列表

```
docker ps [OPTIONS]

Options:
  -a, --all             Show all containers (default shows just running)
  -f, --filter filter   Filter output based on conditions provided
      --format string   Pretty-print containers using a Go template
      --help            Print usage
  -n, --last int        Show n last created containers (includes all states) (default -1)
  -l, --latest          Show the latest created container (includes all states)
      --no-trunc        Don't truncate output
  -q, --quiet           Only display numeric IDs
  -s, --size            Display total file sizes
```

3.移除一个或多个容器实例

```
docker rm [OPTIONS] CONTAINER [CONTAINER...]
```

4.停止一个正在运行的容器

```
docker kill [OPTIONS] CONTAINER [CONTAINER...]
```

5.重启一个正在运行的容器

```
docker restart [OPTIONS] CONTAINER [CONTAINER...]
```

6.启动一个已经停止的容器

```
docker start [OPTIONS] CONTAINER [CONTAINER...]
```



## 关于调试

1.查看容器的输出信息

```
docker logs
```



## 实战

1.下载镜像

```
docker pull redis:latest
docker pull node:latest
```

2.安装pm2

```
docker run -i -t node /bin/bash
$ npm install -g pm2
$ pm2 -v
$ exit
```

3.将容器提交成镜像

```
docker login
docker commit CONTAINERID dhshen/node_pm2
docker images  //可以查看到images中包含了dhshen/node_pm2这个镜像了
```

4.将镜像push到docker上

```
docker push dhshen/node_pm2
上传成功后可以测试一下：
docker rmi IMAGEID	//删除本地镜像
docker pull dhshen/node_pm2
```

5.在本地创建一个部署node应用的目录

```
$ mkdir nodespace		/Users/dhshen/studyspace/nodespace
$ cd nodespace
$ mkdir docker_node		//node应用
$ mkdir log				//保存日志的目录
```

6.在docker_node目录下编写应用

```
//package.json
{
  "name": "docker_node",
  "version": "1.0.0",
  "description": "",
  "main": "app.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "dependencies":{
    "express":"4.10.2",
    "redis":"0.12.1"
  },
  "engines":{
    "node":">=0.10.0"
  },
  "author": "",
  "license": "ISC"
}
```

```
//app.js
var express = require("express");
var redis = require("redis");
var app = express();
var redisHost = process.env['REDIS_PORT_6379_TCP_ADDR'];
var redisPort = process.env['REDIS_PORT_6379_TCP_ADDR'];
var redisClient = redis.createClient(redistPort,redisHost);
app.get('/',function(req,res){
	console.log('get request');
	redisClient.get('access_count',function(err,countNum){
		if(err){
			return res.send('get access count error');
		}

		if(!countNum){
			countNum = 1;
		}else{
			countNum = parseInt(countNum) + 1;
		}

		redisClient.set('access_count',countNum,function(err){
			if(err){
				return res.send('set access count error');
			}
			res.send(countNum.toString());
		});
	});
});

app.listen(8000);
```

7.启动一个临时container安装依赖包

```
docker run --rm -it -v /Users/dhshen/studyspace/nodespace/docker_node:/var/node/docker_node -w /var/node/docker_node dhshen/node_pm2 npm install
```

8.启动容器

```
docker run 
	-d 
	--name "nodeCountAccess" 
	-p 8000:8000 
	-v /Users/dhshen/studyspace/nodespace/docker_node:/var/node/docker_node
	-v /Users/dhshen/studyspace/nodespace/log/pm2:/root/.pm2/logs/
	--link redis-server:redis
	-w /var/node/docker_node/ dhshen/node_pm2 pm2 start --no-daemon app.js
```

```
docker run -d --name "nodeCountAccess" -p 8000:8000 -v /Users/dhshen/studyspace/nodespace/docker_node:/var/node/docker_node -v /Users/dhshen/studyspace/nodespace/log/pm2:/root/.pm2/logs/ --link redis-server:redis -w /var/node/docker_node/ dhshen/node_pm2 pm2 start --no-daemon app.js
```



## 将多个Container盒子连接起来

连接系统依据容器的名称来执行。

除了在启动容器的时候使用`--name`来给容器命名外，还可以使用如下命令来查看容器的名字：

```
docker inspect -f "{{.Name}}" CONTAINER_ID
```

使用`--link`可以让容器之间安全的进行交互

`—link` 参数的格式为` --link name:alias`，其中 name 是要链接的容器的名称，alias 是这个连接的别名。

例如：

```
$ docker run -d -p --name web --link db:db trainning/webapp python app.py

此时db容器和web容器建立互联关系
$ docker ps
CONTAINER ID       STATUS         PORTS                    NAMES
349169744e49  Up About a minute  5432/tcp                 db, web/db
aed84ee21bde  Up 2 minutes       0.0.0.0:49154->5000/tcp  web
```



### docker -h

>   attach      Attach to a running container
>   build       Build an image from a Dockerfile
>   commit      Create a new image from a container's changes
>   cp          Copy files/folders between a container and the local filesystem
>   create      Create a new container
>   deploy      Deploy a new stack or update an existing stack
>   diff        Inspect changes on a container's filesystem
>   events      Get real time events from the server
>   exec        Run a command in a running container
>   export      Export a container's filesystem as a tar archive
>   history     Show the history of an image
>   images      List images
>   import      Import the contents from a tarball to create a filesystem image
>   info        Display system-wide information
>   inspect     Return low-level information on Docker objects
>   kill        Kill one or more running containers
>   load        Load an image from a tar archive or STDIN
>   login       Log in to a Docker registry
>   logout      Log out from a Docker registry
>   logs        Fetch the logs of a container
>   pause       Pause all processes within one or more containers
>   port        List port mappings or a specific mapping for the container
>   ps          List containers
>   pull        Pull an image or a repository from a registry
>   push        Push an image or a repository to a registry
>   rename      Rename a container
>   restart     Restart one or more containers
>   rm          Remove one or more containers
>   rmi         Remove one or more images
>   run         Run a command in a new container
>   save        Save one or more images to a tar archive (streamed to STDOUT by default)
>   search      Search the Docker Hub for images
>   start       Start one or more stopped containers
>   stats       Display a live stream of container(s) resource usage statistics
>   stop        Stop one or more running containers
>   tag         Create a tag TARGET_IMAGE that refers to SOURCE_IMAGE
>   top         Display the running processes of a container
>   unpause     Unpause all processes within one or more containers
>   update      Update configuration of one or more containers
>   version     Show the Docker version information
>   wait        Block until one or more containers stop, then print their exit codes























