1.修改 ip 地址

### 配置 zookeeper

- scp 远程拷贝文件出现 permission denied,please try again 的解决办法

  > scp localfile usrname@ip:savepath sudo scp zoo.cfg root@192.168.226.132:/opt/zookeeper-3.5.5/conf  
  > 1.其中必须是 root 用户名 2.目标路径的文件夹有写入的权限

- Error contacting service. It is probably not running

  > 需要在 zoo.cfg 中的 dataDir=/usr/zookeeper/data 的路径 data 下创建 myid 文件
  > ./zkServer.sh status 查看状态 server.1 的 Mode: leader 其他显示为 follower

- Hadoop HA setup : not able to connect to zookeeper Session 0x0 for server null, unexpected error, closing socket connection and attempting reconnect java.net.ConnectException: Connection refused\

  > Change this in zoo.cfg:
  > server.1=192.168.13.134:2888:3888
  > server.2=192.168.13.135:2888:3888
  > server.3=192.168.13.136:2888:3888

  > change
  > server.1=192.168.13.134:2888:3888
  > server.2=192.168.13.135:2889:3889
  > server.3=192.168.13.136:2890:3890

- hdfs zkfc -formatZK
  INFO zookeeper.ClientCnxn: Opening socket connection to server node6/192.168.226.132:2181. Will not attempt to authenticate using SASL (unknown error)
  19/06/30 22:06:37 WARN zookeeper.ClientCnxn: Session 0x0 for server null, unexpected error, closing socket connection and attempting reconnect java.net.ConnectException: 拒绝连接
  > 进入 zookeeper/bin ./zkServer.sh start 启动每个 service 的 zookeeper 服务

## 1. Zookeeper 分布式集群部署

- zoo.cfg

```xml
tickTime=2000
initLimit=10
syncLimit=5
dataDir=usr/zookeeper
clientPort=2181
server.1=192.168.13.134:2888:3888
server.2=192.168.13.135:2889:3889
server.3=192.168.13.136:2890:3890
```

## 2. Hadoop2.X HA 架构与部署

### hdfs 的配置文件

- hdfs-site.xml

```xml
<configuration>
  <property>
  //检查权限，false 则每个人都可以存取
  <name>dfs.permissions</name>
  <value>false</value>
  </property>
  <property>
  <!-- 为namenode集群定义一个services name -->
  <name>dfs.nameservices</name>
  <value>gnx</value>
  </property>
  <property>
  <!--nameservice 包含哪些namenode，为各个namenode起名-->
   <name>dfs.ha.namenodes.gnx</name>
   <value>nn1,nn2</value>
  </property>
  <property>
  <!-- 名为nn1的namenode 的rpc地址和端口号，rpc用来和datanode通讯 -->
  <name>dfs.namenode.rpc-address.gnx.nn1</name>
  <value>node5:9000</value>
  </property>
  <property>
  <name>dfs.namenode.rpc-address.gnx.nn2</name>
  <value>node8:9000</value>
  </property>
  <property>
  <!--名为nn1的namenode 的http地址和端口号，web客户端 -->
  <name>dfs.namenode.http-address.gnx.nn1</name>
  <value>node5:50070</value>
  </property>
  <property>
  <name>dfs.namenode.http-address.gnx.nn2</name>
  <value>node8:50070</value>
  </property>
  <property>
  <!-- namenode间用于共享编辑日志的journal节点列表 -->
  <name>dfs.namenode.shared.edits.dir</name>
  <value>qjournal://node6:8485;node7:8485;node8:8485/abc</value>
      //注：现实生产中,node6,7,8最好选择三个无关的服务器,abc名称随意取
      //上面的;号表示在三台服务器上都加目录abc
  </property>
  <!--journalnode 上用于存放edits日志的目录-->
  <property>
  <name>dfs.journalnode.edits.dir</name>
  <value>/usr/journalnode</value>
  </property>
  <property>
  <name>dfs.client.failover.proxy.provider.gnx</name>
  <value>org.apache.hadoop.hdfs.server.namenode.ha.ConfiguredFailoverProxyProvider</value>
  </property>
  <property>
  <name>dfs.ha.fencing.methods</name>
  <value>sshfence</value>
  </property>
  <property>
  <name>dfs.ha.fencing.ssh.private-key-files</name>
  <value>/home/ubuntu/.ssh/id_rsa</value>
  </property>
  <property>
  <!--配置两个namenode实现自动切换-->
  <name>dfs.ha.automatic-failover.enabled</name>
  <value>true</value>
  </property>
</configuration>
```

