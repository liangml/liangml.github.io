# Kafka

+ 查看topic
```shell
bin/kafka-topics.sh --list --bootstrap-server 127.0.0.1:9092
```
+ 查看某个topic信息
```shell
bin/kafka-topics.sh --bootstrap-server 127.0.0.1:9092 --describe TOPICNAME
```
+ 查看所有消费组
```shell
bin/kafka-consumer-groups.sh --bootstrap-server 127.0.0.1:9092 --describe --group --all-groups
```
+ 消费消息
```shell
bin/kafka-consumer-groups.sh --zookeeper 127.0.0.1:2181 --topic TOPICNAME
```
+ 查看topic内容
```shell
bin/kafka-console-consumer.sh --bootstrap-server 127.0.0.1:9092 --from-beginning --topic TOPICNAME 
```
+ 创建topic
```bash
bin/kafka-topics.sh --create --bootstrap-server 127.0.0.1:9092 --partitions 1 --replication-factor 1  --topic TOPICNAME 
```
+ 删除topic
```shell
bin/kafka-topics.sh --delete --bootstrap-server 127.0.0.1:9092 --topic TOPICNAME
```
```shell
bin/kafka-topics.sh --zookeeper 127.0.0.1:2181 --list
bin/kafka-topics.sh --zookeeper 127.0.0.1:2181 --describe --topic TOPICNAME
bin/kafka-consumer-groups.sh --bootstrap-server 127.0.0.1:9092 --list
bin/kafka-consumer-groups.sh --bootstrap-server 127.0.0.1:9092 --describe --group GROUPNAME
bin/kafka-consumer-groups.sh --zookeeper 127.0.0.1:2181 --delete --group GROUPNAME
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --from-beginning --topic TOPICNAME
```

---

> 作者: Leon  
> URL: https://blog.liangml.dpdns.org/posts/kafka/  

