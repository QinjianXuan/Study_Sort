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


- 分支管理模型图例
![git分支模型图](/images/git-model@2x.png)


上图出现了feture branches、develop、release branches、hotfixes、master总共五种分支
下面详细解析各个分支作用和用法