- core-site.xml

```xml
<configuration>
    <property>
    <name>fs.defaultFS</name>
    <value>hdfs://gnx</value>
    </property>
    <property>
    <name>hadoop.tmp.dir</name>
    <value>/usr/hadoop/tmp</value>
    </property>
    <property>
    <!--配置zookeeper的主机名和端口号-->
    <name>ha.zookeeper.quorum</name>
    <value>node5:2181,node6:2181,node7:2181</value>
    </property>
</configuration
```

- yarn-site.xm

```xml
<configuration>
 <!--NodeManager上运行的附属服务。需配置成mapreduce_shuffle，才可运行MapReduce程序-->
  <property>
    <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
  </property>
<!--启用resourcemanager ha-->
<property>
    <name>yarn.resourcemanager.ha.enabled</name>
    <value>true</value>
  </property>
<!--声明两台resourcemanager的地址-->
  <property>
    <name>yarn.resourcemanager.cluster-id</name>
    <value>yarn-cluster</value>    //只要不和上面配置的zxl重名就行
  </property>
  <property>
    <name>yarn.resourcemanager.ha.rm-ids</name>
    <value>rm1,rm2</value>
  </property>
  <property>
    <name>yarn.resourcemanager.hostname.rm1</name>
    <value>node5</value>   //此时使用node5就要去hosts文件中去配置
  </property>
  <property>
    <name>yarn.resourcemanager.hostname.rm2</name>
    <value>node8</value>
  </property>
  <property>
//zookeeper的集群
    <name>yarn.resourcemanager.zk-address</name>
    <value>node5:2181,node6:2181,node7:2181</value>
  </property>
</configuration>
```

- mapred-site.xm

```xml
<configuration>
<!--指定mapreduce执行在yarn框架中-->
<property>
     <name>mapreduce.framework.name</name>
     <value>yarn</value>    
</property>
</configuration>
```

> 结果：
> 84707 SecondaryNameNode  
> 118292 ResourceManager //资源管理 两个 namenode 都需要启动
> 117417 NameNode  
> 117017 DFSZKFailoverController//namenode 的 HA 线程  
> 4057 QuorumPeerMain  
> 118760 Jps  
> 84506 DataNode //不需要 nodemanager 知道 datanode 即可知道 nodemanager 的位置

## 3. **HBase 分布式集群部署与设计**

- hbase-site.xml

```xml
// 其中hbase.rootdir的值为配置的hadoop中的fs.defaultFS的值
// 通过vi /home/hadoop-2.5.1/etc/hadoop/core-site.xml查看
<property>
    <name>hbase.rootdir</name>
    <value>hdfs://gnx/hbase</value>
<!-- 如果对于配置的是单节点的hdfs，则可以如下配置
// <value>hdfs://node5:9000/hbase</value>-->
</property>
<property>
    <name>hbase.cluster.distributed</name>
    <value>true</value>
</property>
<property>
    <name>hbase.zookeeper.quorum</name>
    <value>node5,node6,node7</value>
</property>
```

- hbase.env.sh

```sh
# 表示不使用内置zookeeper
export HBASE_MANAGES_ZK=false
export HBASE_CLASSPATH=/opt/hadoop-2.9.2/etc/hadoop
export JAVA_HOME=/opt/jdk1.8.0_201/
export HBASE_PID_DIR=/usr/hbase/pids
```

