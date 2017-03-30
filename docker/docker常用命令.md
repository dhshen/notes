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

4.添加镜像标签

```
docker tag IMAGES:TAG NEWIMAGES:NEWTAG		//为本地镜像添加新的标签
```

5.使用Dockerfile定制镜像

镜像的定制实际上就是定制每一层所添加的配置、文件。

如果把每一层的修改、安装、构建、操作的命令都写入一个脚本，用这个脚本来构建、定制镜像，这个脚本就是Dockerfile。



### 关于容器Container

1.启动Container

```
docker run [OPTION] IMAGE [COMMAND] [ARG...]

关于OPTIONS
-i 让容器的标准输入保持打开(Keep STDIN open even if not attached)
-t 表示分配一个伪终端并绑定到容器的标准输入上
-v 把主机文件映射到Container中，左侧部分是本地主机路径，右侧部分是对应的Container的路径
	如果想要挂载后的文件是只读的，则加上:ro，例：-v /etc/:/opt/etc/:ro
--rm 表示这个Container运行结束后自动删除
-p 端口映射
-d 让Docker在后台运行而不是直接把执行命令的结果输出在当前宿主机下
--name	给这个容器取一个名字
-w	表示命令执行的当前目录
```

```
docker run -idt -p 80:80 --name dh-nginx -v /Users/dhshen/studyspace/webServer/:/usr/share/nginx/html/ nginx:latest /bin/bash
```

2.查看容器列表

```bash
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

```json
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



## Dockerfile

* **FROM**	

  指定基础镜像，Dockerfile的第一条指令，必备

  docker存在一个特殊的镜像，名为scratch。这个镜像是虚拟的概念，并不实际存在，它表示一个空白的镜像。

  如果你以 scratch 为基础镜像的话，意味着你不以任何镜像为基础，接下来所写的指令将作为镜像第一层开始存在。

* **RUN**

  用来执行命令行命令的

  Dockerfile中每一个指令都会建立一层，RUN也不例外。在需要执行多条命令的时候因使用&&将指令串联起来，避免创建过多的层。示例：

  ```
  RUN buildDeps='gcc libc6-dev make' \
      && apt-get update \
      && apt-get install -y $buildDeps \
      && wget -O redis.tar.gz "http://download.redis.io/releases/redis-3.2.5.tar.gz" \
      && mkdir -p /usr/src/redis \
      && tar -xzf redis.tar.gz -C /usr/src/redis --strip-components=1 \
      && make -C /usr/src/redis \
      && make -C /usr/src/redis install \
      && rm -rf /var/lib/apt/lists/* \
      && rm redis.tar.gz \
      && rm -r /usr/src/redis \
      && apt-get purge -y --auto-remove $buildDeps
  ```

  Dockerfile 支持 Shell 类的行尾添加 \ 的命令换行方式，以及行首 # 进行注释的格式。

  ### 镜像构建的命令：

  ```
  docker build [选项] <上下文路径/URL/->

  示例：
  docker build -t nginx:v3 .
  并将镜像命名为nginx:v3
  指定上下文路径为 . 即当前目录。要注意的是最后的.不是说指定Dockerfile所在的目录，而是指定上下午路径。
  使用当前目录下的Dockerfile作为构建的Dockerfile
  ```

  **docker build 命令会将上下文目录下的内容打包交给 Docker 引擎以帮助构建镜像**

  如果目录下有文件不希望构建时传给Docker引擎，那么可以用跟.gitignore一样的语法编辑一个.dockerignore文件。

  如果不额外指定Dockerfile的话，会将上下文目录下的名为Dockerfile的文件作为Dockerfile。

  ### 其它`docker build`的用法

  1.直接用Git repo进行构建

  ```
  docker build https://github.com/twang2218/gitlab-ce-zh.git#:8.14
  ```

  这行命令指定了构建所需的Git repo，并且指定默认的master分支，构建目录为`/8.14/`，然后Docker就会自己去git clone 这个项目、切换到指定分支、并进入到指定目录后开始构建。

  2.用给定的tar压缩包构建

  ```
  docker build http://server/context.tar.gz
  ```

  dcoker引擎会下载这个包，并自动解压缩，以其作为上下文，开始构建。

  3.从标准输入中读取Dockerfile进行构建

  ```
  docker build - < Dockerfile
  或
  cat Dockerfile | docker build -
  ```

  这种形式由于直接从标准输入中读取 Dockerfile 的内容，它没有上下文，因此不可以像其他方法那样可以将本地文件 COPY 进镜像之类的事情。

  4.从标准输入中读取上下文压缩包进行构建

  ```
  docker build - < content.tar.gz
  ```

  如果发现标准输入的文件格式是 gzip、bzip2 以及 xz 的话，将会使其为上下文压缩包，直接将其展开，将里面视为上下文，并开始构建。

