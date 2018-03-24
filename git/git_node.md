---
typora-root-url: ../
---

[TOC]



git基本操作 Note 2018-03-19T15.13.25
========================



### git基本概念

​				

![](/.images/1766480014.jpeg)


#### 1. 工作区（Working Directory）
    就是电脑中通过git init初始化的目录
#### 2. 版本库（Repository）
    工作区有一个隐藏目录.git，这个不算工作区，而是Git的版本库
#### 3. 分支
    每次提交，Git都把它们串成一条时间线，这条时间线就是一个分支。
    HEAD严格来说不是指向提交，而是指向master，master才是指向提交的，
    所以，HEAD指向的就是当前分支。
一开始的时候，master分支是一条线，Git用master指向最新的提交，再用HEAD指向master，就能确定当前分支，以及当前分支的提交点：



![0](/.images/1957911147.png)



每次提交，master分支都会向前移动一步，这样，随着你不断提交，master分支的线也越来越长。

当我们创建新的分支，例如dev时，Git新建了一个指针叫dev，指向master相同的提交，再把HEAD指向dev，就表示当前分支在dev上：

![0 (1)](/.images/1501471077.png)



从现在开始，对工作区的修改和提交就是针对dev分支了，比如新提交一次后，dev指针往前移动一步，而master指针不变：

![0 (2)](/.images/1611475412.png)



假如我们在dev上的工作完成了，就可以把dev合并到master上:

![0 (3)](/.images/1750277683.png)




合并完分支后，甚至可以删除dev分支。删除dev分支就是把dev指针给删掉，删掉后，我们就剩下了一条master分支：



![0 (4)](/.images/1685740803.png)




准备新的feature1分支，在readme.txt中加入abcd.
切换到master分支，在readme.txt中加入abdd.

![0 (5)](/.images/682962317.png)





这时合并feature1分支时，会产生冲突。
readme.txt内容会显示冲突

    <<<<<<< HEAD
    abdd.
    =======
    abcd.
    >>>>>>> feature1<br>
将冲突修改后<br>

![image](https://cdn.liaoxuefeng.com/cdn/files/attachments/00138490913052149c4b2cd9702422aa387ac024943921b000/0)

<br>
用git log --graph可以看到

    *   fff8f43a2d0788a95e01151f1b2fa81694e46738 marge after
    |\  
    | * 9c0bf016f1f5b3ae912d46467c3faee2e030ee93 fea1 commit
    * | 0f309e2702eed9f6e0f1d12c83ebfe270b8d9b7b master commit1
    |/  
    * e6d95562decc7d8ab9826b0eabc8758b1a9a245f dev2 commit

---
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
---
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
#### 4. git merge [分支名]
    合并分支，合并指定分支到当前分支
    eg: git merge dev
#### 5. git branch -d [分支名]
    删除分支
    eg: git branch -d dev
#### 6. git branch
    查看分支，显示所有的分支与当前的分支
    eg: git branch

---
### git远程仓库
#### 1. git remote add [名字][远程库地址]

    添加一个远程库
    eg: git remote add origin git@github.com:uioqvGitHub/testgithub.git
#### 2. git remote
    查看关联的远程库
    eg: git remote
#### 3. git push [远程库名][分支名]
    将master分支的修改，更新到指定远程库
    eg: git push origin master
#### 4. git clone [远程库地址]
    将远程库克隆到本地
    eg: git git@github.com:uioqvGitHub/testgithub.git