### 出错

- java.net.UnknownHostException: gnx 或者 hdfs fs -ls / 无法识别 gnx

```xml
<name>dfs.nameservices</name>
<value>gnx</value>

<name>fs.defaultFS</name>
<value>hdfs://gnx</value> #要和nameservices一致的名字  不要加端口 已经配置过了 9000

<name>dfs.client.failover.proxy.provider.gnx</name> #出错点!!!
<value>org.apache.hadoop.hdfs.server.namenode.ha.ConfiguredFailoverProxyProvider</value>

 <name>hbase.rootdir</name>
 <value>hdfs://gnx/hbase</value> #要和defaultFS的服务名一致
```

## kafka 配置

```xml
broker.id=1 # 相当于zookeeper中的myid，id不能相同
port=9092 # 默认值
host.name=node5 #三台各不相同
zookeeper.connect=node5:2181,node6:2181,node7:2181 # 指定zookeeper地址
```

### 出错

- java.lang.NoSuchMethodError: org.apache.zookeeper.ZooKeeper.<init>(Ljava/lang/String;ILorg/apache/zookeeper/Watcher;Z)
  > jar 包冲突 zookeeper/lib 和 kafka/libs 的 zookeeper.jar 冲突
  > kafka_2.12-2.1.1 2.12 指 scala 版本 2.1.1 是 kafka 的版本 官方推荐使用 2.12 的 (彻底解决 noclass 或者 nomethod 问题)

## flume 数据传递

> node6 和 node7 配置 node6=>a2 node7=>a3

```conf

a2.sources = r1
a2.sinks = k1
a2.channels = c1

# 采集某特定文件到HDFS
# 指定一个可执行的shell脚本
a2.sources.r1.type = exec
# linux 命令：tail -F 等同于--follow=name  --retry，实时查看日志文件 根据文件名进行追踪，并保持重试，即该文件被删除或改名后，如果再次创建相同的文件名，会继续追踪
a2.sources.r1.command = tail -F /opt/data/weblog-flume.log
a2.sources.r1.channels = c1

# 采集目录下的所有文件到HDFS
# 配置source组件
# a2.sources.source1.type = spooldir
# a2.sources.source1.spoolDir = /home/hadoop/logs/
# a2.sources.source1.fileHeader = false

# 端口扫描 nc 另一个服务器向本地端口44444发送服务时 接收信息
# a1.sources.r1.type = netcat
# a1.sources.r1.bind = locahost
# a1.sources.r1.port = 44444

a2.channels.c1.type = memory
a2.channels.c1.capacity = 1000
a2.channels.c1.transactionCapacity = 1000
a2.channels.c1.keep-alive = 5

a2.sinks.k1.type = avro
a2.sinks.k1.channel = c1
a2.sinks.k1.hostname = node5
a2.sinks.k1.port = 5555
```

> node5 的 flume 配置文件

