linux 基础学习 Note 2018-03-23T17.16.11
========================

MSDOS(MBR)分区
    开机管理程序记录区与分区表放在磁盘的第一个磁区，通常是５２５bytes大小
        开机管理程序记录区４４６bytes
        分区表:记录磁盘的分割状态，有６４bytes;
GUID(GPT)分区

文件系统与目录树的关系(mount挂载)


／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／
NAT(達成IP分享器的功能)：　　－－－－－＞　　NAT（Network Address Translation，网络地址转换）
通常小型企業或者是學校單位大多僅會有一條對外的連線，然後全公司/學校內的電腦全部透過這條連線連到網際網路上。
此時我們就得要使用IP分享器來讓這一條對外連線分享給所有的公司內部員工使用。 那麼Linux能不能達到此一IP分享的功能呢？
當然可以，就是透過NAT服務即可達成這項任務了！

在這種環境中，由於Linux作為一個內/外分離的實體，因此網路流量會比較大一點。 此時Linux主機的網路卡就需要比較好些的配備。
其他的CPU、RAM、硬碟等等的影響就小很多。 事實上，單利用Linux作為NAT主機來分享IP是很不智的～因為PC的耗電能力比IP分享器要大的多～

那麼為什麼你還要使用Linux作為NAT呢？
因為Linux NAT還可以額外的加裝很多分析軟體， 可以用來分析用戶端的連線，或者是用來控制頻寬與流量，達到更公平的頻寬使用呢！
更多的功能則有待後續更多的學習囉！你也可以參考我們在伺服器架設篇當中的資料囉！

／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／
SAMBA(加入Windows網路上的芳鄰)：　－－－＞　SMB（Server Messages Block，信息服务块）是一种在局域网上共享文件和打印机的一种通信协议，
在你的Windows系統之間如何傳輸資料呢？當然就是透過網路上的芳鄰來傳輸啦！那還用問。
這也是學校老師在上課過程中要分享資料給同學常用的機制了。
問題是，Windows 7 的網芳一般只能同時分享十部用戶端連線，超過的話就得要等待了～真不人性化。

我們可以使用Linux上面的SAMBA這個軟體來達成加入Windows網芳的功能喔！
SAMBA的效能不錯， 也沒有用戶端連線數的限制，相當適合於一般學校環境的檔案伺服器(file server)的角色呢！

這種伺服器由於分享的資料量較大，對於系統的網路卡與硬碟的大小及速度就比較重要，
如果你還針對不同的使用者提供檔案伺服器功能，那麼/home這個目錄可以考慮獨立出來，並且加大容量。

／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／
Mail(郵件伺服器)：
郵件伺服器是非常重要的，尤其對於現代人來說，電子郵件幾乎已經取代了傳統的人工郵件遞送了。
 拜硬碟價格大跌及Google/Yahoo/MicroSoft公平競爭之賜，一般免費的email信箱幾乎都提供了很不錯的郵件服務，
 包過Web介面的傳輸、大於2GB以上的容量空間及全年無休的服務等等。例如非常多人使用的gmail就是一例： http://gmail.com。

雖然免費的信箱已經非常夠用了，老實說，鳥哥也不建議您架設mail server了。
問題是， 如果你是一間私人單位的公司，你的公司內傳送的email是具有商業機密或隱私性的，那你還想要交給免費信箱去管理嗎？
此時才有需要架設mail server囉。在mail server上面，重要的也是硬碟容量與網路卡速度，
在此情境中，也可以將/var目錄獨立出來，並加大容量。
／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／
Web(WWW伺服器)：
WWW伺服器幾乎是所有的網路主機都會安裝的一個功能，因為他除了可以提供Internet的WWW連線之外，
 很多在網路主機上面的軟體功能(例如某些分析軟體所提供的最終分析結果的畫面)也都使用WWW作為顯示的介面，
 所以這傢伙真是重要到不行的。

CentOS使用的是Apache這套軟體來達成WWW網站的功能，在WWW伺服器上面，如果你還有提供資料庫系統的話，
 那麼CPU的等級就不能太低，而最重要的則是RAM了！要增加WWW伺服器的效能，通常提升RAM是一個不錯的考量。

／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／
 DHCP(提供用戶端自動取得IP的功能)：
 如果你是個區域網路管理員，你的區網內共有20部以上的電腦給一般員工使用，這些員工假設並沒有電腦網路的維護技能。
  那你想要讓這些電腦在連上Internet時需要手動去設定IP還是他可以自動的取得IP呢？當然是自動取得比較方便啦！
  這就是DHCP服務的功能了！用戶端電腦只要選擇『自動取得IP』，其他的，就是你系統管理員在DHCP伺服器上面設定一下即可。
   這個咚咚的硬體要求可以不必很高囉。

／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／
FTP：
常常看到很多朋友喜歡架設FTP去進行網路資料的傳輸，甚至很多人會架設地下FTP網站去傳輸些違法的資料。
老實說，『FTP傳輸再怎麼地下化也是很容易被捉到的』啦！
所以，鳥哥相當不建議您架設FTP的喔！ 不過，對於大專院校來說，因為常常需要分享給全校師生一些免費的資源，
此時匿名使用者的FTP軟體功能就很需要存在了。對於FTP的硬體需求來說，硬碟容量與網路卡好壞相關性較高。
大致上我們會安裝的伺服器軟體就是這一些囉！
當然啦，還是那句老話，在目前你剛接觸Linux的這個階段中，還是以Linux基礎為主，
鳥哥也希望你先瞭解Linux的相關主機操作技巧，其他的架站，未來再談吧！ 而上面列出的各項服務，
僅是提供給你，如果想要架設某種網路服務的主機時，你應該如何規劃主機比較好！
／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／


