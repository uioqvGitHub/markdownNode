[TOC]



cp ls 基础命令 Note 2018-03-19T21.53.20
========================

## cp
### 参数
#### -a 或 --archive
效果等同于指定"-apR"参数

####  --backup[=CONTROL]
若目标文件已存在则备份目标文件加上~后缀，可以指定备份的模式
CONTROL模式：

模式|简写|功能
--- | --- | ---
none | off | 从不备份
simple| never’ | 	简单备份, '目标文件~'  可以用-S指定后缀
existing| nil | 如果存在编号备份，则编号备份，否则简单备份
numbered | t |	编号备份，'目标文件.~1~'

#### -b
若目标文件已存在则备份目标文件加上~后缀
#### -S 或 --suffix
cp -S 后缀  源文件  目标文件;
若目标文件已存在则备份目标文件加上后缀

#### -f 或 --force
强行复制文件或者目录不论文件或目录是否已经存在
#### -i 或 --interactive
覆盖文件前先询问用户
#### -n 或 --no-clobber
目标文件存在不覆盖（覆盖-i选项）

#### -l 或 --link
对源文件建立硬连接，而非复制文件
#### -s 或 --symbolic-link
对源文件建立符号链接， 而非复制文件，与ln不同只有能链接到原文件才会创建

#### -r  -R 或 --recursive
递归处理， 将指定目录下的文件与子目录一并处理。
#### --parents
复制文件，保留完整文件名称，即带目录结构复制。
#### --remove-destination
复制文件，先将目标文件删除后再复制	


#### -p 或  --preserve
保留源文件目录的属性，包括所有者、所属组、权限与时间
赞同于 --preserve=mode,ownership,timestamps
#### --preserve
保留指定的属性
默认：  mode,ownership,timestamps

属性 | 描述
--- | ---
mode | 权限
timestamps | 时间戳
ownership | 拥有者与组
links | 链接属性
context | 安全上下文 
xattr | 文件的扩展属性
all | 所有

#### --no-preserve
不保留指定属性


#### -u 或 --update
只会在源文件的修改时间(Modification Time)较目的文件更新时，或是名称相互对应的目的文件并不存在，才复制文件
#### -v 或 --verbose 
显示执行过程

#### -H
在使用递归删除时，符号链接复制为符号链接
#### -L 或 --dereference
复制链接的文件，而不是链接本身
#### -P 或 --no-dereference
复制符号链接，而不是链接的文件

#### --attributes-only		
仅复制文件属性，不复制文件数据
#### --copy-contents
复制特殊文件的内容
#### -d
相当于-P加上--preserve=links参数
#### --strip-trailing-slashes
删除参数中所有源文件/目录末端的斜杠
#### -t 或 --target-directory
指定目标文件夹， 只能是目录
#### -T, --no-target-directory
目标是普通文件
####  -u, --update
只有当源文件比目标文件新时才覆盖
#### -v, --verbose
显示进度
#### -x 或 --one-file-system
复制时，目标文件系统必需与源文件系统一致，否则不复制

## ls

### 参数
#### -a 或 --all
显示所有条目包括 . 与 ..

#### -A 或 --almost-all

与-a相比,不包括 . 与 ..
#### --author
显示作者, 使用 -l参数时
#### --block-size
指定ls -l 显示文件大小的单位 eg: --block-size=M
#### -B 或 --ignore-backups
不显示以~结尾的文件，即备份文件
#### -c
显示文件的ctime，创建时间
#### -C
按列显示， 与-l 相反
#### --color

为输出着色

值 | 描述
--- | ---
‘always’, ‘yes’, ‘force’ | 总是
‘never’, ‘no’, ‘none’ | 从不
‘auto’, ‘tty’, ‘if-tty’ | 自动

#### -s 或 --size

打印文件的大小

#### -d 或 --directory

显示目录本身而不是其包含的文件

#### --time

字段 | 含义 
--- | ---
atime/access/use | 访问时间
ctime/status | 更改文件信息时间

#### --time-style

值 | 描述
--- | --
full-iso | 完全的iso时
long-iso | 长iso时间包含 年-月-日 时:分
iso | iso 时间 包含 月-日 时:分
locale | 本地语言设置的时间格式
+FORMATE | FORMATE 是一个格式化字符串 %Y年 %m月 %d日 %H时 %M分 %S秒


#### -r 或 --reverse

以反序方式显示列表

#### -R 或 --recursive

列出子目录内容 ，   递归显示目录内容

#### --sort -U -S -t -v -X

以指定字段排序刘国庆

| 字段       | 描述     | 对应相当作用参数 |
| ---------- | -------- | ---------------- |
| none       | 无       | -U               |
| size       | 大小     | -S               |
| time       | 修改时间 | -t               |
| version    | 版本     | -v               |
| exten-sion | 扩展名   | -X               |



#### -f

不排序，启用-aCU，禁用-ls --color	
####  -F, --classify
根据条目的种类添加指示符    (*/=>@|) 

指示符| 描述
--- | ---
/ | 目录
@ | 软链接
*|。。。
=|...
\||...

#### --file-type

 和 -F 差不多不会加*

#### --full-time

与-l --time-style=full-iso相同，当前系统显示 

	年-月-日 时:分:秒.000000000 +0800

#### -g 

与 -l 差不多，但是不显示拥有者

#### -G 或 --no-group

在 -l 选项的 ls 命令中不显示组名称

#### -o

与 -G  相同，  但是不使用 -l 参数也为一个长列表

#### -h 或 --human-readable

在 -l 选项或 -s 选项的 ls 命令中 显示方便读的文件大小

#### --si

与 -h 差不多， 但是是**1000b** 为**1k**

#### -H 或 --dereference-command-line

指定文件时，或为符号连接， 则显示真实文件的属性

#### --dereference-command-line-symlink-to-dir

与 -H 类似但指定的是引用目录的软链接

####--hide

指定不显示的文件， 会被 -a 或 -A 参数覆盖

#### -i 或 --inode

打印每个文件的索引号

#### -l 

使用长列表显示

#### -L 或 --dereference

当显示软链接时， 显示源文件的信息而不是链接本身

#### -m

用逗号分隔列表项

#### -n 或  --numeric-uid-gid

与 -l 类似， 但是列表显示的是  **用户id** 与 **组id** 

#### -p 或 --indicator-style=slash

目录结尾加 /

#### -q 或 --quote-name

条目的名字用双引号 括上


