# Kafka
[Kafka](http://kafka.apache.org/) is a very popular message broker.

## Message processing semantics
### At-least once
Due to retries in a producer or exceptions in a consumer the same message can be processed multiple times.

### At-most once (fire-and-forget)
If a producer does not retry on failure the message might by received by a consumer once or never, depending on where the failure occured.

### Exactly-once for read-process-write cycle (aka stream processing)
A single message is produced and consumed exactly once. How is this achieved?
#### Idempotency
An idempotent logic can be executed multiple times but the result is the same as it would have been called only once. In Kafka this means the same message is produced only once. Even if a producer retries a publish the broker makes sure the same message is added only once.
To enable idempotency per partition set “enable.idempotence=true” on your producer. This will add a unique sequence number to each message. How is this sequence number generated? TODO
#### Transactions
Transactions support atomic writes to multiple partitions. A message should only be marked as consumed when it was processed successfully. Since consumer offsets are also written to a Kafka topic they are contained in the same transaction.
#### Isolation level
A message should only be consumed when a transaction completed successfully.
Configure the consumer to use isolation.level=read_committed.
#### Zombie fencing
A zombie is a process that was considered down and thus was replaced with a new process. Now two instances are running in parallel which might cause problems. To avoid duplicate updates each producer must have a unique transactional.id that survives a restart of the same instance. When a producer registers with a broker an epoch number is incremented. If a producer with an older epoch tries to commit it gets rejected. There is no fencing between different transactional.id. Thus, the key to fencing out zombies properly is to ensure that the input topics and partitions in the read-process-write cycle are always the same for a given transactional.id.
How can this be achieved? TODO

See [Transactions in Apache Kafka](https://www.confluent.io/blog/transactions-apache-kafka/)


## Message deletion
### Thombstones
A thombstone is a keyed message with a null value. All messages with the same key are removed during compaction.

If a message is deleted by the retention logic of Kafka a consumer is not called.
If you want to delete the corresponding data in a state model (in-memory or database) then you have to implement a separat cleanup job.


# Resilient producer
TODO


# Similar products
- [Apache Pulsar](https://pulsar.apache.org/)
