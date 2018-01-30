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

编辑<a href="https://github.com/kingflag/SpringBoot-Kafka/blob/master/appendix/zookeeper/zoo.cfg" target="_blank">zoo.cfg</a>

```
initLimit=10
syncLimit=5
dataDir=/usr/local/zookeeper-3.4.5/data
clientPort=2181
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

最后还需要拷贝几个jar文件到kafka的libs目录，分别是zookeeper-xxxx.jar、log4j-xxxx.jar、slf4j-simple-xxxx.jar

可在<a href="https://github.com/kingflag/SpringBoot-Kafka/tree/master/appendix/libs" target="_blank">libs文件夹下</a>找到

启动zookeeper服务:

`bin/zookeeper-server-start.sh config/zookeeper.properties`

如果zookeeper已经启动，可以省略此步。

新开一个窗口启动kafka服务

`bin/kafka-server-start.sh config/server.properties`

可以在窗口看到

```
SLF4J: Class path contains multiple SLF4J bindings.
SLF4J: Found binding in [jar:file:/usr/local/kafka_2.11/libs/slf4j-log4j12-1.7.25.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/usr/local/kafka_2.11/libs/slf4j-simple-1.7.2.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
SLF4J: Actual binding is of type [org.slf4j.impl.Log4jLoggerFactory]
[2018-01-30 11:04:06,719] INFO KafkaConfig values: 
	advertised.host.name = null
	advertised.listeners = null
	advertised.port = null
	alter.config.policy.class.name = null
	authorizer.class.name = 
	auto.create.topics.enable = true
	auto.leader.rebalance.enable = true
	background.threads = 10
	broker.id = 1
	broker.id.generation.enable = true
	broker.rack = null
	compression.type = producer
	connections.max.idle.ms = 600000
	controlled.shutdown.enable = true
	controlled.shutdown.max.retries = 3
	controlled.shutdown.retry.backoff.ms = 5000
	controller.socket.timeout.ms = 30000
	create.topic.policy.class.name = null
	default.replication.factor = 1
	delete.records.purgatory.purge.interval.requests = 1
	delete.topic.enable = false
	fetch.purgatory.purge.interval.requests = 1000
	group.initial.rebalance.delay.ms = 0
	group.max.session.timeout.ms = 300000
	group.min.session.timeout.ms = 6000
	host.name = localhost
	inter.broker.listener.name = null
	inter.broker.protocol.version = 0.11.0-IV2
	leader.imbalance.check.interval.seconds = 300
	leader.imbalance.per.broker.percentage = 10
	listener.security.protocol.map = SSL:SSL,SASL_PLAINTEXT:SASL_PLAINTEXT,TRACE:TRACE,SASL_SSL:SASL_SSL,PLAINTEXT:PLAINTEXT
	listeners = null
	log.cleaner.backoff.ms = 15000
	log.cleaner.dedupe.buffer.size = 134217728
	log.cleaner.delete.retention.ms = 86400000
	log.cleaner.enable = true
	log.cleaner.io.buffer.load.factor = 0.9
	log.cleaner.io.buffer.size = 524288
	log.cleaner.io.max.bytes.per.second = 1.7976931348623157E308
	log.cleaner.min.cleanable.ratio = 0.5
	log.cleaner.min.compaction.lag.ms = 0
	log.cleaner.threads = 1
	log.cleanup.policy = [delete]
	log.dir = /tmp/kafka-logs
	log.dirs = /usr/local/kafka_2.11/kafka-logs
	log.flush.interval.messages = 9223372036854775807
	log.flush.interval.ms = null
	log.flush.offset.checkpoint.interval.ms = 60000
	log.flush.scheduler.interval.ms = 9223372036854775807
	log.flush.start.offset.checkpoint.interval.ms = 60000
	log.index.interval.bytes = 4096
	log.index.size.max.bytes = 10485760
	log.message.format.version = 0.11.0-IV2
	log.message.timestamp.difference.max.ms = 9223372036854775807
	log.message.timestamp.type = CreateTime
	log.preallocate = false
	log.retention.bytes = -1
	log.retention.check.interval.ms = 300000
	log.retention.hours = 168
	log.retention.minutes = null
	log.retention.ms = null
	log.roll.hours = 168
	log.roll.jitter.hours = 0
	log.roll.jitter.ms = null
	log.roll.ms = null
	log.segment.bytes = 1073741824
	log.segment.delete.delay.ms = 60000
	max.connections.per.ip = 2147483647
	max.connections.per.ip.overrides = 
	message.max.bytes = 1000012
	metric.reporters = []
	metrics.num.samples = 2
	metrics.recording.level = INFO
	metrics.sample.window.ms = 30000
	min.insync.replicas = 1
	num.io.threads = 8
	num.network.threads = 3
	num.partitions = 1
	num.recovery.threads.per.data.dir = 1
	num.replica.fetchers = 1
	offset.metadata.max.bytes = 4096
	offsets.commit.required.acks = -1
	offsets.commit.timeout.ms = 5000
	offsets.load.buffer.size = 5242880
	offsets.retention.check.interval.ms = 600000
	offsets.retention.minutes = 1440
	offsets.topic.compression.codec = 0
	offsets.topic.num.partitions = 50
	offsets.topic.replication.factor = 1
	offsets.topic.segment.bytes = 104857600
	port = 9092
	principal.builder.class = class org.apache.kafka.common.security.auth.DefaultPrincipalBuilder
	producer.purgatory.purge.interval.requests = 1000
	queued.max.requests = 500
	quota.consumer.default = 9223372036854775807
	quota.producer.default = 9223372036854775807
	quota.window.num = 11
	quota.window.size.seconds = 1
	replica.fetch.backoff.ms = 1000
	replica.fetch.max.bytes = 1048576
	replica.fetch.min.bytes = 1
	replica.fetch.response.max.bytes = 10485760
	replica.fetch.wait.max.ms = 500
	replica.high.watermark.checkpoint.interval.ms = 5000
	replica.lag.time.max.ms = 10000
	replica.socket.receive.buffer.bytes = 65536
	replica.socket.timeout.ms = 30000
	replication.quota.window.num = 11
	replication.quota.window.size.seconds = 1
	request.timeout.ms = 30000
	reserved.broker.max.id = 1000
	sasl.enabled.mechanisms = [GSSAPI]
	sasl.kerberos.kinit.cmd = /usr/bin/kinit
	sasl.kerberos.min.time.before.relogin = 60000
	sasl.kerberos.principal.to.local.rules = [DEFAULT]
	sasl.kerberos.service.name = null
	sasl.kerberos.ticket.renew.jitter = 0.05
	sasl.kerberos.ticket.renew.window.factor = 0.8
	sasl.mechanism.inter.broker.protocol = GSSAPI
	security.inter.broker.protocol = PLAINTEXT
	socket.receive.buffer.bytes = 102400
	socket.request.max.bytes = 104857600
	socket.send.buffer.bytes = 102400
	ssl.cipher.suites = null
	ssl.client.auth = none
	ssl.enabled.protocols = [TLSv1.2, TLSv1.1, TLSv1]
	ssl.endpoint.identification.algorithm = null
	ssl.key.password = null
	ssl.keymanager.algorithm = SunX509
	ssl.keystore.location = null
	ssl.keystore.password = null
	ssl.keystore.type = JKS
	ssl.protocol = TLS
	ssl.provider = null
	ssl.secure.random.implementation = null
	ssl.trustmanager.algorithm = PKIX
	ssl.truststore.location = null
	ssl.truststore.password = null
	ssl.truststore.type = JKS
	transaction.abort.timed.out.transaction.cleanup.interval.ms = 60000
	transaction.max.timeout.ms = 900000
	transaction.remove.expired.transaction.cleanup.interval.ms = 3600000
	transaction.state.log.load.buffer.size = 5242880
	transaction.state.log.min.isr = 1
	transaction.state.log.num.partitions = 50
	transaction.state.log.replication.factor = 1
	transaction.state.log.segment.bytes = 104857600
	transactional.id.expiration.ms = 604800000
	unclean.leader.election.enable = false
	zookeeper.connect = localhost:2181
	zookeeper.connection.timeout.ms = 6000
	zookeeper.session.timeout.ms = 6000
	zookeeper.set.acl = false
	zookeeper.sync.time.ms = 2000
 (kafka.server.KafkaConfig)
[2018-01-30 11:04:06,856] INFO starting (kafka.server.KafkaServer)
[2018-01-30 11:04:06,861] INFO Connecting to zookeeper on localhost:2181 (kafka.server.KafkaServer)
[2018-01-30 11:04:06,905] INFO Starting ZkClient event thread. (org.I0Itec.zkclient.ZkEventThread)
[2018-01-30 11:04:06,922] INFO Client environment:zookeeper.version=3.4.10-39d3a4f269333c922ed3db283be479f9deacaa0f, built on 03/23/2017 10:13 GMT (org.apache.zookeeper.ZooKeeper)
[2018-01-30 11:04:06,922] INFO Client environment:host.name=kingflag (org.apache.zookeeper.ZooKeeper)
[2018-01-30 11:04:06,923] INFO Client environment:java.version=1.8.0_141 (org.apache.zookeeper.ZooKeeper)
[2018-01-30 11:04:06,924] INFO Client environment:java.vendor=Oracle Corporation (org.apache.zookeeper.ZooKeeper)
[2018-01-30 11:04:06,925] INFO Client environment:java.home=/home/Java/jdk1.8.0_141/jre (org.apache.zookeeper.ZooKeeper)
[2018-01-30 11:04:06,925] INFO Client environment:java.class.path=.:/home/Java/jdk1.8.0_141/lib:/home/Java/jdk1.8.0_141/jre/lib:/usr/local/kafka_2.11/bin/../libs/aopalliance-repackaged-2.5.0-b05.jar:/usr/local/kafka_2.11/bin/../libs/argparse4j-0.7.0.jar:/usr/local/kafka_2.11/bin/../libs/commons-lang3-3.5.jar:/usr/local/kafka_2.11/bin/../libs/connect-api-0.11.0.0.jar:/usr/local/kafka_2.11/bin/../libs/connect-file-0.11.0.0.jar:/usr/local/kafka_2.11/bin/../libs/connect-json-0.11.0.0.jar:/usr/local/kafka_2.11/bin/../libs/connect-runtime-0.11.0.0.jar:/usr/local/kafka_2.11/bin/../libs/connect-transforms-0.11.0.0.jar:/usr/local/kafka_2.11/bin/../libs/guava-20.0.jar:/usr/local/kafka_2.11/bin/../libs/hk2-api-2.5.0-b05.jar:/usr/local/kafka_2.11/bin/../libs/hk2-locator-2.5.0-b05.jar:/usr/local/kafka_2.11/bin/../libs/hk2-utils-2.5.0-b05.jar:/usr/local/kafka_2.11/bin/../libs/jackson-annotations-2.8.5.jar:/usr/local/kafka_2.11/bin/../libs/jackson-core-2.8.5.jar:/usr/local/kafka_2.11/bin/../libs/jackson-databind-2.8.5.jar:/usr/local/kafka_2.11/bin/../libs/jackson-jaxrs-base-2.8.5.jar:/usr/local/kafka_2.11/bin/../libs/jackson-jaxrs-json-provider-2.8.5.jar:/usr/local/kafka_2.11/bin/../libs/jackson-module-jaxb-annotations-2.8.5.jar:/usr/local/kafka_2.11/bin/../libs/javassist-3.21.0-GA.jar:/usr/local/kafka_2.11/bin/../libs/javax.annotation-api-1.2.jar:/usr/local/kafka_2.11/bin/../libs/javax.inject-1.jar:/usr/local/kafka_2.11/bin/../libs/javax.inject-2.5.0-b05.jar:/usr/local/kafka_2.11/bin/../libs/javax.servlet-api-3.1.0.jar:/usr/local/kafka_2.11/bin/../libs/javax.ws.rs-api-2.0.1.jar:/usr/local/kafka_2.11/bin/../libs/jersey-client-2.24.jar:/usr/local/kafka_2.11/bin/../libs/jersey-common-2.24.jar:/usr/local/kafka_2.11/bin/../libs/jersey-container-servlet-2.24.jar:/usr/local/kafka_2.11/bin/../libs/jersey-container-servlet-core-2.24.jar:/usr/local/kafka_2.11/bin/../libs/jersey-guava-2.24.jar:/usr/local/kafka_2.11/bin/../libs/jersey-media-jaxb-2.24.jar:/usr/local/kafka_2.11/bin/../libs/jersey-server-2.24.jar:/usr/local/kafka_2.11/bin/../libs/jetty-continuation-9.2.15.v20160210.jar:/usr/local/kafka_2.11/bin/../libs/jetty-http-9.2.15.v20160210.jar:/usr/local/kafka_2.11/bin/../libs/jetty-io-9.2.15.v20160210.jar:/usr/local/kafka_2.11/bin/../libs/jetty-security-9.2.15.v20160210.jar:/usr/local/kafka_2.11/bin/../libs/jetty-server-9.2.15.v20160210.jar:/usr/local/kafka_2.11/bin/../libs/jetty-servlet-9.2.15.v20160210.jar:/usr/local/kafka_2.11/bin/../libs/jetty-servlets-9.2.15.v20160210.jar:/usr/local/kafka_2.11/bin/../libs/jetty-util-9.2.15.v20160210.jar:/usr/local/kafka_2.11/bin/../libs/jopt-simple-5.0.3.jar:/usr/local/kafka_2.11/bin/../libs/kafka_2.11-0.11.0.0.jar:/usr/local/kafka_2.11/bin/../libs/kafka_2.11-0.11.0.0-sources.jar:/usr/local/kafka_2.11/bin/../libs/kafka_2.11-0.11.0.0-test-sources.jar:/usr/local/kafka_2.11/bin/../libs/kafka-clients-0.11.0.0.jar:/usr/local/kafka_2.11/bin/../libs/kafka-log4j-appender-0.11.0.0.jar:/usr/local/kafka_2.11/bin/../libs/kafka-streams-0.11.0.0.jar:/usr/local/kafka_2.11/bin/../libs/kafka-streams-examples-0.11.0.0.jar:/usr/local/kafka_2.11/bin/../libs/kafka-tools-0.11.0.0.jar:/usr/local/kafka_2.11/bin/../libs/log4j-1.2.17.jar:/usr/local/kafka_2.11/bin/../libs/lz4-1.3.0.jar:/usr/local/kafka_2.11/bin/../libs/maven-artifact-3.5.0.jar:/usr/local/kafka_2.11/bin/../libs/metrics-core-2.2.0.jar:/usr/local/kafka_2.11/bin/../libs/osgi-resource-locator-1.0.1.jar:/usr/local/kafka_2.11/bin/../libs/plexus-utils-3.0.24.jar:/usr/local/kafka_2.11/bin/../libs/reflections-0.9.11.jar:/usr/local/kafka_2.11/bin/../libs/rocksdbjni-5.0.1.jar:/usr/local/kafka_2.11/bin/../libs/scala-library-2.11.11.jar:/usr/local/kafka_2.11/bin/../libs/scala-parser-combinators_2.11-1.0.4.jar:/usr/local/kafka_2.11/bin/../libs/slf4j-api-1.7.25.jar:/usr/local/kafka_2.11/bin/../libs/slf4j-log4j12-1.7.25.jar:/usr/local/kafka_2.11/bin/../libs/slf4j-simple-1.7.2.jar:/usr/local/kafka_2.11/bin/../libs/snappy-java-1.1.2.6.jar:/usr/local/kafka_2.11/bin/../libs/validation-api-1.1.0.Final.jar:/usr/local/kafka_2.11/bin/../libs/zkclient-0.10.jar:/usr/local/kafka_2.11/bin/../libs/zookeeper-3.4.10.jar (org.apache.zookeeper.ZooKeeper)
[2018-01-30 11:04:06,926] INFO Client environment:java.library.path=/usr/java/packages/lib/amd64:/usr/lib64:/lib64:/lib:/usr/lib (org.apache.zookeeper.ZooKeeper)
[2018-01-30 11:04:06,926] INFO Client environment:java.io.tmpdir=/tmp (org.apache.zookeeper.ZooKeeper)
[2018-01-30 11:04:06,926] INFO Client environment:java.compiler=<NA> (org.apache.zookeeper.ZooKeeper)
[2018-01-30 11:04:06,927] INFO Client environment:os.name=Linux (org.apache.zookeeper.ZooKeeper)
[2018-01-30 11:04:06,927] INFO Client environment:os.arch=amd64 (org.apache.zookeeper.ZooKeeper)
[2018-01-30 11:04:06,927] INFO Client environment:os.version=4.13.0-26-generic (org.apache.zookeeper.ZooKeeper)
[2018-01-30 11:04:06,928] INFO Client environment:user.name=root (org.apache.zookeeper.ZooKeeper)
[2018-01-30 11:04:06,929] INFO Client environment:user.home=/root (org.apache.zookeeper.ZooKeeper)
[2018-01-30 11:04:06,929] INFO Client environment:user.dir=/usr/local/kafka_2.11 (org.apache.zookeeper.ZooKeeper)
[2018-01-30 11:04:06,931] INFO Initiating client connection, connectString=localhost:2181 sessionTimeout=6000 watcher=org.I0Itec.zkclient.ZkClient@4f18837a (org.apache.zookeeper.ZooKeeper)
[2018-01-30 11:04:06,983] INFO Opening socket connection to server localhost/127.0.0.1:2181. Will not attempt to authenticate using SASL (unknown error) (org.apache.zookeeper.ClientCnxn)
[2018-01-30 11:04:06,984] INFO Waiting for keeper state SyncConnected (org.I0Itec.zkclient.ZkClient)
[2018-01-30 11:04:06,997] INFO Socket connection established to localhost/127.0.0.1:2181, initiating session (org.apache.zookeeper.ClientCnxn)
[2018-01-30 11:04:07,049] INFO Session establishment complete on server localhost/127.0.0.1:2181, sessionid = 0x1614500f0e60000, negotiated timeout = 6000 (org.apache.zookeeper.ClientCnxn)
[2018-01-30 11:04:07,056] INFO zookeeper state changed (SyncConnected) (org.I0Itec.zkclient.ZkClient)
[2018-01-30 11:04:07,207] INFO Cluster ID = FprtpOE8QD6M9yN4leKPnA (kafka.server.KafkaServer)
[2018-01-30 11:04:07,220] WARN No meta.properties file under dir /usr/local/kafka_2.11/kafka-logs/meta.properties (kafka.server.BrokerMetadataCheckpoint)
[2018-01-30 11:04:07,283] INFO [ThrottledRequestReaper-Fetch]: Starting (kafka.server.ClientQuotaManager$ThrottledRequestReaper)
[2018-01-30 11:04:07,285] INFO [ThrottledRequestReaper-Produce]: Starting (kafka.server.ClientQuotaManager$ThrottledRequestReaper)
[2018-01-30 11:04:07,286] INFO [ThrottledRequestReaper-Request]: Starting (kafka.server.ClientQuotaManager$ThrottledRequestReaper)
[2018-01-30 11:04:07,340] INFO Loading logs. (kafka.log.LogManager)
[2018-01-30 11:04:07,351] INFO Logs loading complete in 10 ms. (kafka.log.LogManager)
[2018-01-30 11:04:07,449] INFO Starting log cleanup with a period of 300000 ms. (kafka.log.LogManager)
[2018-01-30 11:04:07,451] INFO Starting log flusher with a default period of 9223372036854775807 ms. (kafka.log.LogManager)
[2018-01-30 11:04:07,524] INFO Awaiting socket connections on localhost:9092. (kafka.network.Acceptor)
[2018-01-30 11:04:07,529] INFO [Socket Server on Broker 1], Started 1 acceptor threads (kafka.network.SocketServer)
[2018-01-30 11:04:07,551] INFO [ExpirationReaper-1-Produce]: Starting (kafka.server.DelayedOperationPurgatory$ExpiredOperationReaper)
[2018-01-30 11:04:07,554] INFO [ExpirationReaper-1-Fetch]: Starting (kafka.server.DelayedOperationPurgatory$ExpiredOperationReaper)
[2018-01-30 11:04:07,555] INFO [ExpirationReaper-1-DeleteRecords]: Starting (kafka.server.DelayedOperationPurgatory$ExpiredOperationReaper)
[2018-01-30 11:04:07,659] INFO Creating /controller (is it secure? false) (kafka.utils.ZKCheckedEphemeral)
[2018-01-30 11:04:07,661] INFO [ExpirationReaper-1-topic]: Starting (kafka.server.DelayedOperationPurgatory$ExpiredOperationReaper)
[2018-01-30 11:04:07,676] INFO [ExpirationReaper-1-Heartbeat]: Starting (kafka.server.DelayedOperationPurgatory$ExpiredOperationReaper)
[2018-01-30 11:04:07,685] INFO Result of znode creation is: OK (kafka.utils.ZKCheckedEphemeral)
[2018-01-30 11:04:07,689] INFO [ExpirationReaper-1-Rebalance]: Starting (kafka.server.DelayedOperationPurgatory$ExpiredOperationReaper)
[2018-01-30 11:04:07,716] INFO [GroupCoordinator 1]: Starting up. (kafka.coordinator.group.GroupCoordinator)
[2018-01-30 11:04:07,718] INFO [GroupCoordinator 1]: Startup complete. (kafka.coordinator.group.GroupCoordinator)
[2018-01-30 11:04:07,722] INFO [Group Metadata Manager on Broker 1]: Removed 0 expired offsets in 1 milliseconds. (kafka.coordinator.group.GroupMetadataManager)
[2018-01-30 11:04:07,761] INFO [ProducerId Manager 1]: Acquired new producerId block (brokerId:1,blockStartProducerId:0,blockEndProducerId:999) by writing to Zk with path version 1 (kafka.coordinator.transaction.ProducerIdManager)
[2018-01-30 11:04:07,826] INFO [Transaction Coordinator 1]: Starting up. (kafka.coordinator.transaction.TransactionCoordinator)
[2018-01-30 11:04:07,856] INFO [Transaction Coordinator 1]: Startup complete. (kafka.coordinator.transaction.TransactionCoordinator)
[2018-01-30 11:04:07,858] INFO [Transaction Marker Channel Manager 1]: Starting (kafka.coordinator.transaction.TransactionMarkerChannelManager)
[2018-01-30 11:04:07,929] INFO Will not load MX4J, mx4j-tools.jar is not in the classpath (kafka.utils.Mx4jLoader$)
[2018-01-30 11:04:08,008] INFO Creating /brokers/ids/1 (is it secure? false) (kafka.utils.ZKCheckedEphemeral)
[2018-01-30 11:04:08,028] INFO Result of znode creation is: OK (kafka.utils.ZKCheckedEphemeral)
[2018-01-30 11:04:08,036] INFO Registered broker 1 at path /brokers/ids/1 with addresses: EndPoint(localhost,9092,ListenerName(PLAINTEXT),PLAINTEXT) (kafka.utils.ZkUtils)
[2018-01-30 11:04:08,040] WARN No meta.properties file under dir /usr/local/kafka_2.11/kafka-logs/meta.properties (kafka.server.BrokerMetadataCheckpoint)
[2018-01-30 11:04:08,077] INFO Kafka version : 0.11.0.0 (org.apache.kafka.common.utils.AppInfoParser)
[2018-01-30 11:04:08,080] INFO Kafka commitId : cb8625948210849f (org.apache.kafka.common.utils.AppInfoParser)
[2018-01-30 11:04:08,081] INFO [Kafka Server 1], started (kafka.server.KafkaServer)
```



至此单机服务搭建已经全部完成。。。







