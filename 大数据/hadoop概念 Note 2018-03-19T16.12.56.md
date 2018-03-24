hadoop概念 Note 2018-03-19T16.12.56
========================

### hdfs
hadoop distributed file system  分布式文件系统,主节点存储元信息，从节点存储数据本身 

### 设置集群bolck的备份数
1.  配置文件hdfs-site.xml
###
    <property>
      	<name>dfs.replication</name>
      	<value>1</value>
    </property>
2.  通过命令修改备份数
###
    bin/hadoop fs -setrep -R 1 /
    注意：方法二可以改变整个HDFS里面的备份数，不需要重启HDFS系统，
            而方法一需要重启HDFS系统才能生效
### 负载均衡
机器与机器之间磁盘利用率不平衡是HDFS集群非常容易出现的情况
 尤其是在DataNode节点出现故障或在现有的集群上增添新的DataNode的时候
分析数据块分布和重新均衡DataNode上的数据分布的工具

    $HADOOP_HOME/bin/start-balancer.sh -t 10%
负载均衡程序作为一个与独立的进程NameNode进程分开执行

### NameNode
1.  Namenode是一个中心服务器，单一节点，负责管理文件系统的名字空间(namespace)以及客户端对文件的访问
2.  文件操作，NameNode负责文件元数据的操作，DataNode负责处理文件内容的读写请求，数据流不经过NameNode，只会询问它跟那个DataNode联系
3.  副本存放在那些DataNode上由NameNode来控制，根据全局情况做出块放置决定，读取文件时NameNode尽量让用户先读取最近的副本，降低带宽消耗和读取时延
4.  NameNode全权管理数据块的复制，它周期性地从集群中的每个DataNode接收心跳信号和块状态报告(BlockReport)。接收到心跳信号意味着该DataNode节点工作正常。块状态报告包含了一个该DataNode上所有数据块的列表。 
### DataNode
1. 一个数据块在DataNode上以文件存储在磁盘上，包括两个文件，一个是数据本身，一个是元数据包括数据块的长度，块数据的校验和，以及时间戳
DataNode启动后向NameNode注册，通过后，周期性（1小时）的向NameNode上报所有的块信息
2. 心跳是每3秒一次，心跳返回结果带有NameNode给该DataNode的命令，如复制块数据到另一台机器，或删除某个数据块。如果超过10分钟NameNode没有收到某个DataNode 的心跳信息，则NameNode认为该DataNode节点已死亡不可用。
3. 当DN读取block的时候，它会计算checksum；DN在其文件创建后三周验证其checksum。
    - 如果计算后的checksum，与block创建时值不一样，说明该block已经损坏。
    - client读取其它DN上的block；NN标记该块已经损坏，然后复制block达到预期设置的文件备份数；

### SecondaryNameNode
1. 辅助的NameNode
2. 周期性将EditsLog文件合并
3. 工作原理，如图所示
![截图](file://media/1590260827.png)

4. 工作流程
    - secondarynamenode通知namenode切换edits文件
    - secondarynamenode从namenode获得fsimage和edits(通过http)
    - secondarynamenode将fsimage载入内存，然后开始合并edits
    - secondarynamenode将新的fsimage发回给namenode
    - namenode用新的fsimage替换旧的fsimage
5. 什么时候checkpiont
    - fs.checkpoint.period 指定两次checkpoint的最大时间间隔，默认3600秒。
    - fs.checkpoint.size 规定edits文件的最大值，一旦超过这个值则强制checkpoint，不管是否到达最大时间间隔。默认大小是64M


### 各端口web系统作用
1.  http://master:50070 用以查看hdfs的各种信息
2.  http://master:18088 用来展示yarn上运行的应用程序的执行状态和进程信息
3.  hdfs://master:9000   hdfs的文件系统的入口地址

### hadoopdata 目录结构
	1. **NameNode**
	    1. **name**             ==*NameNode启动时加载*==
	       1. **current**       ==*元数据*==
	            1. edits        
	            2. fsimages
	       2. **in_use.lock**   ==*保证只能有一个NN加载current目录*==
	    2. **namesecondary**    ==*SecondaryNameNode启动时加载*==
	2. **DataNode**
    

### hadoop 机架感知
默认情况下，hadoop的机架感知是没有被启用的
#### 启用机架感知
启用机架感知功能，在NameNode所在机器的core-site.xml中配置

    <property>
        <name>topology.script.file.name</name>
        <value>/path/to/script</value>
        <!--value的值是一个脚本-->
    </property>


### hadoop 安全模式
一种保护机制，处在安全模式的集群不对外提供写操作，会对数据块的完整性校验
###
    频繁的启动或关闭namenode 会自动进入安全模式
#### 进入
    hadoop dfsadmin -safemode enter
#### 强行离开
    hadoop dfsadmin -safemode leave
#### 状态
    hadoop dfsadmin -safemode get
#### 等待安全模式自动退出
    hadoop dfsadmin -safemode wait
    
### hadoop fs 命令与接口api
    -ls <path>
    #表示对HDFS下一级目录的查看
    -lsr <path>
    #表示对HDFS目录的递归查看
    -mkdir <path>
    #创建目录
    -put <src><des>
    #从本地上传文件到HDFS上
        #或 -copyFromLocal<src><des>
    -get <des>
    #从HDFS下载文件到本地
        #或 -copyToLocal <des>
    -cat <src>
    #查看文件内容
    -rm(r) <path>
    #删除文件(夹)