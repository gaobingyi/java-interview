# MQ

### MQ的使用场景

- 解耦
- 异步
- 削峰

### 常用的MQ比较

- RabbitMQ：延时低
- Kafka：吞吐量高
- RocketMQ：功能丰富

- --

### Kafka如何保证消息不丢失

#### 生产端

- 等待ISR集合中的所有副本都同步完成：`acks=-1`，
- 失败重试：`retrys=5`

#### 服务端

- 分区多副本：`--replication-factor 3`
- 至少有一个follower副本保持同步：`min.insync.replicas=2`
- 禁止非同步副本参与leader选举：`unclean.leader.election.enable=false`

#### 消费端

- 消费完成后手动提交消费位移：`enable.auto.commit=false`

### Kafka如何保证消息有序

#### 生产端

- 将需要有序的消息发送到相同的分区
- 失败重试对消息有序的影响：
    - 不允许同时有两个未确认的请求：`max.in.flight.requests.per.connection=1`
    - 或开启幂等性发送：`enable.idempotence=true`，此时`max.in.flight.requests.per.connection=5`

#### 服务端

- 同一分区内的消息是有序的

#### 消费端

- 单线程消费

### Kafka如何保证消息不重复

#### 生产端 & 服务端

- 失败重试对消息重复的影响：开启幂等性发送`enable.idempotence=true`

#### 消费端

- 通过业务代码实现消费逻辑的幂等性
- 或自行管理消费位移，将offset保存到数据库中（与业务处理在同一个事务中）

### Kafka为什么性能好 TODO

- 批量操作：批量发送、批量拉取
- 

- --

### RocketMQ延时消息 TODO

### RocketMQ事务消息

采用两阶段提交，保证本地事务与消息发送的最终一致性

1. 生产者发送消息（第一阶段）
2. 服务端接收消息（半消息，消费者不可见），并响应
3. 生产者执行本地事务，并发送执行结果commit/rollback（第二阶段）
4. 接收执行结果，若commit，则投递到消费者消费，若rollback，则删除消息
5. 二阶段失败补偿：若服务端未收到第二阶段请求，则发起主动事务状态回查

### RocketMQ消费重试 TODO

消费失败重试涉及：延时队列、重试队列、死信队列

> https://rocketmq.apache.org/zh/docs/featureBehavior/10consumerretrypolicy
