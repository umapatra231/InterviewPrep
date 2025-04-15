Here’s a structured list of Kafka interview questions tailored for a Java developer with 8+ years of experience, covering core concepts, advanced scenarios, and real-world integration with Java ecosystems:

1. Core Kafka Concepts
Explain Kafka’s architecture (Brokers, Topics, Partitions, Producers, Consumers).

Key Points: Scalability, fault tolerance, and the role of ZooKeeper (or KRaft in newer versions).

What is a Consumer Group? How does rebalancing work?

Tests: Understanding of parallelism, partition assignment, and group.id.

Compare Kafka with traditional messaging systems (e.g., RabbitMQ).

Focus: Persistence, throughput, and event streaming vs. message queuing.

2. Kafka Producers (Java Client)
How do you ensure “exactly-once” semantics in a Java producer?

Answer: Enable enable.idempotence=true and use transactions (initTransactions(), commitTransaction()).

What is a Partitioner? Write a custom partitioner to route messages based on a header.

Example:

java
Copy
public class CustomPartitioner implements Partitioner {  
    public int partition(String topic, Object key, byte[] keyBytes, Object value, byte[] valueBytes, Cluster cluster) {  
        // Logic to compute partition from headers  
    }  
}  
How do acks=all, retries, and max.in.flight.requests.per.connection affect reliability?

3. Kafka Consumers (Java Client)
How do you handle consumer lag in a high-throughput Java application?

Strategies: Tuning fetch.min.bytes, max.poll.records, or scaling consumers.

Implement a consumer with manual offset commits. When would you use commitSync() vs commitAsync()?

Code Snippet:

java
Copy
consumer.poll(Duration.ofMillis(100)).forEach(record -> {  
    process(record);  
    consumer.commitSync();  
});  
What causes a ConsumerRebalanceListener to trigger? How do you handle offsets during rebalancing?

4. Kafka Streams & KSQL
Build a real-time word count application using Kafka Streams.

Example:

java
Copy
KStream<String, String> textLines = builder.stream("input-topic");  
textLines.flatMapValues(line -> Arrays.asList(line.split("\\W+")))  
         .groupBy((key, word) -> word)  
         .count()  
         .toStream()  
         .to("output-topic");  
Compare KStream and KTable. When would you use a GlobalKTable?

How do you handle out-of-order events in Kafka Streams?

5. Integration with Java/Spring
Configure a KafkaTemplate in Spring Boot to produce Avro messages.

Key Steps: Use SpecificAvroSerializer, configure schema.registry.url.

Implement a @KafkaListener with batch processing and error handling.

Example:

java
Copy
@KafkaListener(topics = "orders")  
public void processBatch(List<ConsumerRecord<String, Order>> batch) {  
    batch.forEach(record -> {  
        try { process(record); }  
        catch (Exception e) { handleError(record, e); }  
    });  
}  
How do you integrate Kafka with Spring Cloud Stream? What are binders and bindings?

6. Performance & Tuning
Optimize Kafka for low latency in a Java application.

Tactics: Tune linger.ms, batch.size, compression (snappy, zstd).

Diagnose high CPU usage in a Kafka consumer. What JVM settings would you adjust?

Checkpoints: Garbage collection (G1GC), -XX:MaxGCPauseMillis, thread stacks.

How do you size Kafka partitions for a Java-based microservice?

Rule of Thumb: # Partitions = max(consumers in group) × throughput per consumer.

7. Fault Tolerance & Reliability
Handle duplicate messages in a Java consumer.

Solutions: Idempotent processing, deduplication tables, or transactional IDs.

What is a Dead Letter Topic (DLT)? Configure one with Spring Kafka.

Implementation: Use DeadLetterPublishingRecoverer and SeekToCurrentErrorHandler.

How does Kafka ensure durability? Discuss replication (ISR), min.insync.replicas, and unclean.leader.election.

8. Security
Secure Kafka with SSL and SASL/SCRAM in a Java client.

Config:

properties
Copy
security.protocol=SASL_SSL  
sasl.mechanism=SCRAM-SHA-512  
ssl.truststore.location=/path/to/truststore.jks  
Implement ACLs to restrict access to Kafka topics.

Example: kafka-acls --add --topic orders --producer --allow-principal User:service-account.

9. Monitoring & Observability
Expose Kafka metrics (e.g., lag) to Prometheus using Micrometer.

Steps: Add micrometer-registry-prometheus, enable JMX or Kafka metrics reporter.

Use the AdminClient API to programmatically check topic health.

Code Snippet:

java
Copy
try (AdminClient admin = AdminClient.create(props)) {  
    DescribeTopicsResult result = admin.describeTopics(Collections.singleton("orders"));  
    TopicDescription desc = result.all().get().get("orders");  
}  
10. Advanced Scenarios
Design an event-driven Order Management System using Kafka and Java.

Components: Order service (producer), Inventory service (consumer), Kafka Streams for validation.

Migrate a legacy Java monolith to Kafka-based microservices without downtime.

Approach: Dual-write to Kafka, use CDC (Debezium) for database changes.

How do you back up and restore Kafka data in a multi-datacenter setup?

Tools: MirrorMaker2, Confluent Replicator, or custom consumers/producers.

11. Troubleshooting
Debug a Java consumer stuck in a rebalance loop.

Causes: Long polling (max.poll.interval.ms), slow processing, or network issues.

Why are messages not appearing in a topic?

Diagnosis: Check producer acks, retries, and broker logs for errors.

Key Areas for Senior Roles
Trade-offs: At-least-once vs exactly-once semantics, partition count vs scalability.

Cost Optimization: Tiered storage, log retention policies.

Disaster Recovery: Multi-region replication, broker failure handling.

Observability: End-to-end tracing (e.g., OpenTelemetry with Kafka headers).