笔记本安装时
／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／
安装笔记本电脑或其他类PC计算机的参数
由於笔记本电脑加入了非常多的省电机制或者是其他硬件的管理机制，包括显示卡常常是整合型的，
因此在笔记本电脑上面的硬件常常与一般桌上型计算机不怎么相同。
所以当你使用适合於一般桌上型计算机的DVD来安装Linux时，
 可能常常会出现一些问题，导致无法顺利的安装Linux到你的笔记本电脑中啊！那怎办？

其实很简单，只要在安装的时候，告诉安装程序的linux核心不要载入一些特殊功能即可。
 最常使用的方法就是，在使用DVD开机时，加入底下这些选项：

boot: linux nofb apm=off acpi=off pci=noacpi
apm(Advanced Power Management)是早期的电源管理模组，
acpi(Advanced Configuration and Power Interface)则是近期的电源管理模组。
这两者都是硬件本身就有支援的，但是笔记本电脑可能不是使用这些机制， 因此，当安装时启动这些机制将会造成一些错误，导致无法顺利安装。

nofb则是取消显示卡上面的缓冲内存侦测。因为笔记本电脑的显示卡常常是整合型的，
 Linux安装程序本身可能就不是很能够侦测到该显示卡模组。此时加入nofb将可能使得你的安装过程顺利一些。


重启x window
／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／
重新启动X Window的快速按钮
一般来说，我们是可以手动来直接修改X Window的配置文件的，不过，修改完成之后的配置项目并不会立刻被加载，
 必须要重新启动X才行(特别注意，不是重新启动，而是重新启动X！) 。那么如何重新启动X呢？ 最简单的方法就是：

直接注销，然后再重新登陆即可；
在X的画面中直接按下[Alt] + [Ctrl] + [Backspace]
第二个方法比较有趣，[backspace]是退格键，你按下三个按钮后X Window立刻会被重新启动。
如果你的X Window因为不明原因导致有点问题时，也可以利用这个方法来重新启动X喔！^_^

关机
／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／
观察系统的使用状态：
如果要看目前有谁在在线，可以下达『who』这个命令，而如果要看网络的联机状态，
可以下达 『 netstat -a 』这个命令，而要看背景运行的程序可以运行『 ps -aux 』这个命令。
使用这些命令可以让你稍微了解主机目前的使用状态！当然啰，就可以让你判断是否可以关机了（这些命令在后面Linux常用命令中会提及喔！）
通知在线使用者关机的时刻：
要关机前总得给在线的使用者一些时间来结束他们的工作，所以，这个时候你可以使用 shutdown 的特别命令来达到此一功能。

正确的关机命令使用：
例如 shutdown 与 reboot 两个命令！
所以底下我们就来谈一谈几个与关机/重新启动相关的命令啰！

将数据同步写入硬盘中的命令： sync
惯用的关机命令： shutdown
重新启动，关机： reboot, halt, poweroff


运行等级
／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／
切换运行等级： init
本章上头有谈到过关于run level的问题。
之前谈到的是系统运行的模式，分为纯文本(run level 3)及图形接口模式(run level 5)。
除了这两种模式外，有没有其他模式呢？其实Linux共有七种运行等级， 七种等级的意义我们在后面会再谈到。
本章你只要知道底下四种运行等级就好了：

run level 0：关机
run level 3：纯文本模式
run level 5：含有图形接口模式
run level 6：重新启动
那如何切换各模式呢？可以使用init这个命令来处理喔！
也就是说，如果你想要关机的话， 除了上述的shutdown -h now以及poweroff之外，你也可以使用如下的命令来关机：
[root@www ~]# init 0


不正当操作文件系统损坏
／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／
文件系统错误的问题
在启动的过程中最容易遇到的问题就是硬盘可能有坏轨或文件系统发生错误(数据损毁)的情况， 这种情况虽然不容易发生在稳定的Linux系统下，不过由于不当的开关机行为， 还是可能会造成的，常见的发生原因可能有：

最可能发生的原因是因为断电或不正常关机所导致的文件系统发生错误， 鸟哥的主机就曾经发生过多次因为跳电，家里的主机又没有安装不断电系统， 结果就导致硬盘内的文件系统错误！文件系统错误并非硬件错误，而是软件数据的问题喔！

硬盘使用率过高或主机所在环境不良也是一个可能的原因， 例如你开放了一个FTP服务，里面有些数据很有用， 所以一堆人抢着下载，如果你又不是使用较稳定的SCSI接口硬盘，仅使用一般PC使用的硬盘， 虽然机率真的不高，但还是有可能造成硬盘坏轨的。此外，如果主机所在环境没有散热的设备， 或者是相对湿度比较高的环境，也很容易造成硬盘的损坏喔！
解决的方法其实很简单，不过因为出错扇区所挂载的目录不同，处理的流程困难度就有差异了。 举例来说，如果你的根目录『/』并没有损毁，那就很容易解决，如果根目录已经损毁了，那就比较麻烦！


