---
typora-root-url: ../
---

[TOC]



git基本操作 https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000
========================



### git基本概念

​				

![](/.images/1766480014.jpeg)

![git æ°æ®æµç¨å¾ç¤ºæå¾](/版本控制工具/assets/2429e4d2661e60027537aea0077f6e40.png)


#### 1. 工作区（Working Directory）  https://blog.csdn.net/qq_32452623/article/details/78417609
    就是电脑中通过git init初始化的目录
#### 2. 版本库（Repository）
    工作区有一个隐藏目录.git，这个不算工作区，而是Git的版本库
#### 3. HEAD https://blog.csdn.net/qq_32452623/article/details/79415990

    $ cat .git/HEAD 
    ref: refs/heads/dev
    
    $ cat .git/refs/heads/dev 
    85b855cab4ed50ba250d2f210f0df8a4707e77c1

### git基本操作

#### 1. git init
    将当前目录初始化为git版本库
    eg: git init
#### 2. git add [file]
    将指定文件添加到仓库，file为.则将所有修改或创建的文件添加到仓库
    eg: git add README.txt, git add .
#### 3. git commit
    提交对仓库的修改，创建一个仓库的新版本
    eg: git commit -m "first commit"
#### 4. git status
    查看仓库的状态，显示git add的文件，与修改过没有add的文件
    eg: git status
#### 5. git diff [file]
    查看add到仓库中的版本，与当前的版本之间的不同，不加路径则查看所有不同
    eg: git diff README.txt, git diff
| 命令             | 作用             |
| ---------------- | ---------------- |
| git diff         | 工作区 vs 暂存区 |
| git diff head    | 工作区 vs 版本库 |
| git diff –cached | 暂存区 vs 版本库 |

#### 6. git log

    查看仓库的所有版本，即执行过git commit过后创建的所有版本
    显示版本id，作者，时间，描述
    --pretty=oneline参数指定为一行显示日志
    只显示版本id与描述
    eg: git log，git log --pretty=oneline
#### 7. git reset --hard 版本
    回退到指定仓库的版本。
    在Git中，用HEAD表示当前版本，上一个版本就是HEAD^，上上一个版本就是HEAD^^，上100个版本HEAD~100
    也可以用版本id指定要返回的版本
    eg: git reset --hard HEAD^，git reset --hard 3628164
#### 8. git reflog
    查看对仓库的所有commit与reset操作
    显示版本id，操作，描述
    eg: git reflog
#### 9. git checkout -- [file]
    丢弃修改，工作区回到最后一次add的状态
    eg: git checkout -- readme.txt
#### 10.git rm [file]
    工作区中的文件删除后，通过此命令将暂存区的文件也删除。
    eg: git rm readme.txt
#### 11.git stash  https://blog.csdn.net/daguanjia11/article/details/73810577

​	我们有时会遇到这样的情况，正在dev分支开发新功能，做到一半时有人过来反馈一个bug，让马上解决，但是新功能做到了一半你又不想提交，这时就可以使用git stash命令先把当前进度保存起来，然后切换到另一个分支去修改bug，修改完提交后，再切回dev分支，使用git stash pop来恢复之前的进度继续开发新功能。下面来看一下git stash命令的常见用法

```
git stash
保存当前工作进度，会把暂存区和工作区的改动保存起来。执行完这个命令后，在运行git status命令，就会发现当前是一个干净的工作区，没有任何改动。使用git stash save 'message...'可以添加一些注释

git stash list
显示保存进度的列表。也就意味着，git stash命令可以多次执行。

git stash pop [–index] [stash_id]
git stash pop 恢复最新的进度到工作区。git默认会把工作区和暂存区的改动都恢复到工作区。
git stash pop --index 恢复最新的进度到工作区和暂存区。（尝试将原来暂存区的改动还恢复到暂存区）
git stash pop stash@{1}恢复指定的进度到工作区。stash_id是通过git stash list命令得到的 
通过git stash pop命令恢复进度后，会删除当前进度。
git stash apply [–index] [stash_id]
除了不删除恢复的进度之外，其余和git stash pop 命令一样。

git stash drop [stash_id]
删除一个存储的进度。如果不指定stash_id，则默认删除最新的存储进度。

git stash clear
删除所有存储的进度。
```

### git分支操作
#### 1. git checkout -b [分支名]
    创建并切换分支
    eg: git checkout -b dev
#### 2. git branch [分支名]
    创建分支
    eg: git branch dev
#### 3. git checkout [分支名]

    切换分支
    eg: git checkout dev
#### 4. git merge/rebase [分支名]   https://blog.csdn.net/wh_19910525/article/details/7554489
    合并分支，合并指定分支到当前分支
    eg: git merge/rebase dev
    解决冲突
    在rebase的过程中，也许会出现冲突(conflict). 在这种情况，Git会停止rebase并会让你去解决 冲突；在解决完冲突后，用"git-add"命令去更新这些内容的索引(index), 然后，你无需执行 git-commit,只要执行:
    $ git rebase --continue
    这样git会继续应用(apply)余下的补丁。
    在任何时候，你可以用--abort参数来终止rebase的行动，并且"mywork" 分支会回到rebase开始前的状态。
    $ git rebase --abort
merge和  rebase区别



![img](/版本控制工具/assets/1339683149_4793.jpg)

#### 5. git branch -d [分支名]

    删除分支
    eg: git branch -d dev
    -D  强制删除   分去没有合并出去时 -d会失败
#### 6. git branch
    查看分支，显示所有的分支与当前的分支
    eg: git branch

#### 7.git branch -r

查看远程库所有分支

#### 8. git branch -a

查看远程库和本地库所有分支

#### 9. git branch -m

重命名分支    -M  强制

### git 标签

#### git tag 标签名 [commit id]

添加标签  可以对指定的  id   打标签

### git tag

查看所有标签



### git远程仓库

#### 1. git remote add [名字][远程库地址]

    添加一个远程库
    eg: git remote add origin git@github.com:uioqvGitHub/testgithub.git
#### 2. git remote
    查看关联的远程库
    eg: git remote
    查看远程库地址  git@git.....
    git remote -v
#### 3. git push [远程库名][分支名]
    将master分支的修改，更新到指定远程库
    eg: git push origin master
#### 4. git clone [远程库地址]
    将远程库克隆到本地
    eg: git git@github.com:uioqvGitHub/testgithub.git

#### 5. git fetch/pull [远程库] \[分支]  https://blog.csdn.net/qq_36113598/article/details/78906882 https://www.jianshu.com/p/d265f7763a3a

```
执行 git fetch/pull [远程库]会将所有分支最新版本拉取到本地版本库/工作区
执行 git fetch/pull [远程库] [分支名]  拉取远程库指定分支
```

> fetch  会创建FETCH_HEAD指针
>
> pull  相当于  git fetch [远程库] + git merge FETCH_HEAD

fetch pull的区别

#### ![img](/版本控制工具/assets/git.jpg)







