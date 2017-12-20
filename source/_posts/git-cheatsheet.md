---
title: git-cheatsheet
date: 2017-09-29 17:19:59
tags: [git]
categories: [文档留存]
---
Git的官方网站：http://git-scm.com

<!-- more -->

## 本地git与在线库关联步骤

{% codeblock lang:perl %}
    git init
    git add .
    git commit -m “init”                       #可将2,、3合并为 git commit -am 'xxx'（不太适用，del掉）
    git remote add origin 你的github仓库地址 
    git push --set-upstream origin master      #pull,push时将本地分支与远程分支关联
    git push -u origin master                  #第一次推送master分支的所有内容；此后使用命令git push origin master推送最新修改；
    git branch --set-upstream dev origin/dev   #将本地分支与远程分支关联
    #想维持树的整洁，方法就是：在git push之前，先git fetch，再git rebase
    git fetch origin master
    git rebase origin/master
    git push
{% endcodeblock %}

## 克隆并创建新分支

{% codeblock lang:perl %}
    git clone 你的github仓库地址
    git remote rename <old> <new>  #重命名
    git remote remove <name>       #删除已映射的远程仓库
    git checkout -b dev origin/dev #作用是checkout远程的dev分支，在本地起名为dev分支，并切换到本地的dev分支
    git branch dev origin/dev #分解步骤如下
    git checkout dev #分解步骤如下
{% endcodeblock %}

## 合并分支

{% codeblock lang:perl %}
    git checkout master     #切换回master分支
    git merge dev           #合并dev分支到master
    git merge --no-ff -m "merge with no-ff" dev    #--no-ff表示禁用Fast forward，加上-m参数，把commit描述写进去
    git branch -d dev       #删除dev分支

   #注：合并分支时，加上--no-ff参数就可以用普通模式合并，合并后的历史有分支，
   #能看出来曾经做过合并，而fast forward合并就看不出来曾经做过合并
{% endcodeblock %}
 
## 分支操作

1. 查看分支：`git branch`
2. 创建分支：`git branch <name>`
3. 切换分支：`git checkout <name>`
4. 创建+切换分支：`git checkout -b <name>`
5. 合并某分支到当前分支：`git merge <name>`
6. 删除分支：`git branch -d <name>`
7. 删除远程分支：`git push origin --delete <branch-name>`


> 注：如果要丢弃一个没有被合并过的分支，可以通过`git branch -D <name>`强行删除。

## 贮藏操作

修复bug时，我们会通过创建新的bug分支进行修复，然后合并，最后删除；

1. 当手头工作没有完成时，先把工作现场`git stash`一下，然后去修复bug，修复后，再`git stash pop`，回到工作现场（ untrack 的文件在stash后依然可见——即依然在工作空间中，不会被保存到stash镜像中，因此对 untrack 文件的修改不能通过 `stash pop` 恢复——因为该命令不暂存未track的文件）
2. 暂存所有修改——相当于 `git add .` 和 `git stash`（包括新添加的文件，即恢复到上一次commit的状态，新添加的文件在stash后不可见——已保存到stash的镜像中，只能通过`stash pop` 恢复，但是不能添加描述信息message） `git stash -a  或者 --all`
2. 暂存所有修改（同 -a， 但是可以添加message信息，方便区分各个stash镜像，最好使用这个命令）`git stash save -a  "some comment" 或者 --all`
3. 一是用`git stash apply`恢复，但是恢复后，stash内容并不删除，你需要用`git stash drop`来删除；
4. 另一种方式是用`git stash pop`，恢复的同时把stash内容也删了
5. 查看所有stash的镜像 `git stash list`
6. 清除所有用 `git stash clear`


> 注：`git stash save [-p|—patch] [-k|--[no-]keep-index] [-u|--include-untracked] [-a|—all] [-q|—quiet] [message]`
  + `-k|--keep-index` 表示 stash之后，所有对暂存区的改变会维持不变（比如你之前add 了一个file，提交之后，`git status`还是能够在暂存区看到你的 add）,如果是—no-keep-index的话，stash之后的状态就是`git reset —hard HEAD`。
  + `-u|--include-untracked` 会把没有记录到的文件也保存下来(比如你新建了一个文件，但是还没有git add，stash也会把这个文件保存下来)
  + `-a|—all` 会把忽略的文件也保存下来（.gitignore中的）
  + `-q|—quiet` 终端不打印输出
  + `message` 一个对这个stash的描述，如果执行`git stash list`,我们能够看到这个描述


## 推送到远程

1. 查看远程库信息，使用`git remote -v`；
2. 本地新建的分支如果不推送到远程，对其他人就是不可见的；
3. 从本地推送分支，使用`git push origin branch-name`，如果推送失败，先用`git pull`抓取远程的新提交；
4. 在本地创建和远程分支对应的分支，使用`git checkout -b branch-name origin/branch-name`，本地和远程分支的名称最好一致；
5. 建立本地分支和远程分支的关联，使用`git branch --set-upstream branch-name origin/branch-name`；
6. 从远程抓取分支，使用`git pull`，如果有冲突，要先处理冲突。

## 设置版本标签

1. 命令`git tag <name>`用于新建一个标签，默认为HEAD，也可以指定一个commit id；
2. `git tag -a <tagname> -m "blablabla..."`可以指定标签信息；
3. `git tag -s <tagname> -m "blablabla..."`可以用PGP签名标签；
4. 命令`git tag`可以查看所有标签。
5. 命令`git push origin <tagname>`可以推送一个本地标签；
6. 命令`git push origin --tags`可以推送全部未推送过的本地标签；
7. 命令`git tag -d <tagname>`可以删除一个本地标签；
8. 命令`git push origin :refs/tags/<tagname>`可以删除一个远程标签。如果要删除一个远程标签，要先删除本地标签再用此命令删除远程标签

## 本地重新映射Git远程仓库地址

### 方法一、通过命令直接修改远程地址

1. 进入项目根目录，比如：`vue-patient`
2. `git remote` 查看所有远程仓库， `git remote xxx` 查看指定远程仓库地址
3. `git remote set-url origin http://0.0.0.0:8080/path/git_project_new.git`

### 方法二、通过命令先删除再添加远程仓库

1. 进入项目根目录，比如：`vue-patient`
2. `git remote` 查看所有远程仓库， `git remote xxx` 查看指定远程仓库地址
3. `git remote rm origin`
4. `git remote add origin http://0.0.0.0:8080/path/git_project_new.git`

### 方法三、直接修改配置文件

1. 进入`vue-patient/.git`
2. `vim config`
3. 修改如下示例中`[remote “origin”]`的`url=`为新的地址即可。

```
[core]
        repositoryformatversion = 0
        filemode = true
        bare = false
        logallrefupdates = true
        ignorecase = true
        precomposeunicode = true
[remote "origin"]
        url = git@github.com:freeser/Monitor.git
        fetch = +refs/heads/*:refs/remotes/origin/*
[branch "master"]
        remote = origin
        merge = refs/heads/master
```