如果根目录没有损毁：
假设你发生错误的partition是在/dev/sda7这一块，那么在启动的时候，
屏幕应该会告诉你：press root password or ctrl+D : 这时候请输入root的密码登陆系统，然后进行如下动作：

在光标处输入root密码登陆系统，进行单人单机的维护工作；
输入『 fsck /dev/sda7 』(fsck 为文件系统检查的命令，
/dev/sda7为错误的partition，请依你的情况下达参数)，
这时屏幕会显示开始修理硬盘的信息，如果有发现任何的错误时，
屏幕会显示： clear [Y/N]？ 的询问信息，就直接输入 Y 吧！
修理完成之后，以 reboot 重新启动啰！

如果根目录损毁了
一般初学者喜欢将自己的硬盘只划分为一个大partition，亦即只有根目录，
 那文件系统错误一定是根目录的问题啰！这时你可以将硬盘拔掉，接到另一台Linux系统的计算机上，
 并且不要挂载(mount)该硬盘，然后以root的身份运行『 fsck /dev/sdb1 』(/dev/sdb1 指的是你的硬盘装置文件名，
 你要依你的实际状况来配置)，这样就 OK 啰！

另外，也可以使用近年来很热门的Live CD，也就是利用光盘启动就能够进入Linux操作系统的特性，
你可以前往：『http://knoppix.tnc.edu.tw/』 这个网站来下载，
并且刻录成为CD，这个时候先用Live CD光盘启动，然后使用fsck去修复原本的根目录，
例如： fsck /dev/sda1 ，就能够救回来了！


修改文件时间或建置新档： touch
／／／／／／／／／／／／／／／／／／／／／／／／／／
我们在 ls 这个命令的介绍时，有稍微提到每个文件在linux底下都会记录许多的时间参数，
 其实是有三个主要的变动时间，那么三个时间的意义是什么呢？

modification time (mtime)：
当该文件的『内容数据』变更时，就会升级这个时间！内容数据指的是文件的内容，而不是文件的属性或权限喔！

status time (ctime)：
当该文件的『状态 (status)』改变时，就会升级这个时间，举例来说，像是权限与属性被更改了，都会升级这个时间啊。

access time (atime)：
当『该文件的内容被取用』时，就会升级这个读取时间 (access)。


默认权限：umask
／／／／／／／／／／／／／／／／／／／／

文件隐藏属性：
chattr (配置文件隐藏属性)
[root@www ~]# chattr [+-=][ASacdistu] 文件或目录名称
选项与参数：
+   ：添加某一个特殊参数，其他原本存在参数则不动。
-   ：移除某一个特殊参数，其他原本存在参数则不动。
=   ：配置一定，且仅有后面接的参数

A  ：当配置了 A 这个属性时，若你有存取此文件(或目录)时，他的存取时间 atime
     将不会被修改，可避免I/O较慢的机器过度的存取磁碟。这对速度较慢的计算机有帮助
S  ：一般文件是非同步写入磁碟的(原理请参考第五章sync的说明)，如果加上 S 这个
     属性时，当你进行任何文件的修改，该更动会『同步』写入磁碟中。
a  ：当配置 a 之后，这个文件将只能添加数据，而不能删除也不能修改数据，只有root
     才能配置这个属性。
c  ：这个属性配置之后，将会自动的将此文件『压缩』，在读取的时候将会自动解压缩，
     但是在储存的时候，将会先进行压缩后再储存(看来对於大文件似乎蛮有用的！)
d  ：当 dump 程序被运行的时候，配置 d 属性将可使该文件(或目录)不会被 dump 备份
i  ：这个 i 可就很厉害了！他可以让一个文件『不能被删除、改名、配置连结也无法
     写入或新增数据！』对於系统安全性有相当大的助益！只有 root 能配置此属性
s  ：当文件配置了 s 属性时，如果这个文件被删除，他将会被完全的移除出这个硬盘
     空间，所以如果误删了，完全无法救回来了喔！
u  ：与 s 相反的，当使用 u 来配置文件时，如果该文件被删除了，则数据内容其实还
     存在磁碟中，可以使用来救援该文件喔！
注意：属性配置常见的是 a 与 i 的配置值，而且很多配置值必须要身为 root 才能配置

范例：请尝试到/tmp底下创建文件，并加入 i 的参数，尝试删除看看。
[root@www ~]# cd /tmp
[root@www tmp]# touch attrtest     <==创建一个空文件
[root@www tmp]# chattr +i attrtest <==给予 i 的属性
[root@www tmp]# rm attrtest        <==尝试删除看看
rm: remove write-protected regular empty file `attrtest'? y
rm: cannot remove `attrtest': Operation not permitted  <==操作不许可
# 看到了吗？呼呼！连 root 也没有办法将这个文件删除呢！赶紧解除配置！

