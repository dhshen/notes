# Git的使用
## Git简介
Git是分布式版本控制系统。
SVN是集中式版本控制系统，而Git则是分布式版本控制系统。
>集中式版本控制系统，版本库是集中存放在中央服务器的，而干活的时候，用的都是自己的电脑，所以要先从中央服务器取得最新的版本，然后开始干活，干完活了，再把自己的活推送给中央服务器。中央服务器就好比是一个图书馆，你要改一本书，必须先从图书馆借出来，然后回到家自己改，改完了，再放回图书馆。

集中式版本控制系统最大的毛病就是必须联网才能工作，如果在局域网内还好，带宽够大，速度够快，可如果在互联网上，遇到网速慢的话，不得把人给憋死啊.

>分布式版本控制系统根本没有“中央服务器”，每个人的电脑上都是一个完整的版本库，这样，你工作的时候，就不需要联网了，因为版本库就在你自己的电脑上。既然每个人电脑上都有一个完整的版本库，那多个人如何协作呢？比方说你在自己电脑上改了文件A，你的同事也在他的电脑上改了文件A，这时，你们俩之间只需把各自的修改推送给对方，就可以互相看到对方的修改了

和集中式版本控制系统相比，分布式版本控制系统的安全性要高很多，因为每个人电脑里都有完整的版本库。分布式版本控制系统通常也有一台充当“中央服务器”的电脑，但这个服务器的作用仅仅是用来方便“交换”大家的修改，没有它大家也一样干活，只是交换修改不方便而已。

## git的安装和配置
在windows下安装了git后，在开始菜单里找到Git Bash，弹出一个命令行的窗口，说明git安装成功了。

安装完成后，还需要最后一步设置，在命令行输入：
```
$ git config --global user.name "Your Name"
$ git config --global user.email "email@example.com"
```
因为git是分布式版本控制系统，因此每隔机器都必须自报家门：你的名字和Email地址。
注意`git config`命令的`--global`参数，用了这个参数，表示你这台机器上所有的Git仓库都会使用这个配置，当然也可以对某个仓库指定不同的用户名和Email地址。

## 创建版本库
版本库又名仓库，英文名**repository**，可以简单的理解成一个目录，这个目录里面的所有文件都可以被Git管理起来，每隔文件的修改、删除，Git都能跟踪，以便任何时刻都可以跟踪历史，或者“还原”。

1. 创建一个空目录`mygit`
2. 在git bash下进入该目录，执行`git init`，该命令把这个目录变成Git可以管理的**仓库**。执行该命令后会发现当前目录下多了一个.git目录，这个目录是Git用来跟踪管理版本库的，不能随便去改动它。
3. **把文件添加到版本库**
在mygit目录下添加test.txt,用notpadd++编辑一些文字在里面并保存。
执行命令`git add test.txt`，没有任何显示，Unix的哲学是“没有消息就是好消息”。
4. 用命令`git commit`告诉git，把文件提交到仓库。
```
$ git commit -m "add a test file"
```
`-m`后面输入的是本次提交的说明，可以输入任意内容，相当于svn的日志。

在windows下执行第3步时会出现<font color=red>`warning: LF will be replaced by CRLF`</font>,这是因为：windows中的换行符为 CRLF， 而在linux下的换行符为LF，解决的方法：

