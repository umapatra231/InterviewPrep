Here’s the full Kafka interview guide in clean .md format (ready to copy-paste into a GitHub repo):

markdown
Copy
# Apache Kafka Interview Guide for Senior Java Developers (2025)

## Table of Contents
1. [Core Concepts](#1-core-concepts)  
2. [Producers](#2-producers-java-client)  
3. [Consumers](#3-consumers-java-client)  
4. [Streams & KSQL](#4-kafka-streams--ksql)  
5. [Spring Integration](#5-integration-with-javaspring)  
6. [Performance & Tuning](#6-performance--tuning)  
7. [Fault Tolerance](#7-fault-tolerance--reliability)  
8. [Security](#8-security)  
9. [Monitoring](#9-monitoring--observability)  
10. [Advanced Scenarios](#10-advanced-scenarios)  
11. [Troubleshooting](#11-troubleshooting)  

---

## 1. Core Concepts

### Q1. Explain Kafka’s architecture (Brokers, Topics, Partitions, Producers, Consumers)
**Key Points:**  
- **Brokers:** Manage message storage and replication.  
- **Topics:** Logical channels for message categorization.  
- **Partitions:** Enable parallelism and scalability.  
- **Producers/Consumers:** Write/read messages to/from topics.  
- **ZooKeeper/KRaft:** Coordination and metadata management.

---

## 2. Producers (Java Client)

### Q2. How do you ensure "exactly-once" semantics in a Java producer?
```java
// Enable idempotence and transactions
props.put(ProducerConfig.ENABLE_IDEMPOTENCE_CONFIG, "true");
props.put(ProducerConfig.TRANSACTIONAL_ID_CONFIG, "txn-1");

try (KafkaProducer<String, String> producer = new KafkaProducer<>(props)) {
    producer.initTransactions();
    producer.beginTransaction();
    producer.send(new ProducerRecord<>("orders", "order-123", "Pending"));
    producer.commitTransaction();
}
Q3. What is a Partitioner? Write a custom partitioner
java
Copy
public class HeaderBasedPartitioner implements Partitioner {
    @Override
    public int partition(String topic, Object key, byte[] keyBytes, 
                         Object value, byte[] valueBytes, Cluster cluster) {
        Headers headers = (Headers) value;
        Header routingHeader = headers.lastHeader("routing-key");
        return Math.abs(routingHeader.hashCode()) % cluster.partitionCountForTopic(topic);
    }
}
3. Consumers (Java Client)
Q4. Handle consumer lag in a high-throughput Java app
Strategies:

Increase fetch.min.bytes to reduce roundtrips.

Tune max.poll.records for batch processing.

Scale consumer instances within the same group.

Q5. Implement manual offset commits
java
Copy
consumer.subscribe(Collections.singleton("orders"));
while (true) {
    ConsumerRecords<String, String> records = consumer.poll(Duration.ofMillis(100));
    for (ConsumerRecord<String, String> record : records) {
        processOrder(record.value());
        consumer.commitSync(Collections.singletonMap(
            new TopicPartition(record.topic(), record.partition()),
            new OffsetAndMetadata(record.offset() + 1)
        ));
    }
}
4. Kafka Streams & KSQL
Q6. Build a real-time word count app
java
Copy
StreamsBuilder builder = new StreamsBuilder();
KStream<String, String> textLines = builder.stream("text-topic");

textLines.flatMapValues(line -> Arrays.asList(line.toLowerCase().split("\\W+")))
         .groupBy((key, word) -> word)
         .count(Materialized.as("word-count-store"))
         .toStream()
         .to("word-count-output", Produced.with(Serdes.String(), Serdes.Long()));

KafkaStreams streams = new KafkaStreams(builder.build(), config);
streams.start();
5. Integration with Java/Spring
Q7. Configure Spring Boot KafkaTemplate for Avro
yaml
Copy
spring:
  kafka:
    producer:
      key-serializer: org.apache.kafka.common.serialization.StringSerializer
      value-serializer: io.confluent.kafka.serializers.KafkaAvroSerializer
      properties:
        schema.registry.url: http://localhost:8081
Q8. Batch processing with @KafkaListener
java
Copy
@KafkaListener(topics = "orders", containerFactory = "batchFactory")
public void processBatch(List<ConsumerRecord<String, Order>> batch) {
    batch.forEach(record -> {
        try {
            orderService.process(record.value());
        } catch (Exception e) {
            deadLetterService.sendToDLT(record);
        }
    });
}
6. Performance & Tuning
Q9. Optimize Kafka for low latency
java
Copy
props.put(ProducerConfig.LINGER_MS_CONFIG, 20);  // Reduce batching delay
props.put(ProducerConfig.COMPRESSION_TYPE_CONFIG, "snappy");  // Compress messages
props.put(ProducerConfig.BATCH_SIZE_CONFIG, 16384);  // 16KB batches
10. Advanced Scenarios
Q10. Design an event-driven Order Management System
Components:

Order Service: Publishes OrderCreated events.

Inventory Service: Consumes events, checks stock.

Payment Service: Processes payments via PaymentProcessed events.

Kafka Streams: Validates order totals in real time.

11. Troubleshooting
Q11. Debug a consumer stuck in rebalance loop
Steps:

Check max.poll.interval.ms (default: 5 minutes).

Profile consumer processing time with JFR.

Verify network connectivity to Kafka brokers.

Analyze broker logs for partition assignment errors.

Key Areas for Senior Roles

Trade-offs: Delivery guarantees vs throughput.

Observability: Trace events with OpenTelemetry headers.
