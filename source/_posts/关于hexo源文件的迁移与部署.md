---
title: 关于hexo源文件的迁移与部署
date: 2018-04-17 07:40:49
tags: 博客
categories: 搭建博客
top:
---

> 为解决意外情况hexo源文件丢失的情况，采用在GitHub的建站仓库上创建一个额外的分支来存放源文件的方法。


### 一.初次搭建hexo静态博客
1. 创建仓库：[change-Y.gitHub.io](https://change-Y.gitHub.io)；
2. 新建一个Readme（不然可能由于空仓库无法进行下边的新建分支操作）；
3. 新建一个分支hexo用来存放hexo源文件，并设为默认分支（master用来存放静态页面，我们今后只需要对hexo分支进行操作即可）；
4. 通过git clone 将change-Y.github.io克隆到本地；
5. 在本地change-Y.github.io文件夹下执行`hexo init`、`npm install` 和 `npm install hexo-deployer-git`（在此步骤操作过程中可能会初始化hexo失败，提示change-Y.github.io不是空的文件夹，删了文件夹下隐藏的.git文件夹依然不可以，于是将.git文件夹备份，新建了一个空文件夹，待初始化完以后将.git放入文件夹。）；
6. 修改_config.yml中的deploy参数，分支应为master，可在type参数下新建参数与branch: master(因为此前设置的hexo为默认分支)；
7. 依次执行`git add .`、`git commit -m "..."`、`git push origin hexo`提交网站相关的文件；
8. 执行`hexo g -d`生成网站并部署到GitHub上。

### 二.日常的改动流程

1. 提交源文件到hexo分支，依次执行`git add .`、`git commit -m "..."`、`git push origin hexo`指令将改动推送到GitHub（此时当前分支应为hexo）；
2. 部署`hexo，hexo g -d`

### 三、本地资料还在，重装了系统

1. 安装nodejs、git、hexo。安装hexo时如遇到权限错误可在安装命令前加sudo。
2. 设置用户名和邮箱，关联远程仓库和添加公钥：
3. ``git config --global user.name` "你的GitHub用户名"`
4. `git config --global user.email "你的GitHub注册邮箱"`
5. `$ ssh-keygen -t rsa -C "你的GitHub注册邮箱"`
6. 将生成的公钥添加到GitHub的个人设置里。
7. `$ ssh -T git@github.com`
8. 如果测试绑定成功就可以在hexo文件夹里执行命令了。

### 四、本地资料丢失后的流程

1. 安装nodejs、git、hexo。安装hexo时如遇到权限错误可在安装命令前加sudo。
2. 设置用户名和邮箱，关联远程仓库和添加公钥，参考[手把手教从零开始在GitHub上使用Hexo搭建博客教程(一)-附GitHub注册及配置](https://www.jianshu.com/p/f4cc5866946b)
2. 使用git clone git@github.com:change-Y/change-Y.github.io.git拷贝仓库（默认分支为hexo）；
3. 在本地新拷贝的change-Y.github.io文件夹下通过Git bash依次执行下列指令：`npm install hexo`、`npm install`、`npm install hexo-deployer-git`（记得，不需要hexo init这条指令）。

-----
部分内容摘自知乎[CrazyMilk的回答](https://www.zhihu.com/question/21193762)，感谢！