```
$ rm -rf .git	//删除之前生成的.git
$ git config --global core.autocrlf false  //禁用自动转换
```
然后重新执行：
```
$ git init
$ git add test.txt
```
[windows使用git时出现：warning: LF will be replaced by CRLF](http://blog.csdn.net/unityoxb/article/details/20768687)

## `git status`和`git diff`命令
`git status`可以告诉你有没有文件被修改过
`git diff`可以查看修改的内容：`git diff test.txt`

## 版本回退
每一次执行git commit时，git便会“保存一个快照”，这个快照在Git中被称为`commit`。

###`git log`命令，查看历史记录
可以看到git的历史记录显示四个字段的信息，分别是：版本号，作者，日期和用户提交的log信息.

```
$ git log
commit 1f627bd6c5c06d998a8dc457b5be7ddfc54dad24
Author: dhshen <609138259@qq.com>
Date:   Sun Sep 4 11:38:58 2016 +0800

    add a new line to test.txt

commit c7bfe9c46d83f5f4fcf9b1696ff6883e6af01c1e
Author: dhshen <609138259@qq.com>
Date:   Sun Sep 4 11:32:22 2016 +0800

    modify test.txt

commit 8e1e35cf649305f98bc3f13b0a5568cce50c17e9
Author: dhshen <609138259@qq.com>
Date:   Sun Sep 4 11:10:29 2016 +0800

    add test.txt to git
```
如果希望看到简略的日志信息，可以在后面加上<font color=red>`--pretty=oneline`</font>
```
$ git log --pretty=oneline
1f627bd6c5c06d998a8dc457b5be7ddfc54dad24 add a new line to test.txt
c7bfe9c46d83f5f4fcf9b1696ff6883e6af01c1e modify test.txt
8e1e35cf649305f98bc3f13b0a5568cce50c17e9 add test.txt to git
```
### `git reset`命令
在git中`HEAD`表示当前版本，上一个版本就是`head^`，上上个版本就是`HEAD^^`，以此类推。
**回退到上一个版本：** `git reset --hard HEAD^`

```
$ git reset --hard HEAD^
HEAD is now at c7bfe9c modify test.txt
```
**回退到指定版本号：** `git reset --hard commit-id`
版本号没必要写全，前几位就可以了，Git会自动去找。

Git的版本回退速度非常快，因为Git在内部有个指向当前版本的HEAD指针，当你回退版本的时候，Git仅仅是把HEAD从指向当前版本的指针改为指向指定版本而已，然后顺便把工作区的文件更新了。

Git提供了一个命令`git reflog`用来记录你的每一次命令,当我们回退了太多时在git log中找不到以前的版本号时可以使用此命令来查找。

总结：
1. 用`git log`可以查看提交历史，以便确定要回退到哪个版本
2. 要重返未来，用`git reflog`查看命令历史，以便确定要回到未来的哪个版本



## 工作区和暂存区 ##
Git和其它版本控制系统不同之处就是有暂存区的概念。
名词解释
* 工作区
	就是在电脑里能看到的目录，比如上面新建的mygit文件夹就是一个工作区。
* 版本库
	工作区有一个隐藏的目录.git，这个不算工作区，而是Git的版本库。
	Git的版本库里存了很多东西，其中最重要的就是称为stage或index的<font color=red>**暂存区**</font>，还有Git为我们自动创建的第一个分支master，以及指向master的HEAD指针。

前面我们把文件往Git版本库里添加的时候，是分为两步执行的：<font color=red>
1. 用`git add`命令把文件添加进去，**实际上就是把文件修改添加到暂存区！**
2. 用`git commit`提交更改，**实际上就是把暂存区的所有内容提交到当前分支！**
</font>

因为我们创建Git版本库时，Git自动为我们创建了唯一一个`master`分支，所以现在`git commit`就是往master分支上提交更改。
你可以理解为：需要提交的文件修改通通放到暂存区，然后一次性提交暂存区的所有修改。

![工作区和暂存区-廖雪峰的博客图](http://www.liaoxuefeng.com/files/attachments/001384907702917346729e9afbf4127b6dfbae9207af016000/0)


## 管理修改
为什么Git比其他版本控制系统更优秀？因为Git跟踪并管理的是修改，而非文件。
举个栗子：
1. 修改test.txt中的内容
2. 执行`git add test.txt`
3. 再次修改test.txt的内容
4. 执行`git commit`

最终的结果是第一次修改的内容得到了提交，而第二次修改的内容并没有被提交。
因为`git add`命令只是将第一次修改放入暂存区，第二次修改并没有执行`git add`命令，因此第二次修改不会放入暂存区，`git commit`只负责把暂存区的内容提交了。

提交后可以用<font color=red>`git diff HEAD -- test.txt`</font>命令查看工作区和版本库里面最新版本的区别。

## 撤销修改
`git checkout -- filename`命令可以丢弃文件在工作区的修改，分为两种情况：
1. 修改的文件还没有添加到暂存区，则文件恢复为当前版本中的状态。
2. 修改的文件已经添加到暂存区，则文件恢复成暂存区中的状态。

如果已经将修改的文件添加到了暂存区，可以使用命令`git reset HEAD filename`将暂存区的该文件撤销掉。

小结：
1. 丢弃工作区的所有修改：`git checkout -- filename`
2. 丢弃暂存区的修改：`git reset HEAD filename`

## 删除文件 ##
当删除工作区的某个文件时，git status会提示工作区与版本库不一致，因为删除也是一次修改操作。
1. 如果希望从版本库中也删除该文件，那么使用`git rm filename`删掉版本库中的该文件。
2. 如果是删错了，希望将误删文件恢复到最新版本，则执行`git checkout -- filename`

`git checkout`其实是用版本库里的版本替换工作区的版本，无论工作区是修改还是删除，都可以一键还原。

#远程仓库
Git是分布式版本控制系统，同一个Git仓库，可以分布到不同的机器上。怎么分布呢？最早，肯定只有一台机器有一个原始版本库，此后，别的机器可以“克隆”这个原始版本库，而且每台机器的版本库其实都是一样的，并没有主次之分。

实际情况往往是这样，找一台电脑充当服务器的角色，每天24小时开机，其他每个人都从这个“服务器”仓库克隆一份到自己的电脑上，并且各自把各自的提交推送到服务器仓库里，也从服务器仓库中拉取别人的提交。

完全可以自己搭建一台运行Git的服务器,但是一开始为了学习，我们可以使用GitHub。

GitHub是一个提供Git仓库托管服务的，只要注册GitHub账号，就可以免费获得Git远程仓库。

由于本地Git仓库和GitHub仓库之间的传输是通过SSH加密的，因此需要一点设置：
1. 创建SSH Key
	执行命令 `ssh-keygen -t rsa -C "youremail@something.com"`
	然后一路回车，通通都使用默认值即可。
	在用户主目录里(windows下为/c/User/用户/)找到.ssh目录，里面会有id_rsa和id_rsa.pub两个文件，这两个就是SSH Key的密钥对，id_rsa是私钥，不能泄露出去，id_rsa.pub是公钥。
2. 登陆GitHub，打开“Settings”，在“SSH and GPG keys”栏中点击“New SSH Key”，然后随便填写一个标题，将id_rsa.pub中的内容复制到Key那一栏种，然后点击“Add SSH Key”按钮添加SSH Key。

为什么GitHub需要SSH Key呢？
因为GitHub需要识别出你推送的提交确实是你推送的，而不是别人冒充的，就可以确认只有你自己才能推送。
GitHub允许添加多个Key，假设你有若干台电脑，就可以在每台电脑上往GitHub推送了。

## 添加远程库 ##
在Github上“New Repository”创建一个新的仓库。
在Repository name填入learngit，其他保持默认设置，点击“Create repository”按钮，就成功地创建了一个新的Git仓库。

目前在GitHub上的这个learngit仓库还是空的，GitHub提示我们，可以从这个仓库克隆出新的仓库，也可以把一个已有的本地仓库与之关联。(见创建成功后的页面)

根据git的提示，在本地的learngit仓库下运行如下命令
```
$ git remote add origin https://github.com/dhshen/learngit.git
也可以使用：
$ git remote add origin git@github.com:dhshen/learngit.git
使用https除了速度慢以外，还有个最大的麻烦是每次推送都必须输入口令，但是在某些只开放http端口的公司内部就无法使用ssh协议而只能用https.
```
本地仓库就关联了GitHub的远程库.

添加后，远程库的名字就是origin，这是Git默认的叫法，也可以改成别的，但是origin这个名字一看就知道是远程库。

接下来我们就可以把本地库的所有内容推送到远程库上：
```
$ git push -u origin master
```
在弹出的提示框中输入github的账户和密码本地库的内容就推送到远程库上去了。
用git push命令，实际上是把当前分支`master`分支推送到远程。

由于远程库是空的，我们第一次推送master分支时，加上`-u`参数，Git不但会把本地的master分支内容推送到远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令。

从现在起，只要本地做了提交，就可以通过命令
```
$ git push origin master
```
把本地master分支的最新修改推送至GitHub。

## 从远程库克隆 ##
上面讲的都是先有本地库，后有远程库的时候，如何关联远程库。假设我们从零开始，那么最好的方式是先创建远程库，然后从远程库克隆。

用**`git clone`**命令克隆一个远程库到本地库：
```
在本地用来放置克隆下来的库的目录下执行如下命令：
$ git clone git@github.com:dhshen/learngit.git
```
### SSH警告 ###
当第一次使用Git的clone或者push命令连接GitHub时，会得到一个警告：
```
The authenticity of host 'github.com (192.30.253.113)' can't be established.
RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'github.com,192.30.253.113' (RSA) to the list of known hosts.
```
这是因为SSH连接在第一次验证GitHub服务器的Key时，需要你确认GitHub的Key的指纹信息是否真的来自GitHub的服务器，输入yes回车即可。

# 分支管理 #
分支在实际中有什么作用呢？
假设你准备开发一个新功能，但是需要两周才能完成，第一周你写了50%的代码，如果立刻提交，由于代码还没写完，不完整的代码会导致别人不能干活了。如果等代码全部写完再提交一次，又存在丢失每天进度的巨大风险。

有了分支就不用怕了，你创建一个属于自己的分支，别人看不到，还继续在原来的分支上正常工作，而你在自己的分支上干活，想提交就提交，直到开发完毕后，再一次性合并到原来的分支上，这样既安全又不影响别人工作。

## 创建与合并分支 ##
一开始，master分支是一条线，Git用master指向最新的提交，再用HEAD指向master，就能确定当前分支，以及当前分支的提交点。每次提交，master分支都会向前移动一步。

当我们创建新的分支，例如dev时，Git新建了一个指针叫dev，指向master相同的提交，再把HEAD指向dev，就表示当前分支在dev上。从此，对工作区内容的修改和提交就是针对dev分支了，比如新提交一次后，dev指针往前移动一步，而master指针不变。

加入我们在dev上的工作完成了，就可以把dev合并到master上，Git怎么合并呢？最简单的方法就是直接把master指向dev的当前提交，就完成了合并。所以Git合并分支也很快！

### 创建分支 ###
```
$ git branch dev
```
### 切换分支 ###
```
$ git checkout dev
```
### 创建并切换到新分支 ###
```
$ git checkout -b dev
```
### 查看当前分支 ###
```
$ git branch	//会列出所有分支，当前分支前面会标一个*号
```

切换分支为dev后修改test.txt文件并提交到dev分支，再切换回master分支时工作区的文件是修改之前的状态。因为此时的分支状态为下图所示：
![image from liaoxuefeng.com](http://www.liaoxuefeng.com/files/attachments/001384908892295909f96758654469cad60dc50edfa9abd000/0)

现在，我们把dev分支的工作成果合并到master分支上：
### 合并分支 ###
```
$ git merge dev		//用于合并指定分支到当前分支
```
`git merge`是用于合并指定的分支到**当前分支！！！**

### 删除分支 ###
```
$ git branch -d dev
```

 因为创建、合并和删除分支非常快，因此Git鼓励使用分支完成某个人物，合并后再删掉分支，这和直接在master分支上工作效果是一样的，但是过程更加安全。

## 解决冲突 ##