范例：请将该文件的 i 属性取消！
[root@www tmp]# chattr -i attrtest
这个命令是很重要的，尤其是在系统的数据安全上面！
由於这些属性是隐藏的性质，所以需要以 lsattr 才能看到该属性呦！
其中，个人认为最重要的当属 +i 与 +a 这个属性了。
+i 可以让一个文件无法被更动，对於需要强烈的系统安全的人来说，
真是相当的重要的！里头还有相当多的属性是需要 root 才能配置的呢！
此外，如果是 log file 这种的登录档，就更需要 +a 这个可以添加，
但是不能修改旧有的数据与删除的参数了！怎样？很棒吧！
 未来提到登录档 (十九章) 的认知时，我们再来聊一聊如何配置他吧！


lsattr (显示文件隐藏属性)
[root@www ~]# lsattr [-adR] 文件或目录
选项与参数：
-a ：将隐藏档的属性也秀出来；
-d ：如果接的是目录，仅列出目录本身的属性而非目录内的档名；
-R ：连同子目录的数据也一并列出来！

[root@www tmp]# chattr +aij attrtest
[root@www tmp]# lsattr attrtest
----ia---j--- attrtest
使用 chattr 配置后，可以利用 lsattr 来查阅隐藏的属性。
不过， 这两个命令在使用上必须要特别小心，否则会造成很大的困扰。
例如：某天你心情好，突然将 /etc/shadow 这个重要的密码记录文件给他配置成为具有 i 的属性，
那么过了若干天之后， 你突然要新增使用者，却一直无法新增！别怀疑，赶快去将 i 的属性拿掉吧！



文件特殊权限： SUID, SGID, SBIT
／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／
我们前面一直提到关於文件的重要权限，那就是 rwx 这三个读、写、运行的权限。
 但是，眼尖的朋友们在第六章的目录树章节中， 一定注意到了一件事，那就是，怎么我们的 /tmp 权限怪怪的？
  还有，那个 /usr/bin/passwd 也怪怪的？怎么回事啊？看看先：

[root@www ~]# ls -ld /tmp ; ls -l /usr/bin/passwd
drwxrwxrwt 7 root root 4096 Sep 27 18:23 /tmp
-rwsr-xr-x 1 root root 22984 Jan  7  2007 /usr/bin/passwd
不是应该只有 rwx 吗？还有其他的特殊权限( s 跟 t )啊？
啊.....头又开始昏了～ @_@ 因为 s 与 t 这两个权限的意义与系统的帐号
 (第十四章)及系统的程序(process, 第十七章)较为相关， 所以等到后面的章节谈完后你才会比较有概念！
 底下的说明先看看就好，如果看不懂也没有关系， 先知道s放在哪里称为SUID/SGID以及如何配置即可，等系统程序章节读完后，再回来看看喔！


Set UID
当 s 这个标志出现在文件拥有者的 x 权限上时，例如刚刚提到的 /usr/bin/passwd 这个文件的权限状态：
『-rwsr-xr-x』，此时就被称为 Set UID，简称为 SUID 的特殊权限。 那么SUID的权限对於一个文件的特殊功能是什么呢？
基本上SUID有这样的限制与功能：

SUID 权限仅对二进位程序(binary program)有效；
运行者对於该程序需要具有 x 的可运行权限；
本权限仅在运行该程序的过程中有效 (run-time)；
运行者将具有该程序拥有者 (owner) 的权限。
讲这么硬的东西你可能对於 SUID 还是没有概念，没关系，我们举个例子来说明好了。
我们的 Linux 系统中，所有帐号的密码都记录在 /etc/shadow 这个文件里面，这个文件的权限为：
『-r-------- 1 root root』，意思是这个文件仅有root可读且仅有root可以强制写入而已。
既然这个文件仅有 root 可以修改，那么鸟哥的 vbird 这个一般帐号使用者能否自行修改自己的密码呢？
你可以使用你自己的帐号输入『passwd』这个命令来看看，嘿嘿！一般使用者当然可以修改自己的密码了！

唔！有没有冲突啊！明明 /etc/shadow 就不能让 vbird 这个一般帐户去存取的，为什么 vbird 还能够修改这个文件内的密码呢？
这就是 SUID 的功能啦！藉由上述的功能说明，我们可以知道

vbird 对於 /usr/bin/passwd 这个程序来说是具有 x 权限的，表示 vbird 能运行 passwd；
passwd 的拥有者是 root 这个帐号；
vbird 运行 passwd 的过程中，会『暂时』获得 root 的权限；
/etc/shadow 就可以被 vbird 所运行的 passwd 所修改。
但如果 vbird 使用 cat 去读取 /etc/shadow 时，他能够读取吗？
因为 cat 不具有 SUID 的权限，所以 vbird 运行 『cat /etc/shadow』 时，是不能读取 /etc/shadow 的。
我们用一张示意图来说明如下：

SUID程序运行的过程示意图
图4.4.1、SUID程序运行的过程示意图
另外，SUID 仅可用在binary program 上， 不能够用在 shell script 上面！
这是因为 shell script 只是将很多的 binary 运行档叫进来运行而已！
所以 SUID 的权限部分，还是得要看 shell script 呼叫进来的程序的配置， 而不是 shell script 本身。
当然，SUID 对於目录也是无效的～这点要特别留意。


Set GID
当 s 标志在文件拥有者的 x 项目为 SUID，那 s 在群组的 x 时则称为 Set GID, SGID 罗！是这样没错！^_^。
举例来说，你可以用底下的命令来观察到具有 SGID 权限的文件喔：

