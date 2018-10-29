---
title: git常用命令总结
date: 2016-03-28T21:16:58+08:00
tags: ["Git学习"]
categories: ["Tool"]
permalink: Git-command-summary
description: git常用命令总结
---
　　本文持续更新...
　　虽然很早就记录了很多的git命令在OneNote里面，但是作为手残党+健忘的我来说，敲命令行简直..那个啥。所以在开头推荐大家一款能够[鼠标点点点的git工具](https://github.com/gitextensions/gitextensions/releases/tag/v2.48.05)，哪里不会点哪里，妈妈再也不用担心我记不住git命令啦！

`git init`  初始化git管理仓库

`git add xxx.txt`  将文件添加到暂存区中，可以单独添加文件，也可以使用“ git add . ”添加所有的未添加文件(“.”代表所有)。

`git commit -m "提交说明"`   提交暂存区中添加的文件到本地git库中。  题外话(注意，一定要写提交说明，而且要细粒度的提交，让工作更加细致)。

`git status`	查看当前仓库的状态，可以看详细的提示信息。Git status只能看到当前的仓库状态，当文件修改时，只能提示当前的文件修改了但是具体做了什么修改他是不知道的。

`git diff`	可以看到具体修改了什么内容<!--more-->

`git log`	查看历史记录，加上"--pretty=oneline"  可以显示简要信息。

`git reset`	可以回退到以前的版本，回退的时候需要看提交信息，git中使用HEAD表示当前的版本，相对上一个版本就是HEAD^，上上一个版本就是HEAD^^。比如要回退到上上个版本就要加入git reset --head HEAD^^

`git reset --head [commit id]`	这个命令是为了回退指定的版本，其中commit id是需要返回的版本的提交id，需要获得commit id执行下面的命令。

`git reflog`	 记录着每一次的commit信息。

`git diff HEAD --[file name]`	可以查看工作区(就是你的项目文件夹)和版本库里面该文件的区别

`git checkout --[file name]`可以让指定的文件恢复到上一次add或者commit时的状态，也就是丢弃工作区的修改。(也就是刚才执行了add命令，那么回复的就是暂存区的版本，如果执行了commit命令，那就是回复的git库的版本)

`git reset HEAD [file name]`如果你想要丢弃当前工作区的修改，但是已经添加到暂存区了，那么就是该命令，丢弃暂存区修改。

`git rm [file name]`	删除掉版本库的文件，删除后执行commit命令，文件就从版本库中删除了。  如果删错了，执行checkout命令可以把版本库中的文件恢复。

`git push origin master`	把本地库推送到远程git库里面。如果是第一次提交就需要加入"-u"：git push -u origin master，以后推送就可以直接git push origin master (origin表示的是远程库的默认名称，master并不是指定的，需要将本地哪个分支push就写哪个分支的名字，git会自动的将相应的分支对应起来)

`git clone [git address]`	将远程git库的文件克隆到本地。
 
`git checkout -b [new branch]`	创建并且切换到一个新的分支。(-b的表示创建并切换，相当于执行了两条命令：git branch [new branch]   >>  git checkout [new branch]  );

`git branch`	列出所有分支，当前分支前面会加*号表示。

`git merge [branch name]`	用于把指定的分支合并到当前的分支；注意：需要先切回master分支然后把指定的分支合并到master分支中。

`git branch -d [branch name]`	删除指定的分支。(合并后就可以删除之前的分支了)

`git checkout [branch name]`	切换到指定的分支

`git log --graph`	可以查看分支合并图

`git stash`	将当前的工作区内容临时储藏起来(临时需要去修改别的一个文件，但是不想提交当前的内容时使用。)

`git stash list`	查看当前隐藏的所有工作区内容

`git stash apply "[stash list param]"`	恢复stash的工作区内容，但是不删除stash内容，“”引号中的参数可以恢复指定的stash内容。参数在stash list命令执行后内容的行首

`git stash drop`	删除stash内容

`git stash pop` 	恢复stash内容的同时删除内容

`git branch -D [branch name]`	强制删除一个还没有合并的分支。

`git remote -v`	可以查看远程库的详细信息。

`git tag [tag name]`	给当前的分支创建标签，首先需要切换到需要打标签的分支上面。

`git tag`	可以查看当前的分支上面的所有的标签列表

`git show [tag name]`	可以查看标签的详细信息

`git tag -a [tag name] -m "标签说明"`	可以打上带有说明信息的标签。

`git tag -d [tag name]`	删除本地的标签

`git push origin :refs/tags/[tag name]`	可以删除远程库中的标签

`git push origin [tag name]`	推送某一个标签到远程库中

`git push origin --tags`	可以把所有没有推送的标签推送到远程库。

`git config --global color.ui true`	可以让git命令显示颜色

