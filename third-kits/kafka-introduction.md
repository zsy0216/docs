# Kafka 简单入门

## 阻塞队列

`BlockingQueue`: Java API 自带的接口

- 解决线程通信的问题
- 阻塞方法：put、take

生产者与消费者模式

- 生产者：产生数据的线程
- 消费者：使用数据的线程

实现类

- ArrayBlockingQueue
- LinkedBlockingQueue
- PriorityBlockingQueue、SynchronousQueue、DelayQueue

测试

生产消息间隔为 20 ms，一共生产 100 次。消费者以一个随机时间进行消费(大概率大于20ms) 直到队列消息为空

```java
public class BlockingQueueTest {
	public static void main(String[] args) {
		BlockingQueue queue = new ArrayBlockingQueue(10);
		new Thread(new Producer(queue)).start();
		new Thread(new Consumer(queue)).start();
		new Thread(new Consumer(queue)).start();
		new Thread(new Consumer(queue)).start();
	}
}

class Producer implements Runnable {
	private BlockingQueue<Integer> queue;

	public Producer(BlockingQueue<Integer> queue) {
		this.queue = queue;
	}

	@Override
	public void run() {
		try {
			for (int i = 0; i < 100; i++) {
				Thread.sleep(20);
				queue.put(i);
				System.out.println(Thread.currentThread().getName() + "生产: " + queue.size());
			}
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
}

class Consumer implements Runnable {
	private BlockingQueue<Integer> queue;

	public Consumer(BlockingQueue<Integer> queue) {
		this.queue = queue;
	}

	@Override
	public void run() {
		try {
			while(true) {
				Thread.sleep(new Random().nextInt(1000));
				queue.take();
				System.out.println(Thread.currentThread().getName() + "消费: " + queue.size());
			}
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
}
```

## Kafka 简介

- Kafka 是一个分布式的流媒体平台
- 应用：消息系统、日志收集、用户行为追踪、流式处理

## Kafka 特点

- 高吞吐量、消息持久化、高可靠性、高扩展性

## Kafka 术语

- `Broker`（Kafka 的服务器）、`Zookeeper`（集群管理）
- `Topic`（存放消息的位置供订阅）、`Partition`（分区对Topic）、`Offset`（消息在分区内的索引）
- `Leader Replica`（）、`Follower Replica`（）主从副本

![topic-partition](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20200913084750208.png)

![partition](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20200913084909120.png)