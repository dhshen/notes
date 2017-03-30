# Docker入门与实战

## 第一章：初识Docker

Docker是基于Go语言实现的开源容器项目。（dotCloud公司2013年）

Docker的构想：“Build,Ship and Run Any App,Anywhere”	“一次封装，到处运行”。

Docker的基础：Linux容器技术。(Linux  Container，LXC)

可以将Docker理解成一种轻量级的沙盒(sandBox)。每个容器内运行着一个应用，不同的容器相互隔离，容器之间可以通过网络互相通信。容器对系统资源的需求也十分有限，可以把容器理解成应用本身也没有问题。

如何正确的构建应用。

在云时代，开发者创建的应用必须要能方便的在网络上传播，也就是说必须脱离底层物理硬件的限制；同时必须是“任何时间、任何地点”可获取的。

通过容器打包应用，解耦应用和运行平台。

开发和运维(DevOps)

优势：

* 更快的交付和部署。
* 更高效的利用资源。它是内核级的虚拟化。
* 更轻松的迁移和扩展。
* 更简单的更新管理。



与传统虚拟机的比较：

* 快。启动和停止可以在秒级实现。
* 资源需求少，一台主机上可运行数千个Docker容器。除了运行其中的应用外，基本不消耗额外的系统资源。
* 通过类似Git设计理念的操作来方便用户获取、分发和更新应用镜像，存储复用，增量更新。
* 通过Dockerfile支持灵活的自动化创建和部署机制。



## 第二章：核心概念与安装配置

三大核心概念：镜像、容器、仓库。

**镜像**：类似于虚拟机镜像，可以将它理解为一个只读的模板。

例如：一个镜像可以包含一个基本的操作系统环境，里面仅安装了Apache应用程序。可以把它称为一个Apache镜像。

镜像是创建Docker容器的基础。



**容器**：类似于一个轻量级的沙箱。Docker利用容器来运行和隔离应用。

**容器是从镜像创建的应用运行实例**

可以把容器看作是一个简易版的Linux系统环境（包括root用户权限、进程空间、用户空间和网络空间等）以及运行在其中的应用程序打包而成的盒子。

注意：**镜像自身是只读的。容器从镜像启动的时候，会在镜像的最上层创建一个可写层**。



**仓库**：类似于代码仓库，它是Docker集中存放镜像文件的场所。

Docker仓库分为 **公开仓库**和**私有仓库**两种。

Docker Hub是最大的公开仓库。

Docker也支持用户在本地网络内创建一个只能自己访问的私有仓库。



### 安装Docker

Centos：

>To install Docker, you need the 64-bit version of CentOS 7.

