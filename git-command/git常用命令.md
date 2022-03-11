# 常用命令大全

## 前言
> [!TIP]
> 感觉有些命令平时用不上，真当要用的时候又得去各大博客查找，索性就自己根据阮一峰的命令大全进行整理，以后好查找，其它命令用到后会记录到此文当中。
> 转自: https://www.ruanyifeng.com/blog/2015/12/git-cheat-sheet.html

## 基本关联流程
```git
# 初始化本地仓库
git init

# 添加本地文件
git add .

# 提交到本地仓库
git commit -m"xxx"

# 关联远程
git remote add origin 你的github仓库地址

# 推送到远程
git push origin 分支
```



## 多账号配置
```git
# 配置较麻烦，就直接拿简书上大佬的文章了
https://www.jianshu.com/p/3a4b0a1bec13
```


## 初始与新建
```git
# 在当前目录新建一个Git代码库
git init

# 新建一个目录，将其初始化为Git代码库
git init [project-name]

# 下载一个项目和它的整个代码历史
git clone [url]
```

## 配置
```git
# 显示当前的Git配置
git config --list

# 编辑Git配置文件
git config -e [--global]

# 设置提交代码时的用户信息
git config [--global] user.name "[name]"
git config [--global] user.email "[email address]"
```

## 添加/删除文件
```git
# 添加指定文件到暂存区
git add [file1] [file2] ...

# 添加指定目录到暂存区，包括子目录
git add [dir]

# 添加当前目录的所有文件到暂存区
git add .

# 添加每个变化前，都会要求确认, 对于同一个文件的多处变化，可以实现分次提交
git add -p

# 删除工作区文件，并且将这次删除放入暂存区
git rm [file1] [file2] ...

# 停止追踪指定文件，但该文件会保留在工作区
git rm --cached [file]

# 改名文件，并且将这个改名放入暂存区
git mv [file-original] [file-renamed]
```


## 分支
```git
# 列出所有本地分支
git branch

# 列出所有远程分支
git branch -r

# 列出所有本地分支和远程分支
git branch -a

# 新建一个分支，但依然停留在当前分支
git branch [branch-name]

# 新建一个分支，并切换到该分支
git checkout -b [branch]

# 新建一个分支，指向指定commit
git branch [branch] [commit]

# 新建一个分支，与指定的远程分支建立追踪关系
git branch --track [branch] [remote-branch]

# 切换到指定分支，并更新工作区
git checkout [branch-name]

# 切换到上一个分支
git checkout -

# 建立追踪关系，在现有分支与指定的远程分支之间
git branch --set-upstream [branch] [remote-branch]

# 合并指定分支到当前分支
git merge [branch]

# 选择一个commit，合并进当前分支
git cherry-pick [commit]

# 删除分支
git branch -d [branch-name]

# 删除远程分支
git push origin --delete [branch-name]
git branch -dr [remote/branch]
```


## 标签
```git
# 列出所有tag
git tag

# 新建一个tag在当前commit
git tag [tag]

# 新建一个tag在指定commit
git tag [tag] [commit]

# 删除本地tag
git tag -d [tag]

# 删除远程tag
git push origin :refs/tags/[tagName]

# 查看tag信息
git show [tag]

# 提交指定tag
git push [remote] [tag]

# 提交所有tag
git push [remote] --tags

# 新建一个分支，指向某个tag
git checkout -b [branch] [tag]
```

## 查看
```git
# 显示有变更的文件
git status

# 显示当前分支的版本历史
git log

# 显示commit历史，以及每次commit发生变更的文件
git log --stat

# 搜索提交历史，根据关键词
git log -S [keyword]

# 显示某个commit之后的所有变动，每个commit占据一行
git log [tag] HEAD --pretty=format:%s

# 显示某个commit之后的所有变动，其"提交说明"必须符合搜索条件
git log [tag] HEAD --grep feature

# 显示某个文件的版本历史，包括文件改名
git log --follow [file]
git whatchanged [file]

# 显示指定文件相关的每一次diff
git log -p [file]

# 显示过去5次提交
git log -5 --pretty --oneline
git log -5 --pretty=oneline

# 显示所有提交过的用户，按提交次数排序
git shortlog -sn

# 显示指定文件是什么人在什么时间修改过
git blame [file]

# 显示暂存区和工作区的差异
git diff

# 显示暂存区和上一个commit的差异
git diff --cached [file]

# 显示工作区与当前分支最新commit之间的差异
git diff HEAD

# 显示两次提交之间的差异
git diff [first-branch]...[second-branch]

# 显示今天你写了多少行代码
git diff --shortstat "@{0 day ago}"

# 显示某次提交的元数据和内容变化
git show [commit]

# 显示某次提交发生变化的文件
git show --name-only [commit]

# 显示某次提交时，某个文件的内容
git show [commit]:[filename]

# 显示当前分支的最近几次提交
git reflog
```

## 远程同步
```git
# 下载远程仓库的所有变动
git fetch [remote]

# 显示所有远程仓库
git remote -v

# 显示某个远程仓库的信息
git remote show [remote]

# 增加一个新的远程仓库，并命名
git remote add [shortname] [url]

# 取回远程仓库的变化，并与本地分支合并
git pull [remote] [branch]

# 上传本地指定分支到远程仓库
git push [remote] [branch]

# 强行推送当前分支到远程仓库，即使有冲突
git push [remote] --force

# 推送所有分支到远程仓库
git push [remote] --all
```

## 撤销
```git
# 恢复暂存区的指定文件到工作区
git checkout [file]

# 恢复某个commit_id的指定文件到暂存区和工作区
git checkout [commit_id] [file]

# 恢复暂存区的所有文件到工作区
git checkout .

# 重置暂存区的指定文件，与上一次commit_id保持一致，但工作区不变
git reset [file]

# 重置暂存区与工作区，与上一次commit_id保持一致
git reset --hard

# 重置当前分支的指针为指定commit_id，同时重置暂存区，但工作区不变
git reset [commit_id]

# 重置当前分支的HEAD为指定commit_id，同时重置暂存区和工作区，与指定commit_id一致
git reset --hard [commit_id]

# 重置当前HEAD为指定commit_id，但保持暂存区和工作区不变
git reset --keep [commit_id]

# 新建一个commit_id，用来撤销指定commit_id
# 后者的所有变化都将被前者抵消，并且应用到当前分支
git revert [commit_id]

# 暂时将未提交的变化移除，稍后再移入
git stash
git stash pop
```
> [!TIP|style:callout|label:注意 |iconVisibility:hidden]
> 撤回应结合历史提交记录，应先查看历史提交记录，通过历史提交记录ID来确定应撤回哪个版本
> ```git
> # 查看历史提交记录，如历史提交记录过多可进行指定条数 --5代表最近5条
> git log --pretty=oneline
> git log -5 --pretty=oneline
> 
> # 撤回 [commit_id]为查询的提交记录id
> git revert [commit_id]
> ```



