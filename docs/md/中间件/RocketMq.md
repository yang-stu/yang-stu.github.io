## RocketMq事务消息



### 核心设计

RocketMQ 的事务消息基于 **两阶段提交（2PC）** 和 **补偿机制**，通过以下三个阶段确保一致性

#### ***\*阶段 1：发送半消息（Half Message）\****

- **定义**：生产者先发送一条 **半消息** 到 Broker，该消息对消费者不可见。
- **作用**：标记事务中间状态，等待本地事务结果确认。
- **示例代码**：

```
Message msg = new Message("OrderTopic", "TagA", "ORDER_123".getBytes());
producer.send(msg); // 半消息发送
```

#### ***\*阶段 2：执行本地事务\****

- **流程**：

- 1. 生产者执行本地事务（如数据库更新、调用外部接口）。
  2. 根据事务结果返回 `COMMIT`、`ROLLBACK` 或 `UNKNOWN` 状态。

- **状态含义**：

- - `COMMIT`：事务成功，消息可被消费。

  - `ROLLBACK`：事务失败，消息丢弃。

  - `UNKNOWN`：事务未完成，需后续回查。

#### ***\*阶段 3：消息提交或回滚\****

- **提交**：若本地事务成功，生产者发送 `COMMIT`，Broker 将半消息转为可消费状态。
- **回滚**：若本地事务失败，发送 `ROLLBACK`，Broker 删除半消息。
- **回查机制**：若生产者未及时确认（如网络异常），Broker 会定期发起 **事务状态回查**，强制获取最终状态。