[root@www ~]# ls -l /usr/bin/locate
-rwx--s--x 1 root slocate 23856 Mar 15  2007 /usr/bin/locate
与 SUID 不同的是，SGID 可以针对文件或目录来配置！如果是对文件来说， SGID 有如下的功能：

SGID 对二进位程序有用；
程序运行者对於该程序来说，需具备 x 的权限；
运行者在运行的过程中将会获得该程序群组的支持！
举例来说，上面的 /usr/bin/locate 这个程序可以去搜寻 /var/lib/mlocate/mlocate.db
这个文件的内容 (详细说明会在下节讲述)， mlocate.db 的权限如下：

[root@www ~]# ll /usr/bin/locate /var/lib/mlocate/mlocate.db
-rwx--s--x 1 root slocate   23856 Mar 15  2007 /usr/bin/locate
-rw-r----- 1 root slocate 3175776 Sep 28 04:02 /var/lib/mlocate/mlocate.db
与 SUID 非常的类似，若我使用 vbird 这个帐号去运行 locate 时，那 vbird 将会取得 slocate 群组的支持，
 因此就能够去读取 mlocate.db 啦！非常有趣吧！

除了 binary program 之外，事实上 SGID 也能够用在目录上，这也是非常常见的一种用途！
 当一个目录配置了 SGID 的权限后，他将具有如下的功能：

使用者若对於此目录具有 r 与 x 的权限时，该使用者能够进入此目录；
使用者在此目录下的有效群组(effective group)将会变成该目录的群组；
用途：若使用者在此目录下具有 w 的权限(可以新建文件)，则使用者所创建的新文件，该新文件的群组与此目录的群组相同。
SGID 对於专案开发来说是非常重要的！因为这涉及群组权限的问题，您可以参考一下本章后续情境模拟的案例，
应该就能够对於 SGID 有一些了解的！^_^


Sticky Bit
这个 Sticky Bit, SBIT 目前只针对目录有效，对於文件已经没有效果了。 SBIT 对於目录的作用是：

当使用者对於此目录具有 w, x 权限，亦即具有写入的权限时；
当使用者在该目录下创建文件或目录时，仅有自己与 root 才有权力删除该文件
换句话说：当甲这个使用者於 A 目录是具有群组或其他人的身份，并且拥有该目录 w 的权限，
这表示『甲使用者对该目录内任何人创建的目录或文件均可进行 "删除/更名/搬移" 等动作。』
不过，如果将 A 目录加上了 SBIT 的权限项目时， 则甲只能够针对自己创建的文件或目录进行删除/更名/移动等动作，而无法删除他人的文件。

举例来说，我们的 /tmp 本身的权限是『drwxrwxrwt』， 在这样的权限内容下，
任何人都可以在 /tmp 内新增、修改文件，但仅有该文件/目录创建者与 root 能够删除自己的目录或文件。
这个特性也是挺重要的啊！你可以这样做个简单的测试：

以 root 登陆系统，并且进入 /tmp 当中；
touch test，并且更改 test 权限成为 777 ；
以一般使用者登陆，并进入 /tmp；
尝试删除 test 这个文件！
由於 SUID/SGID/SBIT 牵涉到程序的概念，因此再次强调，这部份的数据在您读完第十七章关於程序方面的知识后，要再次的回来瞧瞧喔！
目前，你先有个简单的基础概念就好了！文末的参考数据也建议阅读一番喔！


SUID/SGID/SBIT 权限配置
前面介绍过 SUID 与 SGID 的功能，那么如何配置文件使成为具有 SUID 与 SGID 的权限呢？
 这就需要第六章的数字更改权限的方法了！ 现在你应该已经知道数字型态更改权限的方式为『三个数字』的组合，
  那么如果在这三个数字之前再加上一个数字的话，最前面的那个数字就代表这几个权限了！

4 为 SUID
2 为 SGID
1 为 SBIT
假设要将一个文件权限改为『-rwsr-xr-x』时，由於 s 在使用者权限中，所以是 SUID ，因此，
在原先的 755 之前还要加上 4 ，也就是：『 chmod 4755 filename 』来配置！此外，还有大 S 与大 T 的产生喔！参考底下的范例啦！

Tips:
注意：底下的范例只是练习而已，所以鸟哥使用同一个文件来配置，你必须了解 SUID 不是用在目录上，
而 SBIT 不是用在文件上的喔！	鸟哥的图示
[root@www ~]# cd /tmp
[root@www tmp]# touch test                  <==创建一个测试用空档
[root@www tmp]# chmod 4755 test; ls -l test <==加入具有 SUID 的权限
-rwsr-xr-x 1 root root 0 Sep 29 03:06 test
[root@www tmp]# chmod 6755 test; ls -l test <==加入具有 SUID/SGID 的权限
-rwsr-sr-x 1 root root 0 Sep 29 03:06 test
[root@www tmp]# chmod 1755 test; ls -l test <==加入 SBIT 的功能！
-rwxr-xr-t 1 root root 0 Sep 29 03:06 test
[root@www tmp]# chmod 7666 test; ls -l test <==具有空的 SUID/SGID 权限
-rwSrwSrwT 1 root root 0 Sep 29 03:06 test
最后一个例子就要特别小心啦！怎么会出现大写的 S 与 T 呢？不都是小写的吗？
因为 s 与 t 都是取代 x 这个权限的，但是你有没有发现阿，我们是下达 7666 喔！
也就是说， user, group 以及 others 都没有 x 这个可运行的标志( 因为 666 嘛 )，
所以，这个 S, T 代表的就是『空的』啦！怎么说？ SUID 是表示『该文件在运行的时候，具有文件拥有者的权限』，
但是文件 拥有者都无法运行了，哪里来的权限给其他人使用？当然就是空的啦！ ^_^

