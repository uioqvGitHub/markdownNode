

###iptables配置文件

直接改iptables配置就可以了：vim /etc/sysconfig/iptables。

####1、关闭所有的 INPUT FORWARD OUTPUT 只对某些端口开放。

- 下面是命令实现：

  iptables -P INPUT DROP
  iptables -P FORWARD DROP
  iptables -P OUTPUT DROP

>  再用命令 iptables -L -n 查看 是否设置好， 好看到全部 DROP 了

这样的设置好了，我们只是临时的， 重启服务器还是会恢复原来没有设置的状态
还要使用 service iptables save 进行保存
看到信息 firewall rules 防火墙的规则 其实就是保存在 /etc/sysconfig/iptables
可以打开文件查看 vi /etc/sysconfig/iptables
2、
下面我只打开22端口，看我是如何操作的，就是下面2个语句（一下为命令行模式）
iptables -A INPUT -p tcp --dport 22 -j ACCEPT
iptables -A OUTPUT -p tcp --sport 22 -j ACCEPT
再查看下 iptables -L -n 是否添加上去, 看到添加了
Chain INPUT (policy DROP)
target     prot opt source               destination
ACCEPT     tcp -- 0.0.0.0/0            0.0.0.0/0           tcp dpt:22
Chain FORWARD (policy DROP)
target     prot opt source               destination
Chain OUTPUT (policy DROP)
target     prot opt source               destination
ACCEPT     tcp -- 0.0.0.0/0            0.0.0.0/0           tcp spt:22
现在Linux服务器只打开了22端口，用putty.exe测试一下是否可以链接上去。
可以链接上去了，说明没有问题。
最后别忘记了保存 对防火墙的设置
通过命令：service iptables save 进行保存
重启iptables 
service iptables save && service iptables restart
关闭防火墙
chkconfig iptables off && service iptables stop
iptables -A INPUT -p tcp --dport 22 -j ACCEPT
iptables -A OUTPUT -p tcp --sport 22 -j ACCEPT
针对这2条命令进行一些讲解吧
-A 参数就看成是添加一条 INPUT 的规则
-p 指定是什么协议 我们常用的tcp 协议，当然也有udp 例如53端口的DNS
到时我们要配置DNS用到53端口 大家就会发现使用udp协议的
而 --dport 就是目标端口 当数据从外部进入服务器为目标端口
反之 数据从服务器出去 则为数据源端口 使用 --sport
-j 就是指定是 ACCEPT 接收 或者 DROP 不接收
3、禁止某个IP访问
1台Linux服务器,2台windows xp 操作系统进行访问
Linux服务器ip 192.168.1.99
4/18/2018 linux中iptables配置文件及命令详解详解 - 我的小草鱼 - 博客园
https://www.cnblogs.com/itxiongwei/p/5871075.html 2/12
xp1 ip: 192.168.1.2
xp2 ip: 192.168.1.8
下面看看我2台xp 都可以访问的
192.168.1.2 这是 xp1 可以访问的，
192.168.1.8 xp2 也是可以正常访问的。
那么现在我要禁止 192.168.1.2 xp1 访问， xp2 正常访问，
下面看看演示
通过命令 iptables -A INPUT -p tcp -s 192.168.1.2 -j DROP
这里意思就是 -A 就是添加新的规则， 怎样的规则呢？ 由于我们访问网站使用tcp的，
我们就用 -p tcp , 如果是 udp 就写udp，这里就用tcp了， -s就是 来源的意思，
ip来源于 192.168.1.2 ，-j 怎么做 我们拒绝它 这里应该是 DROP
好，看看效果。好添加成功。下面进行验证 一下是否生效
一直出现等待状态 最后 该页无法显示 ，这是 192.168.1.2 xp1 的访问被拒绝了。
再看看另外一台 xp 是否可以访问， 是可以正常访问的 192.168.1.8 是可以正常访问的
4、如何删除规则
首先我们要知道 这条规则的编号，每条规则都有一个编号
通过 iptables -L -n --line-number 可以显示规则和相对应的编号
num target     prot opt source               destination
1    DROP       tcp -- 0.0.0.0/0            0.0.0.0/0           tcp dpt:3306
2    DROP       tcp -- 0.0.0.0/0            0.0.0.0/0           tcp dpt:21
3    DROP       tcp -- 0.0.0.0/0            0.0.0.0/0           tcp dpt:80
多了 num 这一列， 这样我们就可以 看到刚才的规则对应的是 编号2
那么我们就可以进行删除了
iptables -D INPUT 2
删除INPUT链编号为2的规则。
再 iptables -L -n 查看一下 已经被清除了。
5、过滤无效的数据包
假设有人进入了服务器，或者有病毒木马程序，它可以通过22，80端口像服务器外传送数据。
它的这种方式就和我们正常访问22，80端口区别。它发向外发的数据不是我们通过访问网页请求
而回应的数据包。
下面我们要禁止这些没有通过请求回应的数据包，统统把它们堵住掉。
iptables 提供了一个参数 是检查状态的，下面我们来配置下 22 和 80 端口，防止无效的数据包。
iptables -A OUTPUT -p tcp --sport 22 -m state --state ESTABLISHED -j ACCEPT
可以看到和我们以前使用的：
iptables -A OUTPUT -p tcp --sport 22 -j ACCEPT
多了一个状态判断。
同样80端口也一样， 现在删掉原来的2条规则，
iptables -L -n --line-number    这个是查看规则而且带上编号。我们看到编号就可以
删除对应的规则了。
iptables -D OUTPUT 1     这里的1表示第一条规则。
当你删除了前面的规则， 编号也会随之改变。看到了吧。
好，我们删除了前面2个规则，22端口还可以正常使用，说明没问题了
4/18/2018 linux中iptables配置文件及命令详解详解 - 我的小草鱼 - 博客园
https://www.cnblogs.com/itxiongwei/p/5871075.html 3/12
下面进行保存，别忘记了，不然的话重启就会还原到原来的样子。
service iptables save    进行保存。
Saving firewall rules to /etc/sysconfig/iptables:          [ OK ]
其实就是把刚才设置的规则写入到 /etc/sysconfig/iptables 文件中。
6、DNS端口53设置
下面我们来看看如何设置iptables来打开DNS端口，DNS端口对应的是53
大家看到我现在的情况了吧，只开放22和80端口， 我现在看看能不能解析域名。
host www.google.com    输入这个命令后，一直等待，说明DNS不通
出现下面提示 ：
;; connection timed out; no servers could be reached
ping 一下域名也是不通
[root@localhost ~ping www.google.com
ping: unknown host www.google.com
我这里的原因就是 iptables 限制了53端口。
有些服务器，特别是Web服务器减慢，DNS其实也有关系的，无法发送包到DNS服务器导致的。
下面演示下如何使用 iptables 来设置DNS 53这个端口，如果你不知道 域名服务端口号，你
可以用命令 : grep domain /etc/services
[root@localhost ~grep domain /etc/services
domain          53/tcp                          # name-domain server
domain          53/udp
domaintime      9909/tcp                        # domaintime
domaintime      9909/udp                        # domaintime
看到了吧， 我们一般使用 udp 协议。
好了， 开始设置。。。
iptables -A OUTPUT -p udp --dport 53 -j ACCEPT 
这是我们 ping 一个域名，数据就是从本机出去，所以我们先设置 OUTPUT，
我们按照ping这个流程来设置。
然后 DNS 服务器收到我们发出去的包，就回应一个回来
iptables -A INPUT -p udp --sport 53 -j ACCEPT
同时还要设置
iptables -A INPUT -p udp --dport 53 -j ACCEPT
iptables -A OUTPUT -p udp --sport 53 -j ACCEPT
好了， 下面开始测试下， 可以用 iptables -L -n 查看设置情况，确定没有问题就可以测试了
[root@localhost ~iptables -L -n
Chain INPUT (policy DROP)
target     prot opt source               destination
ACCEPT     tcp -- 0.0.0.0/0            0.0.0.0/0           tcp dpt:22
ACCEPT     tcp -- 0.0.0.0/0            0.0.0.0/0           tcp dpt:80
ACCEPT     udp -- 0.0.0.0/0            0.0.0.0/0           udp spt:53
ACCEPT     udp -- 0.0.0.0/0            0.0.0.0/0           udp dpt:53
Chain FORWARD (policy DROP)
target     prot opt source               destination
4/18/2018 linux中iptables配置文件及命令详解详解 - 我的小草鱼 - 博客园
https://www.cnblogs.com/itxiongwei/p/5871075.html 4/12
Chain OUTPUT (policy DROP)
target     prot opt source               destination
ACCEPT     tcp -- 0.0.0.0/0            0.0.0.0/0           tcp spt:22 state ESTABLISHED
ACCEPT     tcp -- 0.0.0.0/0            0.0.0.0/0           tcp spt:80 state ESTABLISHED
ACCEPT     udp -- 0.0.0.0/0            0.0.0.0/0           udp dpt:53
ACCEPT     udp -- 0.0.0.0/0            0.0.0.0/0           udp spt:53
可以测试一下 是否 DNS 可以通过iptables 了。
[root@localhost ~host www.google.com
www.google.com is an alias for www.l.google.com.
www.l.google.com is an alias for www-china.l.google.com.
www-china.l.google.com has address 64.233.189.104
www-china.l.google.com has address 64.233.189.147
www-china.l.google.com has address 64.233.189.99
正常可以解析 google 域名。
ping 方面可能还要设置些东西。
用 nslookup 看看吧
[root@localhost ~nslookup

> www.google.com
> Server:         192.168.1.1
> Address:        192.168.1.1#53
> Non-authoritative answer:
> www.google.com canonical name = www.l.google.com.
> www.l.google.com        canonical name = www-china.l.google.com.
> Name:   www-china.l.google.com
> Address: 64.233.189.147
> Name:   www-china.l.google.com
> Address: 64.233.189.99
> Name:   www-china.l.google.com
> Address: 64.233.189.104
> 说明本机DNS正常， iptables 允许53这个端口的访问。
> 7、iptables对ftp的设置
> 现在我开始对ftp端口的设置，按照我们以前的视频，添加需要开放的端口
> ftp连接端口有2个 21 和 20 端口，我现在添加对应的规则。
> [root@localhost rootiptables -A INPUT -p tcp --dport 21 -j ACCEPT
> [root@localhost rootiptables -A INPUT -p tcp --dport 20 -j ACCEPT
> [root@localhost rootiptables -A OUTPUT -p tcp --sport 21 -j ACCEPT
> [root@localhost rootiptables -A OUTPUT -p tcp --sport 20 -j ACCEPT
> 好，这样就添加完了，我们用浏览器访问一下ftp,出现超时。
> 所以我刚才说 ftp 是比较特殊的端口，它还有一些端口是 数据传输端口，
> 例如目录列表， 上传 ，下载 文件都要用到这些端口。
> 而这些端口是 任意 端口。。。 这个 任意 真的比较特殊。
> 如果不指定什么一个端口范围， iptables 很难对任意端口开放的，
> 如果iptables允许任意端口访问， 那和不设置防火墙没什么区别，所以不现实的。
> 那么我们的解决办法就是 指定这个数据传输端口的一个范围。
> 下面我们修改一下ftp配置文件。
> 4/18/2018 linux中iptables配置文件及命令详解详解 - 我的小草鱼 - 博客园
> https://www.cnblogs.com/itxiongwei/p/5871075.html 5/12
> 我这里使用vsftpd来修改演示，其他ftp我不知道哪里修改，大家可以找找资料。
> [root@localhost rootvi /etc/vsftpd.conf
> 在配置文件的最下面 加入
> pasv_min_port=30001
> pasv_max_port=31000
> 然后保存退出。
> 这两句话的意思告诉vsftpd, 要传输数据的端口范围就在30001到31000 这个范围内传送。
> 这样我们使用 iptables 就好办多了，我们就打开 30001到31000 这些端口。
> [root@localhost rootiptables -A INPUT -p tcp --dport 30001:31000 -j ACCEPT
> [root@localhost rootiptables -A OUTPUT -p tcp --sport 30001:31000 -j ACCEPT
> [root@localhost rootservice iptables save
> 最后进行保存， 然后我们再用浏览器范围下 ftp。可以正常访问
> 用个账号登陆上去，也没有问题，上传一些文件上去看看。
> 看到了吧，上传和下载都正常。。 再查看下 iptables 的设置
> [root@localhost rootiptables -L -n
> Chain INPUT (policy DROP)
> target     prot opt source               destination
> ACCEPT     tcp -- 0.0.0.0/0            0.0.0.0/0          tcp dpt:22
> ACCEPT     tcp -- 0.0.0.0/0            0.0.0.0/0          tcp dpt:21
> ACCEPT     tcp -- 0.0.0.0/0            0.0.0.0/0          tcp dpt:20
> ACCEPT     tcp -- 0.0.0.0/0            0.0.0.0/0          tcp dpts:30001:31000
> Chain FORWARD (policy DROP)
> target     prot opt source               destination
> Chain OUTPUT (policy DROP)
> target     prot opt source               destination
> ACCEPT     tcp -- 0.0.0.0/0            0.0.0.0/0          tcp spt:22
> ACCEPT     tcp -- 0.0.0.0/0            0.0.0.0/0          tcp spt:21
> ACCEPT     tcp -- 0.0.0.0/0            0.0.0.0/0          tcp spt:20
> ACCEPT     tcp -- 0.0.0.0/0            0.0.0.0/0          tcp spts:30001:31000
> 这是我为了演示ftp特殊端口做的简单规则，大家可以添加一些对数据包的验证
> 例如 -m state --state ESTABLISHED,RELATED 等等要求更加高的验证
> 例子1：
> iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
#如上例，添加一条入站规则：对进来的包的状态进行检测。已经建立tcp连接的包以及该连接相关的包允许通过！
#ESTABLISHED：已建立的链接状态。RELATED：该数据包与本机发出的数据包有关。
例子2：
iptables -A INPUT -i lo -j ACCEPT
4/18/2018 linux中iptables配置文件及命令详解详解 - 我的小草鱼 - 博客园
https://www.cnblogs.com/itxiongwei/p/5871075.html 6/12
#-i 参数是指定接口，这里的接口是lo ，lo就是Loopback（本地环回接口），意思就允许本地环回接口在INPUT表的
所有数据通信。
例子3：
-A INPUT -j REJECT --reject-with icmp-host-prohibited搜索
-A FORWARD -j REJECT --reject-with icmp-host-prohibited
# 这两条的意思是在INPUT表和FORWARD表中拒绝所有其他不符合上述任何一条规则的数据包。并且发送一条host
prohibited的消息给被拒绝的主机。（一般不发送，就不添加这两条）
以下是iptables相关命令和参数：
ilter 这个规则表是预设规则表，拥有 INPUT、FORWARD 和 OUTPUT 三个规则链，这
个规则表顾名思义是用来进行封包过滤的理动作（例如：
DROP、 LOG、 ACCEPT 或 REJECT），我们会将基本规则都建立在此规则表中。
主要包含：命令表     
              用来增加(-A、-I)删除(-D)修改(-R)查看(-L)规则等；
              常用参数   用来指定协议(-p)、源地址(-s)、源端口(--sport)、目的地址(-d)、目的端口(--dport)、
              进入网卡(-i)、出去网卡(-o)等设定包信息（即什么样的包）；  用来描述要处理包的信息。
              常用处理动作    用   -j   来指定对包的处理(ACCEPT、DROP、REJECT、REDIRECT等)。
1、常用命令列表：  常用命令(-A追加规则、-D删除规则、-R修改规则、-I插入规则、-L查看规则)
命令 -A, --append
范例 iptables -A INPUT ...
说明 新增规则(追加方式)到某个规则链(这里是INPUT规则链)中，该规则将会成为规则链中的最后一条规则。
命令 -D, --delete
范例 iptables -D INPUT --dport 80 -j DROP
      iptables -D INPUT 1
说明 从某个规则链中删除一条规则，可以输入完整规则，或直接指定规则编号加以删除。
命令 -R, --replace
范例 iptables -R INPUT 1 -s 192.168.0.1 -j DROP
说明 取代现行规则，规则被取代后并不会改变顺序。(1是位置)
命令 -I, --insert
范例 iptables -I INPUT 1 --dport 80 -j ACCEPT
说明 插入一条规则，原本该位置(这里是位置1)上的规则将会往后移动一个顺位。
命令 -L, --list
范例 iptables -L INPUT
4/18/2018 linux中iptables配置文件及命令详解详解 - 我的小草鱼 - 博客园
https://www.cnblogs.com/itxiongwei/p/5871075.html 7/12
说明 列出某规则链中的所有规则。
命令 -F, --flush
范例 iptables -F INPUT
说明 删除某规则链(这里是INPUT规则链)中的所有规则。
命令 -Z, --zero
范例 iptables -Z INPUT
说明 将封包计数器归零。封包计数器是用来计算同一封包出现次数，是过滤阻断式攻击不可或缺的工具。
命令 -N, --new-chain
范例 iptables -N allowed
说明 定义新的规则链。
命令 -X, --delete-chain
范例 iptables -X allowed
说明 删除某个规则链。
命令 -P, --policy
范例 iptables -P INPUT DROP
说明 定义过滤政策。 也就是未符合过滤条件之封包，预设的处理方式。
命令 -E, --rename-chain
范例 iptables -E allowed disallowed
说明 修改某自订规则链的名称。
2、常用封包比对参数：(-p协议、-s源地址、-d目的地址、--sport源端口、--dport目的端口、-i 进入网卡、-o 出去
网卡)
参数           -p, --protocol                    (指定协议)
范例 iptables -A INPUT -p tcp           (指定协议)      -p all   所有协议，  -p !tcp 去除tcp外的所有协议。
说明 比对通讯协议类型是否相符，可以使用 ! 运算子进行反向比对，例如：-p ! tcp ，
意思是指除 tcp 以外的其它类型，包含udp、icmp ...等。如果要比对所有类型，则可以使用 all 关键词，例如：-
p all。
参数          -s, --src, --source                  (指定源地址，指定源端口--sport)
例如： iptables -A INPUT -s 192.168.1.1
说明 用来比对封包的来源 IP，可以比对单机或网络，比对网络时请用数字来表示屏蔽，
例如：-s 192.168.0.0/24，比对 IP 时可以使用 ! 运算子进行反向比对，
例如：-s ! 192.168.0.0/24。
参数         -d, --dst, --destination           (指定目的地址，指定目的端口--dport)
例如： iptables -A INPUT -d 192.168.1.1
说明 用来比对封包的目的地 IP，设定方式同上。
参数         -i, --in-interface                      (指定入口网卡)      -i  eth+   所有网卡
例如： iptables -A INPUT -i eth0
4/18/2018 linux中iptables配置文件及命令详解详解 - 我的小草鱼 - 博客园
https://www.cnblogs.com/itxiongwei/p/5871075.html 8/12
说明 用来比对封包是从哪片网卡进入，可以使用通配字符 + 来做大范围比对，
例如：-i eth+ 表示所有的 ethernet 网卡，也以使用 ! 运算子进行反向比对，
例如：-i ! eth0。
参数        -o, --out-interface                   (指定出口网卡)
例如： iptables -A FORWARD -o eth0
说明 用来比对封包要从哪片网卡送出，设定方式同上。
参数        --sport, --source-port              (源端口)
例如： iptables -A INPUT -p tcp --sport 22
说明 用来比对封包的来源端口号，可以比对单一埠，或是一个范围，
例如：--sport 22:80，表示从 22 到 80 端口之间都算是符合件，
如果要比对不连续的多个埠，则必须使用 --multiport 参数，详见后文。比对埠号时，可以使用 ! 运算子进行反向比
对。
参数        --dport, --destination-port     (目的端口)
例如： iptables -A INPUT -p tcp --dport 22
说明 用来比对封包的目的端口号，设定方式同上。
参数       --tcp-flags                                  (只过滤TCP中的一些包，比如SYN包，ACK包，FIN包，RST包
等等)
例如： iptables -p tcp --tcp-flags SYN,FIN,ACK SYN
说明  比对 TCP 封包的状态旗号，参数分为两个部分，第一个部分列举出想比对的旗号，
           第二部分则列举前述旗号中哪些有被设，未被列举的旗号必须是空的。TCP 状态旗号包括：SYN（同步）、
ACK（应答）、
FIN（结束）、RST（重设）、URG（紧急）PSH（强迫推送） 等均可使用于参数中，除此之外还可以使用关键
词 ALL 和
NONE 进行比对。比对旗号时，可以使用 ! 运算子行反向比对。
参数 --syn
例如： iptables -p tcp --syn
说明 用来比对是否为要求联机之 TCP 封包，与 iptables -p tcp --tcp-flags SYN,
FIN,ACK SYN 的作用完全相同，如果使用 !运算子，可用来比对非要求联机封包。
参数 -m multiport --source-port
例如： iptables -A INPUT -p tcp -m multiport --source-port 22,53,80,110
说明 用来比对不连续的多个来源埠号，一次最多可以比对 15 个埠，可以使用 ! 
运算子进行反向比对。
参数 -m multiport --destination-port
例如： iptables -A INPUT -p tcp -m multiport --destination-port 22,53,80,110
说明 用来比对不连续的多个目的地埠号，设定方式同上。
参数 -m multiport --port
例如： iptables -A INPUT -p tcp -m multiport --port 22,53,80,110
说明 这个参数比较特殊，用来比对来源埠号和目的埠号相同的封包，设定方式同上。
4/18/2018 linux中iptables配置文件及命令详解详解 - 我的小草鱼 - 博客园
https://www.cnblogs.com/itxiongwei/p/5871075.html 9/12
注意：在本范例中，如果来源端口号为 80目的地埠号为 110，这种封包并不算符合条件。
参数 --icmp-type
例如： iptables -A INPUT -p icmp --icmp-type 8
说明 用来比对 ICMP 的类型编号，可以使用代码或数字编号来进行比对。
请打 iptables -p icmp --help 来查看有哪些代码可用。
参数 -m limit --limit
例如： iptables -A INPUT -m limit --limit 3/hour
说明 用来比对某段时间内封包的平均流量，上面的例子是用来比对：每小时平均流量是
否超过一次 3 个封包。 除了每小时平均次外，也可以每秒钟、每分钟或每天平均一次，
默认值为每小时平均一次，参数如后： /second、 /minute、/day。 除了进行封数量的
比对外，设定这个参数也会在条件达成时，暂停封包的比对动作，以避免因骇客使用洪水
攻击法，导致服务被阻断。
参数 --limit-burst
范例 iptables -A INPUT -m limit --limit-burst 5
说明 用来比对瞬间大量封包的数量，上面的例子是用来比对一次同时涌入的封包是否超
过 5 个（这是默认值），超过此上限的封将被直接丢弃。使用效果同上。
参数 -m mac --mac-source
范例 iptables -A INPUT -m mac --mac-source 00:00:00:00:00:01
说明 用来比对封包来源网络接口的硬件地址，这个参数不能用在 OUTPUT 和 Postrouting规则炼上，这是因为封包
要送出到网后，才能由网卡驱动程序透过 ARP 通讯协议查出目的地的 MAC 地址，所以 iptables 在进行封包比对
时，并不知道封包会送到个网络接口去。
参数 --mark
范例 iptables -t mangle -A INPUT -m mark --mark 1
说明 用来比对封包是否被表示某个号码，当封包被比对成功时，我们可以透过 MARK 处理动作，将该封包标示一个
号码，号码最不可以超过 4294967296。
参数 -m owner --uid-owner
范例 iptables -A OUTPUT -m owner --uid-owner 500
说明 用来比对来自本机的封包，是否为某特定使用者所产生的，这样可以避免服务器使用
root 或其它身分将敏感数据传送出，可以降低系统被骇的损失。可惜这个功能无法比对出
来自其它主机的封包。
参数 -m owner --gid-owner
范例 iptables -A OUTPUT -m owner --gid-owner 0
说明 用来比对来自本机的封包，是否为某特定使用者群组所产生的，使用时机同上。
参数 -m owner --pid-owner
范例 iptables -A OUTPUT -m owner --pid-owner 78
说明 用来比对来自本机的封包，是否为某特定行程所产生的，使用时机同上。
参数 -m owner --sid-owner
范例 iptables -A OUTPUT -m owner --sid-owner 100
说明 用来比对来自本机的封包，是否为某特定联机（Session ID）的响应封包，使用时
4/18/2018 linux中iptables配置文件及命令详解详解 - 我的小草鱼 - 博客园
https://www.cnblogs.com/itxiongwei/p/5871075.html 10/12
机同上。
参数 -m state --state
范例 iptables -A INPUT -m state --state RELATED,ESTABLISHED
说明 用来比对联机状态，联机状态共有四种：INVALID、ESTABLISHED、NEW 和 RELATED。
INVALID 表示该封包的联机编号（Session ID）无法辨识或编号不正确。
ESTABLISHED 表示该封包属于某个已经建立的联机。
NEW 表示该封包想要起始一个联机（重设联机或将联机重导向）。
RELATED 表示该封包是属于某个已经建立的联机，所建立的新联机。例如：FTP-DATA 联机必定是源自某个 FTP 联
机。
3、常用的处理动作： (-j  指定对满足条件包的处理，常用动作有ACCEPT接受报、DROP丢弃报、
REJECT丢弃报并通知对方、REDIRECT重定向包等)
-j   参数用来指定要进行的处理动作，常用的处理动作包括：ACCEPT、REJECT、DROP、REDIRECT、
MASQUERADE、LOG、DNAT、SNAT、MIRROR、QUEUE、RETURN、MARK，分别说明如下：
ACCEPT        将封包放行，进行完此处理动作后，将不再比对其它规则，直接跳往下一个规则链
（natostrouting）。
REJECT          拦阻该封包，并传送封包通知对方，可以传送的封包有几个选择：ICMP port-unreachable、
ICMP echo-reply 或是 tcp-reset（这个封包会要求对方关闭联机），进行完此处理动作后，将不再比对其它规则，
直接中断过滤程序。
例如：iptables -A FORWARD -p TCP --dport 22 -j REJECT --reject-with tcp-reset
DROP            丢弃封包不予处理，进行完此处理动作后，将不再比对其它规则，直接中断过滤程序。
REDIRECT       将封包重新导向到另一个端口（PNAT），进行完此处理动作后，将会继续比对其它规则。
                       这个功能可以用来实作通透式porxy 或用来保护 web 服务器。
                例如：iptables -t nat -A PREROUTING -p tcp --dport 80 -j REDIRECT --to-ports 8080
MASQUERADE              改写封包来源 IP 为防火墙 NIC IP，可以指定 port 对应的范围，进行完此处理动作后，
直接跳往下一个规则（mangleostrouting）。这个功能与 SNAT 略有不同，当进行 IP 伪装时，不需指定要伪装成哪
个 IP，IP 会从网卡直接读，当使用拨接连线时，IP 通常是由 ISP 公司的 DHCP 服务器指派的，这个时
候 MASQUERADE 特别有用。
                例如：iptables -t nat -A POSTROUTING -p TCP -j MASQUERADE --to-ports 1024-31000
LOG              将封包相关讯息纪录在 /var/log 中，详细位置请查阅 /etc/syslog.conf 组态档，进行完此处理动作
后，将会继续比对其规则。
                例如：iptables -A INPUT -p tcp -j LOG --log-prefix "INPUT packets"
SNAT             改写封包来源 IP 为某特定 IP 或 IP 范围，可以指定 port 对应的范围，进行完此处理动作后，将直接
跳往下一个规则（mangleostrouting）。
                例如：iptables -t nat -A POSTROUTING -p tcp-o eth0 -j SNAT --to-source 194.236.50.155-
194.236.50.160:1024-32000
DNAT            改写封包目的地 IP 为某特定 IP 或 IP 范围，可以指定 port 对应的范围，进行完此处理动作后，将会
直接跳往下一个规炼（filter:input 或 filter:forward）。
               例如：iptables -t nat -A PREROUTING -p tcp -d 15.45.23.67 --dport 80 -j DNAT --todestination
192.168.1.1-192.168.1.10:80-100
MIRROR        镜射封包，也就是将来源 IP 与目的地 IP 对调后，将封包送回，进行完此处理动作后，将会中断过滤
程序。
4/18/2018 linux中iptables配置文件及命令详解详解 - 我的小草鱼 - 博客园
https://www.cnblogs.com/itxiongwei/p/5871075.html 11/12
QUEUE          中断过滤程序，将封包放入队列，交给其它程序处理。透过自行开发的处理程序，可以进行其它应
用，
                      例如：计算联机费......等。
RETURN        结束在目前规则炼中的过滤程序，返回主规则炼继续过滤，如果把自订规则炼看成是一个子程序，那
么这个动作，就相当提早结束子程序并返回到主程序中。
MARK          将封包标上某个代号，以便提供作为后续过滤的条件判断依据，进行完此处理动作后，将会继续比对其
它规则。
               例如：iptables -t mangle -A PREROUTING -p tcp --dport 22 -j MARK --set-mark 2
四.拓展模块
1.按来源MAC地址匹配
# iptables -t filter -A FORWARD -m --mac-source 00:02:b2:03:a5:f6 -j DROP
拒绝转发来自该MAC地址的数据包
2.按多端口或连续端口匹配
  20:  表示20以后的所有端口
  20:100  表示20到100的端口
:20  表示20之前的所有端口
   -m multiport [--prots, --sports,--dports]
例子：
# iptables -A INPUT -p tcp -m multiport --dports 21,20,25,53,80 -j ACCEPT 【多端口匹配】
# iptables -A INPUT -p tcp --dport 20: -j ACCEPT
# iptables -A INPUT -p tcp --sport 20:80 -j ACCEPT
# iptables -A INPUT -p tcp --sport :80 -j ACCEPT
3.还可以按数据包速率和状态匹配
-m limit --limit 匹配速率 如： -m limit --limit 50/s -j ACCEPT
-m state --state 状态 如： -m state --state INVALID,RELATED -j ACCEPT
4.还可以限制链接数
-m connlimit --connlimit-above n 限制为多少个              
        例如：
        iptables -I FORWARD -p tcp -m connlimit --connlimit-above 9 -j DROP        //表示限制链接数最大为9
个
5、模拟随机丢包率
       iptables -A FORWARD -p icmp -m statistic --mode random --probability 0.31  -j REJECT   //表示31%
的丢包率
       或者
       -m random --average 5 -j DROP 表示模拟丢掉5%比例的包
相关知识：
4/18/2018 linux中iptables配置文件及命令详解详解 - 我的小草鱼 - 博客园
https://www.cnblogs.com/itxiongwei/p/5871075.html 12/12
Linux 中延时模拟
设置延时 3s :
tc qdisc add dev eth0 root netem delay 3000ms
可以在 3000ms 后面在加上一个延时，比如 ’3000ms 200ms‘表示 3000ms ± 200ms ，延时范围 2800 – 3200
之间.
结果显示如下
Linux 中丢包模拟
设置丢包 50% ,iptables 也可以模拟这个，但一下不记的命令了，下次放上来:
tc qdisc change dev eth0 root netem loss 50%
上面的设丢包，如果给后面的 50% 的丢包比率修改成 ’50% 80%’ 时，这时和上面的延时不一样，这是指丢包比率
为 50-80% 之间。