# git的主要使用

摘自[廖雪峰的官方教程](https://www.liaoxuefeng.com/wiki/896043488029600)
***

## 1.vscode提供的git插件对.git中文件状态的定义

> A: 你本地新增的文件（服务器上没有）.
> C: 文件的一个新拷贝.
> D: 你本地删除的文件（服务器上还在）.
> M: 文件的内容或者mode被修改了.
> R: 文件名被修改了。
> T: 文件的类型被修改了。
> U: 文件没有被合并(你需要完成合并才能进行提交)。
> X: 未知状态(很可能是遇到git的bug了，你可以向git提交bug report)。

***

## 2.部分常用命令

\# 将该文件夹转为.git仓库
>git init  

\# 注册
>git config user.name "liujianghang"
>git config user.email "1178262698@qq.com"

\# 生成ssh密钥
>ssh-keygen -t rsa -C "1178262698@qq.com"

\# 本地仓库状态
>git status  

\# 加入到stage
>git add file  

\# 提交
>git commit -m "version"  

\# 简要log
>git log --pretty=oneline

\# 版本回退
>\# 在工作区做了修改，并未添加到暂存区，想撤销工作区的修改
git restore file  
\# 在工作区做了修改，并用git add 添加到了暂存区，未提交；想撤销。1.先撤销暂存区的修改，用  2.然后参考情况1撤销工作区的修改；
>git restore --staged file
\# 在工作区做了修改，且git add git commit添加并提交了内容，想撤销本次提交，直接用回退版本，即可保证工作区，暂存区，版本库都是上次的内容
>git reset --hard HEAD^
\# 回退一个版本
>git reset --hard a45a  # 退回到某个版本 git reset HEAD^  

\# 推送
>git push -u origin master  
>git push  # 直接推送

\# 远程库
>git remote -v  
>git remote rm origin # 删除关联
>git remote add origin git@github.com:liujianghang/learngit.git # 与远程产生关联

\# 克隆库
>git clone git@github.com:liujianghang/gitskills.git  

\# HEAD指向的是目前正在工作的分支
>git branch -r  # 查看远程分支
>git branch  # 查看本地分支
>git branch newbranch # 创建分支  
>git switch dev  # 切换分支  
>git switch -c featurel \ 创建并切换
>git merge dev # 命令用于合并指定分支到当前分支
>git branch -d dev  # 删除分支

***

**解决分支冲突（多个分支分别add和commit了，无法快速合并）时我们可以保留其中一种修改，或者两种都保留，将冲突符号去除，以及留下需要保留的。**
\# 存储当前工作（所有add 和 commit全部被存储）
>git stash  
>git stash list
>git stash drop # 删除
>git stash pop # 恢复工作并删除
>git stash apply stash@{0} # 恢复指定stash

\# 标签
>git tag v1.0 # 打一个新标签，默认打在最新提交的commit上
>git tag # 查看所有标签
>git tag v0.9 f52c633 # 给某个commit加标签
>git show \<tagname\> # 查看标签信息
>git tag -a v0.1 -m "version 0.1 released" 1094adb # 打标签并增加说明
>git tag -d v0.1 # 删除标签
>git push origin v1.0 # 标签推送到远程 git push origin --tags

\# 配置命令别名
>git config --global alias.st status # 用st代替status
>git config --global alias.lg “log --graph --pretty=oneline --abbrev-commit” # 用来lg代替打印log

***

## 3.部分进阶说明

为了方便操作，Git专门提供了一个**cherry-pick命令**，让我们能复制一个特定的提交到当前分支
切换到当前分支，如：

>git cherry-pick 4c805e2

git自动给dev分支做了一次提交，注意这次提交的commit是1d4b803，它并不同于master的4c805e2，因为这两个commit只是改动相同，但确实是两个不同的commit。

**多人协作**的工作模式通常是这样：

首先，可以试图用git push origin \<branch-name>推送自己的修改；
如果推送失败，则因为远程分支比你的本地更新，需要先用git pull试图合并；
如果合并有冲突，则解决冲突，并在本地提交；
没有冲突或者解决掉冲突后，再用git push origin \<branch-name>推送就能成功！
如果git pull提示no tracking information，则说明本地分支和远程分支的链接关系没有创建，用命令git branch --set-upstream-to \<branch-name> origin/\<branch-name>。
这就是多人协作的工作模式，一旦熟悉了，就非常简单。

查看远程库信息，使用git remote -v；
本地新建的分支如果不推送到远程，对其他人就是不可见的；
从本地推送分支，使用git push origin branch-name，如果推送失败，先用git pull抓取远程的新提交；
在本地创建和远程分支对应的分支，使用git checkout -b branch-name origin/branch-name，本地和远程分支的名称最好一致；
建立本地分支和远程分支的关联，使用git branch --set-upstream branch-name origin/branch-name；
从远程抓取分支，使用git pull，如果有冲突，要先处理冲突。

git有commit，为什么还要引入tag？
“请把上周一的那个版本打包发布，commit号是6a5819e...”
“一串乱七八糟的数字不好找！”
如果换一个办法：
“请把上周一的那个版本打包发布，版本号是v1.2”

加上你自己定义的文件，最终得到一个完整的**.gitignore**文件，以下是不会被上传的文件
最后一步就是把.gitignore也提交到Git，就完成了！当然检验.gitignore的标准是git status命令是不是：**working directory clear。**
**下面是一个.gitignore的例子**  

>\# Windows
Thumbs.db
ehthumbs.db
Desktop.ini

>\# Python
*.py[cod]
*.so
*.egg
*.egg-info
dist
build

>\# My configurations
db.ini
deploy_key_rsa

***

## 4.debug

**1.hint：储存了库中的git储存库**

**resolution：**

>rm -rf folder1/.git
git rm --cached folder1
git add folder1  