而除了数字法之外，你也可以透过符号法来处理喔！其中 SUID 为 u+s ，而 SGID 为 g+s ，SBIT 则是 o+t 罗！来看看如下的范例：

# 配置权限成为 -rws--x--x 的模样：
[root@www tmp]# chmod u=rwxs,go=x test; ls -l test
-rws--x--x 1 root root 0 Aug 18 23:47 test

# 承上，加上 SGID 与 SBIT 在上述的文件权限中！
[root@www tmp]# chmod g+s,o+t test; ls -l test
-rws--s--t 1 root root 0 Aug 18 23:47 test



inode
／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／
inode table (inode 表格)
再来讨论一下 inode 这个玩意儿吧！
如前所述 inode 的内容在记录文件的属性以及该文件实际数据是放置在哪几号 block 内！
基本上，inode 记录的文件数据至少有底下这些：(注4)

该文件的存取模式(read/write/excute)；
该文件的拥有者与群组(owner/group)；
该文件的容量；
该文件创建或状态改变的时间(ctime)；
最近一次的读取时间(atime)；
最近修改的时间(mtime)；
定义文件特性的旗标(flag)，如 SetUID...；
该文件真正内容的指向 (pointer)；
inode 的数量与大小也是在格式化时就已经固定了，除此之外 inode 还有些什么特色呢？

每个 inode 大小均固定为 128 bytes；
每个文件都仅会占用一个 inode 而已；
承上，因此文件系统能够创建的文件数量与 inode 的数量有关；
系统读取文件时需要先找到 inode，并分析 inode 所记录的权限与用户是否符合，若符合才能够开始实际读取 block 的内容。



fdisk 分区只能分2TB以下的硬盘，，

parted分区命令的笔记　
／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／
命令格式：
    [root@www ~]# parted [装置] [命令 [参数]]
    选项与参数：
    命令功能：
    新增分割：mkpart [primary|logical|extended] [ext3|vfat] 开始 结束
    分割表  ：print
    删除分割：rm [partition]
打印分区表：
    parted /dev/sda print
创建一个512M逻辑分区:
    parted /dev/sda mkpart logical ext3 19.2GB 19.7GB
删除分区８:
    parted /dev/sda rm 8
强制内核升级分割表
    partprobe　强制内核升级分割表


linux 文件压缩的笔记
／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／
几个常见的压缩文件扩展名：
    *.Z         compress 程序压缩的文件；
    *.gz        gzip 程序压缩的文件；
    *.bz2       bzip2 程序压缩的文件；
    *.tar       tar 程序打包的数据，并没有压缩过；
    *.tar.gz    tar 程序打包的文件，其中并且经过 gzip 的压缩
    *.tar.bz2   tar 程序打包的文件，其中并且经过 bzip2 的压缩
gzip 格式：
    [root@www ~]# gzip [-cdtv#] 档名
    [root@www ~]# zcat 档名.gz
    选项与参数：
    -c  ：将压缩的数据输出到萤幕上，可透过数据流重导向来处理；
    -d  ：解压缩的参数；
    -t  ：可以用来检验一个压缩档的一致性～看看文件有无错误；
    -v  ：可以显示出原文件/压缩文件的压缩比等资讯；
    -#  ：压缩等级，-1 最快，但是压缩比最差、-9 最慢，但是压缩比最好！默认是 -6
示例文件前三行内容
    uioqv@uioqvhost:~/uioqv/workspace/linux-workspace/gzip> cat abcde | head -n 3
    abcdeaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
    abcdeaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
    abcdeaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
gzip 压缩示例：
    方式一：
        uioqv@uioqvhost:~/uioqv/workspace/linux-workspace/gzip> gzip -v abcde.cp
        abcde.cp:        98.4% -- replaced with abcde.cp.gz
        uioqv@uioqvhost:~/uioqv/workspace/linux-workspace/gzip> ll
        总用量 8
        -rw-r--r-- 1 uioqv users 1720 1月  15 22:33 abcde
        -rw-r--r-- 1 uioqv users   54 1月  15 22:34 abcde.cp.gz
    方式二：
        uioqv@uioqvhost:~/uioqv/workspace/linux-workspace/gzip> gzip -c abcde.cp > abcde.cp.gz
        uioqv@uioqvhost:~/uioqv/workspace/linux-workspace/gzip> ll
        总用量 12
        -rw-r--r-- 1 uioqv users 1720 1月  15 22:33 abcde
        -rw-r--r-- 1 uioqv users 1720 1月  15 22:34 abcde.cp
        -rw-r--r-- 1 uioqv users   54 1月  15 22:41 abcde.cp.gz
