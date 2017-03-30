# git-flow

## git-flow工作流

***master***分支存储了正式发布的历史，不能直接工作在这个 master 分支上。

***develop***分支作为功能的集成分支；你进行任何新的开发的基础分支。

这两个分支被称作为 长期分支。它们会存活在项目的整个生命周期中。而其他的分支，例如针对功能的分支，针对发行的分支，仅仅只是临时存在的。它们是根据需要来创建的，当它们完成了自己的任务之后就会被删除掉。

![git-workflow-release-cycle-4maintenance](https://ww4.sinaimg.cn/large/006tKfTcgy1fdq4yzb90gj30h20akjs2.jpg)

### 功能分支【feature】

每一个新功能位于一个自己的分支，而且是从develop分支作为父分支。

当新功能完成时，合并回develop分支。

新功能提交应该从不直接与master分支交互。



### 发布分支【release】

一旦develop分支上有了做一次发布的足够功能，就从develop分支上fork一个发布分支。新建的分支用于开始发布循环，所以从这个时间点开始之后新的功能不能再加到这个分支上。这个分支只应该做Bug修复，文档生成和其它面向发布的任务。

一旦对外发布的工作都完成了，发布分支合并到master分支并分配一个版本号打好Tag。另外，这些从新建发布分支以来做的修改要合并回develop分支。



### 热修复分支【hotfix】

关于维护分支或说是热修复（hotfix）分支：

用于快速给产品发布版本打补丁。

唯一可以直接从master分支fork出来的分支。

修复完成后修改应该马上合并回master分支和develop分支，master分支应该用新的版本号打好Tag。



## git-flow的追根溯源

Github [**README**]:  [https://github.com/nvie/gitflow](https://github.com/nvie/gitflow)

>A collection of Git extensions to provide high-level repository operations for Vincent Driessen's branching model

git-flow就是基于 Vincent Driessen的分支模型实现的。

Vincent Driessen的分支模型：[A successful Git branching model](http://nvie.com/posts/a-successful-git-branching-model/)

国内关于此篇文章的翻译：[介绍一个成功的 Git 分支模型](http://www.oschina.net/translate/a-successful-git-branching-model)



## git-flow命令

### 初始化

```
git flow init
```

使用 git-flow，从初始化一个现有的 git 库内开始；建议使用默认的命名；



### 新特性

#### 增加新特性：

```
git flow feature start FEATUR_NAME
```

执行的操作：创建了一个基于'develop'的特性分支，并切换到这个分支之下



#### 完成新特性：

```
git flow feature finish FEATURE_NAME
```

执行的操作：合并FEATURE分支到develop分支；删除这个新特性分支；切换回develop分支



#### 发布新特性：

```
git flow feature publish [FEATURE_NAME]
```

执行的操作：发布新特性分支到远程服务器，其它用户也可以使用这分支。



#### 取得一个发布的新特性分支：

```
git flow feature track FEATURE_NAME
```

执行的操作：取得其它用户发布的新特性分支，并签出远程的变更



### 做一个release版本

#### 开始准备release版本：

```
git flow release start [RELEASE_NAME]
```

执行的操作：从 'develop' 分支开始创建一个 release 分支



#### 发布release：

```
git flow release publish RELEASE_NAME
```

类似于发布新特性分支



#### 取得一个发布的release版本

```
git flow release track [RELEASE_NAME]
```



#### 完成一个release

```
git flow release finish RELEASE_NAME
```

执行的操作：

- 归并 release 分支到 'master' 分支
- 用 release 分支名打 Tag
- 归并 release 分支到 'develop'
- 移除 release 分支



### 紧急修复

#### 开始 git flow 紧急修复

```
git flow hotfix start VERSION [BASENAME]
```

VERSION 参数标记着修正版本。你可以从 [BASENAME]开始，[BASENAME]为finish release时填写的版本号



#### 完成紧急修复

```
git flow hotfix finish VERSION
```

当完成紧急修复分支，代码归并回 develop 和 master 分支。相应地，master 分支打上修正版本的 TAG。



## 安装git-flow

关于安装：[https://github.com/nvie/gitflow/wiki/Installation](https://github.com/nvie/gitflow/wiki/Installation)

#### OSX

```
brew install git-flow
```

#### Centos

```
yum install gitflow
```

#### windows

window的安装会麻烦一些。

[git flow：安装和使用](https://my.oschina.net/xsjayz/blog/263059)



## JetBrains系列软件中安装git-flow plugin

以mac下的webstorm为例

![step](https://ww3.sinaimg.cn/large/006tNbRwgy1fdy4stn5yxj30yi0kmq5w.jpg)

![](https://ww2.sinaimg.cn/large/006tNbRwgy1fdy4tzlw35j30q00lt40l.jpg)

按照上图中的步骤可以在webstorm中安装git-flow的插件。

![](https://ww4.sinaimg.cn/large/006tNbRwgy1fdy4wt6h7gj30bo0773yv.jpg)

安装完重启webstorm后可以看到右下角多了Gitflow的选项。

安装这个插件后就不需要再输入git flow的命令了，不过git-flow总共也就那么几条命令，先熟练命令再用这个插件也许更好一点吧。



参考链接：

[Git工作流指南：Gitflow工作流](http://blog.jobbole.com/76867/)

[git-flow备忘清单](https://danielkummer.github.io/git-flow-cheatsheet/index.zh_CN.html)





