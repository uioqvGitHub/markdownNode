[TOC]



ip 命令 Note 2018-03-23T15.00.01
========================

### 参数
	-V：显示指令版本信息；
	-s：输出更详细的信息；
	-f：强制使用指定的协议族；
	-4：指定使用的网络层协议是IPv4协议；
	-6：指定使用的网络层协议是IPv6协议；
	-0：输出信息每条记录输出一行，即使内容较多也不换行显示；
	-r：显示主机时，不使用IP地址，而使用主机的域名。

### ip addr
设置添加ip地址

		ip addr add 192.168.17.30/24 dev eth0

### ip route 
显示路由列表

		ip route show

更改默认路由

		ip route add default via 192.168.17.3

显示网络统计数据

		ip -s -s link ls eth0

添加路由
	
		ip route add 192.168.16.0/24 dev eth0  proto kernel  scope link  src 192.168.16.150  metric 100 

删除路由

		ip route add 192.168.16.0/24

### ip link
启用/禁用端口
		ip link set eth0 up|down