# Spring 整合 Kafka

## 1. 引入依赖

```xml
<dependency>
    <groupId>org.springframework.kafka</groupId>
    <artifactId>spring-kafka</artifactId>
</dependency>
```

## 2. 增加相关配置

```yaml
# KafkaProperties
spring:
  kafka:
    bootstrap-servers: localhost:9092
    consumer:
      group-id: test-consumer-group
      enable-auto-commit: true
      auto-commit-interval: 3000 #ms
```

## 3. 测试访问 Kafka

- 生产者：kafkaTemplate.send(topic, data);

- 消费者：

  ```java
  @KafkaListener(topics = {"test"})
  public void handleMessage(ConsumerRecord record){}
  ```

- 实例：先启动 Zookeeper 和 Kafka 服务

```java
@SpringBootTest
public class KafkaTest {
	@Resource
	KafkaProducer kafkaProducer;

	@Test
	public void testKafka() {
		kafkaProducer.sentMessage("test", "hello");
		kafkaProducer.sentMessage("test", "world");
		try {
			Thread.sleep(1000 * 10);
		} catch (InterruptedException e) {
			e.printStackTrace();
		}
	}
}

@Component
class KafkaProducer {
	@Resource
	KafkaTemplate kafkaTemplate;

	public void sentMessage(String topic, String content) {
		kafkaTemplate.send(topic, content);
	}
}

@Component
class KafkaConsumer {
	@KafkaListener(topics = {"test"})
	public void handleMessage(ConsumerRecord record) {
		System.out.println(record.value());
	}
}
```