zcat
    uioqv@uioqvhost:~/uioqv/workspace/linux-workspace/gzip> zcat abcde.cp.gz | head -n 3
    abcdeaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
    abcdeaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
    abcdeaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
gzip　解压缩示例:
    uioqv@uioqvhost:~/uioqv/workspace/linux-workspace/gzip> gzip -d abcde.cp.gz
    uioqv@uioqvhost:~/uioqv/workspace/linux-workspace/gzip> ll
    总用量 8
    -rw-r--r-- 1 uioqv users 1720 1月  15 22:33 abcde
    -rw-r--r-- 1 uioqv users 1720 1月  15 22:34 abcde.cp
bzip 格式：
    [root@www ~]# bzip2 [-cdkzv#] 档名
    [root@www ~]# bzcat 档名.bz2
    选项与参数：
    -c  ：将压缩的过程产生的数据输出到萤幕上！
    -d  ：解压缩的参数
    -k  ：保留原始文件，而不会删除原始的文件喔！
    -z  ：压缩的参数
    -v  ：可以显示出原文件/压缩文件的压缩比等资讯；
    -#  ：与 gzip 同样的，都是在计算压缩比的参数， -9 最佳， -1 最快！
bzip 压缩:
    方式一：
        uioqv@uioqvhost:~/uioqv/workspace/linux-workspace/gzip> bzip2 -z abcde.cp
        uioqv@uioqvhost:~/uioqv/workspace/linux-workspace/gzip> ll
        总用量 8
        -rw-r--r-- 1 uioqv users 1720 1月  15 22:33 abcde
        -rw-r--r-- 1 uioqv users   55 1月  15 22:34 abcde.cp.bz2
    方式二：
        uioqv@uioqvhost:~/uioqv/workspace/linux-workspace/gzip> bzip2 -c abcde.cp > abcde.cp.bz2
        uioqv@uioqvhost:~/uioqv/workspace/linux-workspace/gzip> ll
        总用量 12
        -rw-r--r-- 1 uioqv users 1720 1月  15 22:33 abcde
        -rw-r--r-- 1 uioqv users 1720 1月  15 22:34 abcde.cp
        -rw-r--r-- 1 uioqv users   55 1月  15 22:47 abcde.cp.bz2
bzcat
    uioqv@uioqvhost:~/uioqv/workspace/linux-workspace/gzip> bzcat abcde.cp.bz2 | head -n 3
    abcdeaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
    abcdeaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
    abcdeaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
bzip 解压缩：
    uioqv@uioqvhost:~/uioqv/workspace/linux-workspace/gzip> bzip2 -d abcde.cp.bz2
    uioqv@uioqvhost:~/uioqv/workspace/linux-workspace/gzip> ll
    总用量 8
    -rw-r--r-- 1 uioqv users 1720 1月  15 22:33 abcde
    -rw-r--r-- 1 uioqv users 1720 1月  15 22:34 abcde.cp

／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／
打包命令 tar
格式：
    tar 的选项与参数

    [root@www ~]# tar [-j|-z] [cv] [-f 创建的档名] filename... <==打包与压缩
    [root@www ~]# tar [-j|-z] [tv] [-f 创建的档名]             <==察看档名
    [root@www ~]# tar [-j|-z] [xv] [-f 创建的档名] [-C 目录]   <==解压缩
    选项与参数：
    -c  ：创建打包文件，可搭配 -v 来察看过程中被打包的档名(filename)
    -t  ：察看打包文件的内容含有哪些档名，重点在察看『档名』就是了；
    -x  ：解打包或解压缩的功能，可以搭配 -C (大写) 在特定目录解开
          特别留意的是， -c, -t, -x 不可同时出现在一串命令列中。
    -j  ：透过 bzip2 的支持进行压缩/解压缩：此时档名最好为 *.tar.bz2
    -z  ：透过 gzip  的支持进行压缩/解压缩：此时档名最好为 *.tar.gz
    -v  ：在压缩/解压缩的过程中，将正在处理的档名显示出来！
    -f filename：-f 后面要立刻接要被处理的档名！建议 -f 单独写一个选项罗！
    -C 目录    ：这个选项用在解压缩，若要在特定目录解压缩，可以使用这个选项。
    
    其他后续练习会使用到的选项介绍：
    -p  ：保留备份数据的原本权限与属性，常用於备份(-c)重要的配置档
    -P  ：保留绝对路径，亦即允许备份数据中含有根目录存在之意；
    --exclude=FILE：在压缩的过程中，不要将 FILE 打包！
    其实最简单的使用 tar 就只要记忆底下的方式即可：

压　缩：tar -jcv -f filename.tar.bz2 要被压缩的文件或目录名称
查　询：tar -jtv -f filename.tar.bz2
解压缩：tar -jxv -f filename.tar.bz2 -C 欲解压缩的目录

-P 压缩过程中保留根目录
    如果你确定你就是需要备份根目录到 tar 的文件中，那可以使用 -P (大写) 这个选项，请看底下的例子分析：

    范例：将档名中的(根)目录也备份下来，并察看一下备份档的内容档名
    [root@www ~]# tar -jpPcv -f /root/etc.and.root.tar.bz2 /etc
    ....中间过程省略....
    [root@www ~]# tar -jtf /root/etc.and.root.tar.bz2
    /etc/dbus-1/session.conf
    /etc/esd.conf
    /etc/crontab
    # 这次查阅档名不含 -v 选项，所以仅有档名而已！没有详细属性/权限等参数。