* **COPY**

  复制文件

  COPY 指令将从构建上下文目录中 <源路径> 的文件/目录复制到新的一层的镜像内的 <目标路径> 位置

  ```
  格式：
  COPY <源路径>... <目标路径>
  COPY ["<源路径1>",... "<目标路径>"]
  ```

  <目标路径> 可以是容器内的绝对路径，也可以是相对于工作目录的相对路径（工作目录可以用 WORKDIR 指令来指定）。目标路径不需要事先创建，如果目录不存在会在复制文件前先行创建缺失目录。

  使用 COPY 指令，源文件的各种元数据都会保留。比如读、写、执行权限、文件变更时间等。

* **ADD**

  更高级的复制文件

  可以遵循这样的原则，所有的文件复制均使用 COPY 指令，仅在需要自动解压缩的场合使用 ADD

* **CMD**

  容器启动命令

  Docker 不是虚拟机，容器就是进程。既然是进程，那么在启动容器的时候，需要指定所运行的程序及参数。CMD 指令就是用于指定默认的容器主进程的启动命令的

* **ENTRYPOINT**

  ENTRYPOINT

* **ENV**

  设置环境变量

  ```
  格式：
  ENV <key> <value>
  或
  ENV <key>=<value> <key>=<value> ...
  ```

  示例：

  ```
  ENV VERSION=1.0 DEBUG=on \
      NAME="Happy Feet"
  这个例子中演示了如何换行，以及对含有空格的值用双引号括起来的办法，这和 Shell 下的行为是一致的。
  ```

  定义了环境变量，那么在后续的指令中，就可以使用这个环境变量。

  示例：

  ```
  ENV NODE_VERSION 7.2.0
  RUN curl -SLO "https://nodejs.org/dist/v$NODE_VERSION/node-v$NODE_VERSION-linux-x64.tar.xz" 
  ```

  以下指令支持环境变量的展开：

  ADD、COPY、ENV、EXPOSE、LABEL、USER、WORKDIR、VOLUME、STOPSIGNAL、ONBUILD

* **ARG**

  构建参数。

  构建参数和ENV的效果是一样的，都是设置环境变量，不同的是ARG 所设置的构建环境的环境变量，在将来容器运行时是不会存在这些环境变量的。

* **VOLUME**

  定义匿名卷。

  ```dockerfile
  格式：
  VOLUME ["<路径1>","<路径2>"...]
  VOLUME <路径>
  ```

  之前我们说过，容器运行时应该尽量保持容器存储层不发生写操作，对于数据库类需要保存动态数据的应用，其数据库文件应该保存于卷(volume)中。为了防止运行时用户忘记将动态文件所保存目录挂载为卷，在 Dockerfile 中，我们可以事先指定某些目录挂载为匿名卷，这样在运行时如果用户不指定挂载，其应用也可以正常运行，不会向容器存储层写入大量数据。

  ```
  VOLUME /data
  这里的 /data 目录就会在运行时自动挂载为匿名卷，任何向 /data 中写入的信息都不会记录进容器存储层，从而保证了容器存储层的无状态化。当然，运行时可以使用-v覆盖这个挂载设置。
  ```