```conf
a1.sources = r1
a1.channels = kafkaC hbaseC
a1.sinks = kafkaSink hbaseSink

a1.sources.r1.type = avro
a1.sources.r1.channels = hbaseC kafkaC
a1.sources.r1.bind = node5
a1.sources.r1.port = 5555
a1.sources.r1.threads = 5

#****************************flume + hbase******************************
a1.channels.hbaseC.type = memory
a1.channels.hbaseC.capacity = 10000
a1.channels.hbaseC.transactionCapacity = 10000
a1.channels.hbaseC.keep-alive = 20

a1.sinks.hbaseSink.type = asynchbase
a1.sinks.hbaseSink.table = weblogs #表明
a1.sinks.hbaseSink.columnFamily = info #列簇
a1.sinks.hbaseSink.serializer = org.apache.flume.sink.hbase.SimpleAsyncHbaseEventSerializer
a1.sinks.hbaseSink.channel = hbaseC
a1.sinks.hbaseSink.serializer.payloadColumn = datetime,userid,searchname,retorder,cliorder,cliurl #列簇下的列名

#****************************flume + kafka******************************
a1.channels.kafkaC.type = memory
a1.channels.kafkaC.capacity = 10000
a1.channels.kafkaC.transactionCapacity = 10000
a1.channels.kafkaC.keep-alive = 20

a1.sinks.kafkaSink.channel = kafkaC
a1.sinks.kafkaSink.type = org.apache.flume.sink.kafka.KafkaSink
a1.sinks.kafkaSink.brokerList = node5:9092,node6:9092,node7:9092
a1.sinks.kafkaSink.topic = weblogs
a1.sinks.kafkaSink.zookeeperConnect = node5:2181,node6:2181,node7:2181
a1.sinks.kafkaSink.requiredAcks = 1
a1.sinks.kafkaSink.batchSize = 1
a1.sinks.kafkaSink.serializer.class = kafka.serializer.StringEncoder
```

### linux 命令

- tr 对来自标准输入的字符进行替换、压缩和删除
- cat cat 命令用于连接文件并打印到标准输出设备上
  > tr "\t" "," 将制表符转成逗号
  > cat IMG_FILE > /dev/fd0 把 image file 写到软盘
  > cat weblog.log|tr "\t" "," > weblog.log 把 weblog 文件中的 tab 更换成逗号

## 数据采集/存储/分发流程

- weblogs 项目 node6/7

```java
package main.java;

import java.io.*;
public class ReadWrite {

   static String readFileName;
   static String writeFileName;

   public static void main(String args[]){
       readFileName = args[0];
       writeFileName = args[1];
       try {
           // readInput();
           readFileByLines(readFileName);
       }catch(Exception e){
       }
   }

   public static void readFileByLines(String fileName) {
       FileInputStream fis = null;
       InputStreamReader isr = null;
       BufferedReader br = null;
       String tempString = null;
       try {
           System.out.println("以行为单位读取文件内容，一次读一整行：");
           fis = new FileInputStream(fileName);// FileInputStream
           // 从文件系统中的某个文件中获取字节
           isr = new InputStreamReader(fis,"GBK");
           br = new BufferedReader(isr);
           int count=0;
           while ((tempString = br.readLine()) != null) {
               count++;
               // 显示行号
               Thread.sleep(300);
               String str = new String(tempString.getBytes("UTF8"),"GBK");
               System.out.println("row:"+count+">>>>>>>>"+tempString);
               method1(writeFileName,tempString);
               //appendMethodA(writeFileName,tempString);
           }
           isr.close();
       } catch (IOException e) {
           e.printStackTrace();
       } catch (InterruptedException e) {
           e.printStackTrace();
       } finally {
           if (isr != null) {
               try {
                   isr.close();
               } catch (IOException e1) {
               }
           }
       }
   }

   public static void method1(String file, String conent) {
       BufferedWriter out = null;
       try {
           out = new BufferedWriter(new OutputStreamWriter(
                   new FileOutputStream(file, true)));
           out.write("\n");
           out.write(conent);
       } catch (Exception e) {
           e.printStackTrace();
       } finally {
           try {
               out.close();
           } catch (IOException e) {
               e.printStackTrace();
           }
       }
   }
}
```

> node6/7

```sh
   #/bin/bash
   echo "start log......"
   #第一个参数是原日志文件，第二个参数是日志生成输出文件
   java -jar /opt/jars/weblogs.jar /opt/datas/weblog.log /opt/datas/weblog-flume.log
```

- 编写 flume 的 shell

### 出错

- 运行 jar 文件 Invalid or corrupt jarfile 错误
  > idea 打包错误,application 需要选 Main class 生成的 jar 里有 MF 文件 记录 main 程序名称和依赖的 jar 包
