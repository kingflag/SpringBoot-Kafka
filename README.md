# SpringBoot-Kafka

环境信息:
```
System:Ubuntu 16

Java:java version "1.8.0_141"
	 Java(TM) SE Runtime Environment (build 1.8.0_141-b15)
	 Java HotSpot(TM) 64-Bit Server VM (build 25.141-b15, mixed mode)

zookeeper:zookeeper-3.4.5

kafka:kafka_2.11
```

zookeeper添加到环境变量中

```
export JAVA_HOME=/home/Java/jdk1.8.0_141
export JRE_HOME=$JAVA_HOME/jre
export CLASSPATH=.:$JAVA_HOME/lib:$JAVA_HOME/jre/lib
export ZOOKEEPER_HOME=/usr/local/zookeeper-3.4.5
export PATH=$JAVA_HOME/bin:$JAVA_HOME/jre/bin:$ZOOKEEPER_HOME/bin
```

更新环境变量

`source /etc/profile`

**安装配置zookeeper**

复制zoo_sample.cfg到zoo.cfg：

```shell
cp zoo_sample.cfg zoo.cfg
```

编辑zoo.cfg

```
# The number of milliseconds of each tick
tickTime=2000
# The number of ticks that the initial 
# synchronization phase can take
initLimit=10
# The number of ticks that can pass between 
# sending a request and getting an acknowledgement
syncLimit=5
# the directory where the snapshot is stored.
# do not use /tmp for storage, /tmp here is just 
# example sakes.
dataDir=/usr/local/zookeeper-3.4.5/data

dataLogDir=/usr/local/zookeeper-3.4.5/log
# the port at which the clients will connect
clientPort=2181
#
# Be sure to read the maintenance section of the 
# administrator guide before turning on autopurge.
#
# http://zookeeper.apache.org/doc/current/zookeeperAdmin.html#sc_maintenance
#
# The number of snapshots to retain in dataDir
#autopurge.snapRetainCount=3
# Purge task interval in hours
# Set to "0" to disable auto purge feature
#autopurge.purgeInterval=1
server.1=localhost:2888:3888
```

进入zookeeper-3.4.5下的data文件夹

创建myid文件，供  server.1=localhost:2888:3888  使用

`vim myid`
写入1，(注意，此处的数字和server后的值一致)

保存退出！

在zookeeper-3.4.5目录下执行 

`./bin/zkServer.sh start`

然后执行

`./bin/zkServer.sh status`

可以看到

```
JMX enabled by default
Using config: /usr/local/zookeeper-3.4.5/bin/../conf/zoo.cfg
Mode: standalone
```

jps查看

```
8105 Jps
8063 QuorumPeerMain
```

zookeeper启动完成



**安装配置kafka**

启动kafka前要启动zookeeper

下载解压kafka，我使用的是kafka_2.11

可以使用wget http://mirrors.hust.edu.cn/apache/kafka/1.0.0/kafka_2.11-1.0.0.tgz进行下载

进入 `kafka_2.11/config` 文件夹

配置<a href="https://github.com/kingflag/SpringBoot-Kafka/blob/master/appendix/kafka/server.properties" target="_blank">server.properties</a>

主要修改

```
#broker.id需改成正整数，单机为1就好
broker.id=1
#指定端口号
port=9092
#localhost这一项还有其他要修改，详细见下面说明
host.name=localhost
#指定kafka的日志目录
log.dirs=log.dirs=/usr/local/kafka_2.11/kafka-logs
#连接zookeeper配置项，这里指定的是单机，所以只需要配置localhost
zookeeper.connect=localhost:2181
```



配置<a href="https://github.com/kingflag/SpringBoot-Kafka/blob/master/appendix/kafka/zookeeper.properties" target="_blank">zookeeper.properties</a>

```
#数据目录
dataDir=/usr/local/kafka_2.11/zookeeper/data
#客户端端口
clientPort=2181
host.name=localhost
```



配置<a href="https://github.com/kingflag/SpringBoot-Kafka/blob/master/appendix/kafka/producer.properties" target="_blank">producer.properties</a>

```
zookeeper.connect=localhost:2181
```

配置<a href="https://github.com/kingflag/SpringBoot-Kafka/blob/master/appendix/kafka/consumer.properties" target="_blank">consumer.properties</a>

```
zookeeper.connect=localhost:2181
```