* EXPOSE   

  声明端口

  ```dockerfile
  EXPOSE <端口1> [<端口2>...]
  ```

  EXPOSE 指令是声明运行时容器提供服务端口，这只是一个声明，在运行时并不会因为这个声明应用就会开启这个端口的服务。在 Dockerfile 中写入这样的声明有两个好处，一个是帮助镜像使用者理解这个镜像服务的守护端口，以方便配置映射；另一个用处则是在运行时使用随机端口映射时，也就是 docker run -P 时，会自动随机映射 EXPOSE 的端口。

  此外，在早期 Docker 版本中还有一个特殊的用处。以前所有容器都运行于默认桥接网络中，因此所有容器互相之间都可以直接访问，这样存在一定的安全性问题。于是有了一个 Docker 引擎参数 --icc=false，当指定该参数后，容器间将默认无法互访，除非互相间使用了 --links 参数的容器才可以互通，并且只有镜像中 EXPOSE 所声明的端口才可以被访问。这个 --icc=false 的用法，在引入了 docker network 后已经基本不用了，通过自定义网络可以很轻松的实现容器间的互联与隔离。

  要将 EXPOSE 和在运行时使用 -p <宿主端口>:<容器端口> 区分开来。-p，是映射宿主端口和容器端口，换句话说，就是将容器的对应端口服务公开给外界访问，而 EXPOSE 仅仅是声明容器打算使用什么端口而已，并不会自动在宿主进行端口映射。

* WORKDIR

  指定工作路径。

  使用 WORKDIR 指令可以来指定工作目录（或者称为当前目录），以后各层的当前目录就被改为指定的目录，该目录需要已经存在，WORKDIR 并不会帮你建立目录。

  之前提到一些初学者常犯的错误是把 Dockerfile 等同于 Shell 脚本来书写，这种错误的理解可能会导致出现下面这样的错误：

  ```dockerfile
  RUN cd /app
  RUN echo "hello" > world.txt
  ```

  如果将这个 Dockerfile 进行构建镜像运行后，会发现找不到 /app/world.txt 文件，或者其内容不是 hello。原因其实很简单，在 Shell 中，连续两行是同一个进程执行环境，因此前一个命令修改的内存状态，会直接影响后一个命令；而在 Dockerfile 中，这两行 RUN 命令的执行环境根本不同，是两个完全不同的容器。这就是对 Dokerfile 构建分层存储的概念不了解所导致的错误。

  之前说过每一个 RUN 都是启动一个容器、执行命令、然后提交存储层文件变更。第一层 RUN cd /app 的执行仅仅是当前进程的工作目录变更，一个内存上的变化而已，其结果不会造成任何文件变更。而到第二层的时候，启动的是一个全新的容器，跟第一层的容器更完全没关系，自然不可能继承前一层构建过程中的内存变化。

  因此如果需要改变以后各层的工作目录的位置，那么应该使用 WORKDIR 指令。

* USER

  指定当前的用户身份

  ​


### Docker hub

* 登录

  ```
  docker login
  ```

  输入用户名、密码和邮箱来完成注册和登录。 注册成功后，本地用户目录的 .dockercfg 中将保存用户的认证信息。

* 查找镜像

  ```
  docker search IMAGE_NAME
  ```

* 下载镜像

  ```
  docker pull IMAGE_NAME
  ```



### 私有仓库

有时候使用 Docker Hub 这样的公共仓库可能不方便，用户可以创建一个本地仓库供私人使用。

`docker-registry` 是官方提供的工具，可以用于构建私有的镜像仓库



### 数据卷

在用 docker run 命令的时候，使用 -v 标记来创建一个数据卷并挂载到容器里。在一次 run 中多次使用可以挂载多个数据卷
























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























