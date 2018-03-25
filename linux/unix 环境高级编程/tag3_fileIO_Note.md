---
typora-root-url: ..
---

[TOC]

第三章文件IO Note 2018-03-19T12.07.50
========================

### 文件描述符
1.  文件描述符是一个非负整数，当打开一个现有文件或创建一个新文件时，内核向进程返回一个文件描述符
2.  文件描述符变化范围是
    0~OPEN_MAX-1
2.  按照惯例，shell把
    1. STDIN_FILENO 
    文件描述符0与进程标准输入关联
    2. STDOUT_FILENO
    文件描述符1与进程标准输出关联
    3. STDERR_FILENO
    文件描述符2与进程标准错误关联

### open和openat 返回文件描述符

    #include <fcntl.h>
    int open(const char* path, int oflag, ... /*mode_t mode*/)
    int openat(int fd, const char* path, int oflag, ... /*mode_t mode*/)
1.  path是绝对路径，这种情况fd将被忽略，open相当于openat
2.  path是相对路径，fd指出了相对路径名在文件系统中的开始地址
3.  path是相对路径，fd具有特殊值AT_FDCWD，这种情况路径名在当前工作目录获取，open与openat类似

成功返回文件描述符，若出错，返回-1

oflag | 描述
--- | ---
O_RDONLY |   只读打开
O_WRONLY  |  只写打开
O_RDWR     | 读写打开
O_EXEC    |  只执行打开
O_SEARCH  |  只搜索打开（应用于目录）
O_APPEND  |  追加到文件尾端
O_CLOEXEC |
O_CREAT   |  若文件不存在则创建，指定此参数时需要指定mode参数
O_DIRECTORY | 若path引用的不是目录则出错
O_EXCL    |  如果同时指定了O_CREAT而文件已存在则出错，用此可以测试一个文件是否存在，如果不存在则创建此文件，这使测试和创建成为一个原子操作
O_NOCTTY  |  如果path引用的是终端设备，则不将该设备分配作为此进程的控制终端
O_NOFOLLOW | 如果path引用的是一个符号链接，则出错
O_NONBLOCK | 如果path引用的是一个FIFO、一个块特殊文件或一个字符特殊文件，则此选项为文件的本次打开操作和后续I/O操作设置非阻塞方式
O_SYNC    |  使每次write等待物理I/O操作完成
O_TRUNC   |  如果文件存在且以只写或读写成功打开，则将其长度截断为0
O_TTY_INIT | 如果打开一个还未打开的终端设备，设置非标准termios参数值
O_DSYNC |
O_RSYNC |

### creat 指定mode创建文件

    #include <fcntl.h>
    int creat(const char* path, mode_t mode);
成功返回0出错返回-1
等效于

    open(path, O_WRONLY | O_CREAT | O_TRUNC, mode);
### close 关闭一个文件描述符
    #include <unistd.h>
    int close(int fd);
关闭一个已打开文件
### lseek 设置文件偏移量

    #include <unistd.h>
    off_t lseek(int fd, off_t offset, int whence);
成功返回新文件偏移量，出错返回-1

1.  若whence是SEEK_SET，将偏移量设置为距文件开始处offset个字节
2.  若whence是SEEK_CUR，则将文件偏移量设置为其当前值加上offset
3.  若whence是SEEK_END，则将文件偏移量设置为文件长度加上offset

确定打开文件的当前偏移量

    off_t   currpos;
    currpos = lseek(fd, 0, SEEK_CUR);

>这种方法也可以来确定所涉及的文件是否可以设置偏移量，如果文件描述符指向的是一个管道、FIFO或网络套接字，则lseek返回-1，并将errno设置为ESPIPE

### read 从文件描述符代表的文件中读取

    #include <unistd.h>
    ssize_t read(int fd, void* buf, size_t nbytes);
返回读到时字节数，若已到文件末尾返回0，出错返回-1

### write 向文件描述符代表的文件中写入

    #include <unistd.h>
    ssize_t write(int fd, const void* buf, size_t nbytes);
成功返回已写的字节数，失败返回-1

### pread 原子方式从指定文件偏移内容读

    #include <unistd.h>
    ssize_t pread(int fd, void* buf, size_t nbytes, off_t offset);
相当于lseek后read的原子操作

### pwrite 原子方式向指定文件偏移内容写

    #include <unistd.h>
    ssize_t pwrite(int fd, void* buf, size_t nbytes, off_t offset);

### dup和dup2

    #include <unistd.h>
    int dup(int fd);
    int dup2(int fd, int fd2);

若成功，返回新的文件描述符，若出错返回-1

> 对于dup返回的文件描述符一定是当前可以文件描述符的最小值。
>
> > 1. 调用 `dup(fd);`
> >
> >    等效于 `fcntl(fd, F_DUPFD, 0);`
> >
> > 2. 调用 `dub2(fd, fd2);`
> >
> >    等效于 `close(fd2); ` `fcntl(fd, F_DUPFD, fd2);`
> >
> > > 第二种情况dup2不完全赞同于close + fcntl:
> > >
> > > 1. dup2是一个原子操作
> > > 2. dup2和fcntl有一些不同的errno

### sync、 fsync 与 fdatasync

​	当我们向文件写入数据时，内核通常先将数据复制到缓冲区，然后排入队列，晚些时候再写入磁盘，这种方式被称为延迟写（dalayed write)

​	为了保证磁盘上实际文件系统与缓冲区中内容的一致性， unix系统提供了 sync、 fsync 和 fdatasync 三个函数。

```c
#include<unistd.h>
int fsync(int fd);
int fdatasync(int fd);
/*若成功，返回0 若出错，返回-1*/
void sync(void);
```

- sync只是将所有修改的块缓冲区排入写队列，然后返回，它并不等待实际写磁盘操作结束。

  > 通常， update 系统守护进程周期性调用（一般每隔30秒）sync函数。

- fsync 函数只对由文件描述符fd指定的一个文件起作用， 并且等待实际写磁盘操作结束才返回。

- fdatasync 函数类似于 fsync， 但它只影响文件的数据部分， fsync还会更新文件属性。

  > FreeBSD8.0不支持fdatasync


### fcntl

​	fcntl函数可以改变已经打开文件的属性。

```c
#include<fcntl.h>
int fcntl(int fd, int cmd, ... /* int arg */);
/*若成功， 则依赖于cmd 若出错，返回-1*/
```

功能：

| cmd                        | 功能                    |
| -------------------------- | ----------------------- |
| F_DUPFD 或 F_DUPFD_CLOEXEC | 复制一个已有的描述符    |
| F_GETFD 或 F_SETFD         | 获取/设置文件描述符标志 |
| F_GETFL 或 F_SETFL         | 获取/设置文件状态标志   |
| F_GETOWN 或 F_SETOWN       | 获取/设置异步I/O所有权  |
| F_GETLK 或 F_SETLK 或 F_SETLKW |获取/设置记录锁|

### ioctl

​	ioctl 函数是I/O操作的杂物箱

```c
#include <unistd.h> /* System V */
#include <sys/ioctl.h> /* BSD and Linux */
int ioctl(int fd, int request, ...);
/* 若成功， 返回其他值 若出错， 返回-1 */
```

​	在此原型中， 我们表示的只是 ioctl 函数本身所要求的头文件。 通常还要求另外设备专用头文件。 例如， 除 POSIX.1 所说明的基本操作之外， 终端 I/O 的 ioctl 命令都需要头文件 <termios.h>。

​	![](/../.images/temp.jpg)