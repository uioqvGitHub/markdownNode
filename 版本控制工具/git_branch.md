[TOC]

### 1. gitflow

![img](20151230174636677)

1. remote有两个主分支  master, develop   有不同类型的 hotfixes, release, feature 支持型分支。
2. 每当 `develop` 分支到达一个稳定的阶段，可以对外发布时，所有的改变都会被合并到`master` 分支，并打一个发布版本的 tag
3. 线上的bug需要紧急修复，从master检出一条hotfixes分支，修复bug，将分支合并到master与develop
4. 开发新功能，从develop检出一条feature分支，开发功能模块，将分支合并到develop
   1. develop分支已经比较稳定,检出一条release分支，准备发布新版本修修bug等，合并到master与develop分支![img](20151229145702928-1529398970462)

#### feature分支

​	有时也被称作 topic 分支，用来开发包括即将发布或远期发布的新的特性。当我们开始开发一个特性的时候，发布合并的目标可能还不太确定。Feature 分支的生命周期会和新特性的开发周期保持同步，但是最终会合并回 `develop` 

> Feature 分支通常仅存在于开发者的代码库中，并不出现在 `origin` 里。

- 可能派发自：`develop`
- 必须合并回：`develop`
- 分支命名规范：除了 `master`、`develop`、`release-*` 或 `hotfix-*` 的任何名字

#### release分支

​	预发布的分支，对develop进行微调，允许小幅度的问题修复以及准备发布时的meta数据(比如版本号、发布日期等)。会增加一个大版本，如1.1-1.2

- 可能派发自：`develop`
- 必须合并回：`develop` 和 `master`
- 分支命名规范：`release-*`

####hotfix分支

​	Hotfix 分支和 release 分支非常类似，因为他们都意味着会产生一个新的生产环境的发布，会增加一个小版本，如1.2-1.21

​	如果此时已经存在了一个 release 分支，那么 hotfix 的改变需要合并到这个 release 分支，而不是 develop 分支。因为把对 bug 的修复合并回 release 分支之后，release 分支最终还是会合并回 `develop` 分支的。(如果在 `develop` 分支中立刻需要对这个 bug 的修复，且等不及 release 分支合并回来，则你还是可以直接合并回 `develop` 分支的)

- 可能派发自：`master`
- 必须合并回：`develop` 和 `master`
- 分支命名规范：`hotfix-*`

### 2. -no-ff标记

​	使用-no-ff标记使得合并操作总是产生一次新的提交，哪怕合并操作可以快速完成。这个标记避免将 feature 分支和团队协作的所有提交的历史信息混在主分支的其它提交之后。比较一下：

![img](20151229150332334)





