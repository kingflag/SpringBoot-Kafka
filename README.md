# SpringBoot-Kafka



<a href="https://github.com/kingflag/SpringBoot-Kafka/blob/master/Ubuntu16%E4%B8%8B%E5%8D%95%E6%9C%BA%E5%AE%89%E8%A3%85%E9%85%8D%E7%BD%AEzookeeper%E5%92%8Ckafka.md" target="_blank">Ubuntu16下单机安装配置zookeeper和kafka教程</a>

kafka启动完成后，使用jps可以查看查是否成功

***application.properties中的kafka配置***

```
#kafka相关配置
spring.kafka.bootstrap-servers=ip:9092
#设置一个默认组
spring.kafka.consumer.group-id=defaultGroup
#key-value序列化反序列化
spring.kafka.consumer.key-deserializer=org.apache.kafka.common.serialization.StringDeserializer
spring.kafka.consumer.value-deserializer=org.apache.kafka.common.serialization.StringDeserializer
spring.kafka.producer.key-serializer=org.apache.kafka.common.serialization.StringSerializer
spring.kafka.producer.value-serializer=org.apache.kafka.common.serialization.StringSerializer
spring.kafka.producer.batch-size=65536
spring.kafka.producer.buffer-memory=524288
```

***KafkaConsumer消费类***

~~~
package com.king.simple.springbootkafka.consumer;

import org.apache.kafka.clients.consumer.ConsumerRecord;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.kafka.annotation.KafkaListener;
import org.springframework.stereotype.Component;

/**
 * KafkaConsumer消费类
 * @author wangguoqi
 * @Date:2018/1/31
 */
@Component
public class KafkaConsumer {
	private Logger logger = LoggerFactory.getLogger(this.getClass());

	@KafkaListener(topics = {"test"})
	public void listen(ConsumerRecord<?, ?> record) {
		System.out.printf("offset = %d,key =%s,value=%s\n", record.offset(), record.key(), record.value());
	}

}
~~~

***Producer生产者***

```
package com.king.simple.springbootkafka.producer;

import org.apache.kafka.clients.producer.ProducerRecord;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.kafka.core.KafkaTemplate;
import org.springframework.stereotype.Component;

/**
 * @author wangguoqi
 * @Date:2018/1/31
 */
@Component
public class KafkaProducer {

	@Autowired
	private KafkaTemplate kafkaTemplate;

	String topic="test";
	public void sendMessage(String key,String data){
		kafkaTemplate.send(new ProducerRecord(topic,key,data));
	}
}
```

***建立一个restful模拟发送***

测试地址( //<http://localhost:8080/kafka/send.do?key=2&data=king-test-message>)

~~~
package com.king.simple.springbootkafka.controller;

import com.king.simple.springbootkafka.producer.KafkaProducer;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

/**
 * @author wangguoqi
 * @Date:2018/1/31
 */
@RestController
public class ProducerController {
	@Autowired
	private KafkaProducer kafkaProducer;

	@RequestMapping(value = "/kafka/send.do", method = RequestMethod.GET)
	public String sendMessage(@RequestParam(value = "key") String key, @RequestParam(value = "data") String data) {
		kafkaProducer.sendMessage(key, data);
		return "sucess";
	}
}
~~~

启动spring-boot程序,在kafka集群,模拟发送topic,检验接收

~~~bash
bin/kafka-console-producer.sh --broker-list  localhost:9092  --topic  test
~~~

访问上面的URL后

可以看到结果

~~~
2018-01-31 17:38:16.890  INFO 2183 --- [nio-8080-exec-1] o.a.kafka.common.utils.AppInfoParser     : Kafka version : 0.10.1.1
2018-01-31 17:38:16.892  INFO 2183 --- [nio-8080-exec-1] o.a.kafka.common.utils.AppInfoParser     : Kafka commitId : f10ef2720b03b247
offset = 5,key =2,value=king-test-message
offset = 6,key =2,value=king-test-message
offset = 7,key =2,value=king-test-message
offset = 8,key =2,value=king-test-message
offset = 9,key =2,value=king-test-message
offset = 10,key =2,value=king-test-message
offset = 11,key =2,value=king-test-message
offset = 12,key =2,value=king-test-message
~~~

直接启动springboot



注意:

官方在springboot1.5.0之后的版本集成了kafka，在springboot1.5.0之前集成会比较麻烦，后续我会更新Spring和kafka的集成。