[Docker官网的Centos安装指南](https://docs.docker.com/engine/installation/linux/centos/#/prerequisites)



## 第三章：使用Docker镜像

Docker运行容器前需要本地存在对应的镜像，如果镜像没有保存在本地，Docker会尝试先从默认镜像仓库下载。（默认使用Docker Hub公共注册服务器中的仓库，用户也可以通过配置，使用自定义的镜像仓库。）

通常情况下，描述一个镜像需要包括 **“名称+标签”** 信息。

可以使用docker pull命令直接从Docker Hub镜像源来下载镜像。

```bash
docker pull IMAGE[:TAG]
```

对于Docker镜像来说，如果不显示指定TAG，则默认会选择`latest`标签，这会下载仓库中最新版本的镜像。

*注意：一般来说，latest标签意味着该镜像的内容会跟踪最新的非稳定版本而发布，内容是不稳定的。*

镜像文件由若干层（layer）组成。当不同的镜像包括相同的层时，本地仅存储该层的一份内容，减小了需要的存储空间。

*严格来说，镜像的仓库名称中还应该添加仓库地址(即registry，注册服务器)作为前缀，只是我们默认使用的是Docker Hub服务，该前缀可以忽略*，例如：

```
docker pull ubuntu:14.04
```

相当于：

```
docker pull registry.hub.docker.com/ubuntu:14.04
```

如果从非官方的仓库下载，则需要在仓库名称前指定完整的仓库地址。



```
docker images		//列出本地主机上已有镜像的基本信息
```

镜像的ID唯一标识镜像，一般可以用该ID的前若干个字符组成的可区分字符串来替代完整的ID，类似于git中的commit id。



镜像的标签信息用来标注不同的版本信息。

```
docker tag IMAGE:TAG NEWIMAGE:NEWTAG		//为本地镜像添加新的标签
```

新标签指向的镜像和原来的标签指向的是同一个镜像，只是别名不同而已，docker tag起到类似链接的作用。



```bash
docker inspect IMAGE:TAG	//获取该镜像的详细信息，包括制作者、适应架构、各层的数字摘要等。
```



```
docker history IMAGE:TAG	//列出镜像各层的创建信息
```

在docker中，如果过长的命令被自动截断了，可以使用`--no-trunc`选项来输出完整的命令。



```
docker search TERM		//搜寻镜像
```



```
docker rmi IMAGE[IMAGE...]		//删除镜像，其中IMAGE可以为标签或ID
```

*当同一个镜像拥有多个标签时，`docker rmi 标签`命令只是删除该镜像的一个标签而已，但是当镜像只有一个标签时，`docker rmi 标签`会彻底删除镜像*

*当`docker rmi`命令后面跟上镜像的ID时，会先尝试删除所有指向该镜像的标签，然后删除该镜像文件本身*

*当有该镜像创建的容器存在时，镜像文件默认是无法被删除的。可以使用`-f`参数来强制删除镜像，但不推荐这么做。*



#### 创建镜像

创建镜像有三种方法：

* 基于已有镜像的容器创建
* 基于本地模板导入
* 基于Dockerfile文件创建



##### 基于已有镜像的容器创建

```
docker commit [OPTIONS] CONTAINER REPOSITORY[:TAG]
```

OPTIONS的主要选项包括

```
-a	作者信息
-c	提交时执行的Dockerfile指令
-m	提交信息，类似于Git中的commit -m
-p	提交时暂停容器运行
```

##### 基于本地模板导入

```
docker import [OPTIONS] file|URL [REPOSITORY[:TAG]]
```

直接从一个操作系统模板文件导入一个镜像。



#### 存出和载入镜像

##### 存出镜像

导出镜像到本地文件

```
docker save -o ubuntu_14.04.tar ubuntu:14.04	
```

之后用户就可以通过复制ubuntu_14.04.tar文件将该镜像分享给他人。

##### 载入镜像

```
docker load --input ubuntu_14.04.tar
或
docker load < ubuntu_14.04.tar
```



#### 上传镜像

可以使用`docker push`命令上传镜像到仓库，默认上传到Docker Hub官方仓库(**需要登陆**)。

```
docker push NAME[:TAG]
docker push [REGISTRY_HOST[:REGISTRY_PORT]/]NAME[:TAG]
例如：
docker push dhshen/test:latest
```



## 第四章：Docker容器

**容器是镜像的一个运行实例**

镜像是静态的只读文件，而容器带有运行时需要的可写文件层。

docker容器是独立运行的一个应用及它们必须的运行环境。

#### 新建容器

```
docker create 
```

使用`docker create`命令新建的容器处于停止状态，可以使用`docker start`命令来启动它。

`Create`命令和后续的`run`命令支持的选项都十分复杂，主要包括如下几大类：

* 与容器运行模式相关

  ```
  -d	是否在后台运行容器，默认为否
  -i 	保持标准输入打开，默认为false
  -P	通过NAT机制将容器标记暴露的端口自动映射到本地主机的临时端口
  -p	指定如何映射到本地主机端口
  -t	是否分配一个伪终端，默认为false
  -v	挂载主机上的容器卷到容器内
  -w	容器内的默认工作目录
  ```

* 与容器和环境配置相关

  ```
  --ip=""		指定容器的IPv4地址
  --link		链接到其它容器
  --name		指定容器的别名
  -e			指定容器内环境变量
  ```


* 与容器资源限制和安全保护相关

  ```

  ```



#### 启动容器

```
docker start ...
```



#### 新建并启动一个容器

```
docker run ...
```

等价于先执行`docker create`命令，再执行`docker start`命令

**当利用`docker run`来创建并启动容器时，Docker在后台运行的标准操作包括：**

* 检查本地是否存在指定的镜像，不存在就从共有仓库下载；
* 利用镜像创建一个容器，并启动该容器；
* 分配一个文件系统给容器，并在只读的镜像层外面挂载一层可读写层
* 从宿主主机配置的网桥接口中桥接一个虚拟接口到容器中；
* 从网桥的地址池配置一个IP地址给容器
* 执行用户指定的应用程序
* 执行完毕后容器被自动终止

例如：

```
docker run -it ubuntu:14.04 /bin/bash
```

用户可以按`Ctrl+D`或输入`exit`命令来退出容器，之后容器就自动处于退出(Exited)状态了。这是因为对Docker容器来说，当运行的应用退出后，容器也就没有继续运行的必要了。

##### 守护态运行

通过添加`-d`参数使Docker容器在后台以守护态形式运行。

```bash
docker run -d ubuntu:14.04 /bin/sh k-c "while true;do echo hello world;sleep 1;done"
```

此时，要获取容器的输出信息，可以使用如下命令：

```
docker logs CONTAINER_ID
```



#### 终止容器

```
docker stop CONTAINER_ID
```

首先向容器发送SIGTERM信号，等待一段超时时间(*默认10秒*)后，再发送SIGKILL信号来终止容器。

```
docker kill CONTAINER_ID
```

会直接发送SIGKILL信号来强行终止容器。



##### 重启容器

```
docker restart
```

将一个运行状态的容器先终止，然后再重新启动它。



#### 进入容器

进入后台运行的容器进行操作，有多种方法，包括：官方的`attach`或`exec`命令，以及第三方的`nsenter`工具等。

**attach**命令：

```
docker attach [OPTIONS] CONTAINER_ID
```

使用attach并不方便，当多个窗口同时用attach命令连到同一个容器时所有窗口都会同步显示。

**exec**命令：

```
docker exec [OPTIONS] CONTAINER_ID COMMAND [ARG...]
```

exec命令可以在容器内直接执行任意命令。

比较重要的参数有：

​	`-i`：打开标准输入接受用户输入密码

​	`-t`：分配伪终端

​	`--privileged`：是否给执行命令以高权限

​	`-u`：执行命令的用户名或ID

示例：

```
docker exec -it 243c /bin/bash
```

可以看到，一个bash终端打开了，在不影响容器内其它应用的前提下，用户可以很容易与容器进行交互。

*通过指定-it参数来保持标准输入打开，并且分配一个伪终端。通过exec命令对容器执行操作是最为推荐的方式。*





