仅解开单一文件的方法
    # 1. 先找到我们要的档名，假设解开 shadow 文件好了：
    [root@www ~]# tar -jtv -f /root/etc.tar.bz2 | grep 'shadow'
    -r-------- root/root  1230 2008-09-29 02:21:20 etc/shadow-
    -r-------- root/root   622 2008-09-29 02:21:20 etc/gshadow-
    -r-------- root/root   636 2008-09-29 02:21:25 etc/gshadow
    -r-------- root/root  1257 2008-09-29 02:21:25 etc/shadow  <==这是我们要的！
    # 先搜寻重要的档名！其中那个 grep 是『撷取』关键字的功能！我们会在第三篇说明！
    # 这里您先有个概念即可！那个管线 | 配合 grep 可以撷取关键字的意思！

    # 2. 将该文件解开！语法与实际作法如下：
    [root@www ~]# tar -jxv -f 打包档.tar.bz2 待解开档名
    [root@www ~]# tar -jxv -f /root/etc.tar.bz2 etc/shadow
    etc/shadow
    [root@www ~]# ll etc
    total 8
    -r-------- 1 root root 1257 Sep 29 02:21 shadow  <==呦喝！只有一个文件啦！

打包某目录，但不含该目录下的某些文件
    [root@www ~]# tar -jcv  -f /root/system.tar.bz2 --exclude=/root/etc* \
    > --exclude=/root/system.tar.bz2  /etc /root

仅备份比某个时刻还要新的文件
    # 1. 先由 find 找出比 /etc/passwd 还要新的文件
    [root@www ~]# find /etc -newer /etc/passwd

    [root@www ~]# tar -jcv -f /root/etc.newer.then.passwd.tar.bz2 \
    > --newer-mtime="2008/09/29" /etc/*

／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／
备份工具 dd

格式：
    [root@www ~]# dd if="input_file" of="output_file" bs="block_size" \
    > count="number"
    选项与参数：
    if   ：就是 input file 罗～也可以是装置喔！
    of   ：就是 output file 喔～也可以是装置；
    bs   ：规划的一个 block 的大小，若未指定则默认是 512 bytes(一个 sector 的大小)
    count：多少个 bs 的意思。
／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／
备份工具 cpio

格式：

    [root@www ~]# cpio -ovcB  > [file|device] <==备份
    [root@www ~]# cpio -ivcdu < [file|device] <==还原
    [root@www ~]# cpio -ivct  < [file|device] <==察看
    备份会使用到的选项与参数：
      -o ：将数据 copy 输出到文件或装置上
      -B ：让默认的 Blocks 可以添加至 5120 bytes ，默认是 512 bytes ！
    　  　 这样的好处是可以让大文件的储存速度加快(请参考 i-nodes 的观念)
    还原会使用到的选项与参数：
      -i ：将数据自文件或装置 copy 出来系统当中
      -d ：自动创建目录！使用 cpio 所备份的数据内容不见得会在同一层目录中，因此我们
           必须要让 cpio 在还原时可以创建新目录，此时就得要 -d 选项的帮助！
      -u ：自动的将较新的文件覆盖较旧的文件！
      -t ：需配合 -i 选项，可用在"察看"以 cpio 创建的文件或装置的内容
    一些可共享的选项与参数：
      -v ：让储存的过程中文件名称可以在萤幕上显示
      -c ：一种较新的 portable format 方式储存
／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／
DOS 与 Linux 的断行字符
    [root@www ~]# dos2unix [-kn] file [newfile]
    [root@www ~]# unix2dos [-kn] file [newfile]
文件编码转换
    [root@www ~]# iconv --list
    [root@www ~]# iconv -f 原本编码 -t 新编码 filename [-o newfile]
    选项与参数：
    --list ：列出 iconv 支持的语系数据
    -f     ：from ，亦即来源之意，后接原本的编码格式；
    -t     ：to ，亦即后来的新编码要是什么格式；
    -o file：如果要保留原本的档案，那么使用 -o 新档名，可以建立新编码档案。
简繁转换
    [root@www vitest]# iconv -f utf8 -t big5 vi.utf8 | \
    > iconv -f big5 -t gb2312 | iconv -f gb2312 -t utf8 -o vi.gb.utf8
／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／
变量中内容的删除与替换

变量配置方式	说明
${变量#关键词}　　若变量内容从头开始的数据符合『关键词』，则将符合的最短数据删除
${变量##关键词}	若变量内容从头开始的数据符合『关键词』，则将符合的最长数据删除

${变量%关键词}   若变量内容从尾向前的数据符合『关键词』，则将符合的最短数据删除
${变量%%关键词}　　若变量内容从尾向前的数据符合『关键词』，则将符合的最长数据删除

${变量/旧字符串/新字符串}　　　若变量内容符合『旧字符串』则『第一个旧字符串会被新字符串取代』
${变量//旧字符串/新字符串}	　　若变量内容符合『旧字符串』则『全部的旧字符串会被新字符串取代』


／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／／
linux startx启动方式

startx -- :1