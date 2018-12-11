## Git分支管理模型
-------
###  1.介绍一些git常用的命令

```
//配置用户名和邮箱
//说明：git config命令的--global参数，用了这个参数，表示你这台机器上所有的Git仓库都做了该配置。但是对于公司的//项目你想要配置公司的邮箱，那么你可以在公司的版本控制目录下去掉--global参数对该单个项目进行重新配置！
git config --global user.name "Your Name"
git config --global user.email "email@gmail.com"

//生成 public/private rsa key pair
ssh-keygen -t rsa -C "your_email@gmail.com"
//粘贴ssh key
//mac
pbcopy < ~/.ssh/id_rsa.pub
//win
clip < ~/.ssh/id_rsa.pub


//初始化git
git init
//将README.MD添加到git版本控制中
git add README.md
//将目前为止有改动的并且已经添加到版本库中农的文件提交到版本库
git commit -m "commit description"
//给本地版本库添加一个远程仓库的地址
git remote add origin https://github.com/xxx/xxx/xxx.git
//将本地master分支推送至远端的仓库
git push -u origin master

//从当前所在分支切出一个名为develop的分支
//不加-b就是直接切换分支，如果没有该分支，命令行会提醒你de
git checkout -b develop
//检查项目中是都有未进行版本控制和已修改的文件
git status
//将项目当前目录下所有文件提交到暂存区
git add .
//提交暂存区里的代码到本地库
git commit -m "commit description"
//同步本地分支到远程仓库，如果远端没有develop会自动创建
git push origin develop
```

### 2.推荐博客


> * 国外行家Vincent Driessen的《A Successful Git branching model》
博客地址：[a-successful-git-branching-model](https://nvie.com/posts/a-successful-git-branching-model/)
> * 阮一峰老师的《Git分支管理策略》
博客地址：[Git分支管理策略](http://www.ruanyifeng.com/blog/2012/07/git.html)

### 3.图例与详解


#### 1. 分支管理模型图例
![git分支模型图](/Git相关/images/git-model@2x.png)


上图出现了feture branches、develop、release branches、hotfixes、master总共五种分支
下面详细解析各个分支作用和用法：

##### 主要分支
主要分支包括两个主要分支master和develop，生命周期贯穿整个项目过程
1. Master分支


在主要分支中，master是在我们运行git init 之后就会默认生成的分支，所以我们的其他所有分支的起点应该都是从master分支check出去的。另外我们通常也正是用这个master分支也用来记录我们的每一个生产版本，所以每一次合并其他分支到mater分支时，都应该非常的谨慎
    
2. devleop分支


在主要分支中，develop是开发分支，是从master切出来的，当开发完成之后将其合并回master，然后打出一个生产包，再用tag标记一个版本的发行。
master与develop分支的关系图：
![master与develop关系图](/Git相关/images/main-branches@2x.png)


##### 临时分支
临时分支分为三种feature branches、hotfixes、release branches。临时分支具有一定的时效性。

1.feature（功能分支）添加时创建此分支，完成功能合并到develop后删除。大致流程如下：


![feature分支示意图](/Git相关/images/fb@2x.png)

在开发过程中会用到的git代码
```
//从develop切出一个新的分支命名为feature_name
git checkout -b feature_name develop
//检查项目中是否有未进行版本控制和已修改的文件
git status
//将项目当前目录下所有文件添加到暂存区
git add .
//提交暂存区里的代码到本地仓库
git commit -m "commit description"
//提交代码到临时分支，并推送到远程仓库进行管理
git push origin feature_name
```
在功能分支开发完成之后
```
//切换回develop
git checkout develop
//将feature_name分支合并到develop
git merge --no-ff feature_name
//删除本地的feature_name分支
git branch -d feature_name
//删除远程的feature_name分支
git push origin --delete feature_name
//将develop分支推送到远程仓库
git pull origin develop
git push origin develop
```

2.release分支（预发布分支）


![release分支示意图](/Git相关/images/640.webp)

release分支可能会用的git指令
```
//首先从develop分支切出release1.0.0分支
git checkout -b release-1.0.0
//接着修复bug检查、添加到暂存区、提交到本地仓库
git status
git add .
git commit -m "commit description"
//确认release-1.0.0没有问题了，将分支合并到master分支
git checkout master
git merge --no-ff release-1.0.0
//在分支上打tag
git tag -a v1.0.0 -m "release v1.0.0"
# -a:后面跟的是标签名
# -m:后面跟的是该标签的说明（一般可以不用加-m）

//推送master分支和tag到远程
git pull origin master
git push origin master
git push --tags

//合并分支到develop
git checkout develop
git merge --no-ff release-1.0.0

//最后可以删除分支然后推送develop到远程仓库
git branch -d release-1.0.0
git pull origin develop
git push origin develop

```

hotfix（bug修复分支）

![hotfix分支流程图](/Git相关/images/hotfix-branches@2x.png)

hotfix分支可能会用到的git指令
```
//首先从master分支切出hotfix-1.0.1
git checkout master
git checkout -b hotfix-1.0.1

//修复完成后检查、添加到暂存区、提交到本地仓库
git status
git add .
git commit -m "commit description"

//确认hotfix-1.0.1的分支测试通过，将分支合并到master
git checkout master
gite merge --no-ff hotfix-1.0.1

//在master分支上打tag
git tag -a v1.0.1 -m "hotfix-v1.0.1"
# -a:后面跟的是标签名
# -m:后面跟的是该标签的说明（一般可以不用加-m）

//推送master分支和tag到远程仓库
git pull origin master
git push origin master
git push --tags

//合并分支到develop
git checkout develop
git merge --no-ff hotfix-1.0.1

//最后删除hotfix-1.0.1分支然后推送develop到远程仓库
git branch -d hotfix-1.0.1
git pull origin develop
git push origin develop
```

##### merge的三种模式

![merge的三种模式](/Git相关/images/merge.webp)

1、git merge feature  
默认fast-forward 模式，如上图最右边的分支合并，不是没有合并，develop分支的HEAD 直接指给了feature分支的HEAD 然后就呈现了这样的状态，但这种合并是有条件的——当我们从develop分支切出feature分支开始，到feature分支开发完合并到develop分支之前，develop分支都不能有新的提交，才会使用fast-forward模式进行合并。否则即使你用这个命令也会让你以第二种模式合并。

2、git merge --no-ff feature
--no-ff ，看着命令再结合前面提到的默认模式。基本已经猜出其意思了，就是强行关闭fast-forward。如上图中中间的哪一组分支合并图。当我们使用这种模式进行合并的时候，它就会创建出一个新的合并分支节点作为当前分支的HEAD。用这种方式，我们能够更清晰看到分支完成的内容和分支的起止时间。所以更多的时候我们是推荐使用这种合并方式的。

注：当我们在命令行里执行了上面的合并命令后，会弹出上图所示内容。这是通过Vim去修改本次提交的备注。它默认给的是Merge branch 'feature' into develop 一般我都用默认，不修改，直接输入：wq然后回车就保存退出了

3、git merge --squash feature
这个模式是将feature分支上的左右的改动提交都合成一个点作为develop的一次commit。通常我们使用的场景是如果我们切出一个分支来修复bug 然后出现了很多的补充提交和小改动的提交，看起来这些都很冗余没有必要，那么我们就可以用这种模式。该模式提交完了的最终形态有点像fast-forward模式。并且它不同于上面两种模式的一点是，你执行了这个命令之后，只是把 feature分支上的所有改动都移植到了develop分支上的相应文件上。接下来我们还需要自己再手动 add . --> commit 一次。