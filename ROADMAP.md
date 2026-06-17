# Event Driven Async Platform Roadmap

## 1. Fundamentals of event-driven and asynchronous systems

### 001. Research synchronous and asynchronous communication

Compare request-response interaction with asynchronous message transmission in terms of coupling, latency, availability, fault tolerance, and operational complexity.

### 002. Research event-driven architecture

Examine producers, consumers, brokers, event channels, subscriptions, and the impact of the event-driven approach on backend service boundaries.

### 003. Separate command, event, and job

Define the semantic differences between a command, a fact of an event that has occurred, and a background task, establishing rules for their use in the platform.

### 004. Research message delivery guarantees

Examine at-most-once, at-least-once, and effectively-once delivery, as well as the limitations of the practical implementation of exactly-once semantics.

### 005. Research message ordering

Examine global ordering, partition ordering, aggregate ordering, and the causes of order violations during parallel processing.

### 006. Research eventual consistency

Define the consistency model between services, possible temporary data discrepancies, and methods for detecting and resolving them.

### 007. Research the CAP theorem

Consider the impact of a network partition on availability, consistency, and the behavior of distributed asynchronous workflows.

### 008. Research PACELC

Examine the trade-off between latency and consistency during normal system operation and during network partitions.

### 009. Research backpressure

Define methods for limiting producers, consumers, and queues when the platform throughput capacity is exceeded.

### 010. Create a failure taxonomy

Classify transient, permanent, infrastructure, business, serialization, and poison-message failures.

### 011. Research retry semantics

Examine immediate retry, delayed retry, exponential backoff, jitter, retry budget, and limitations on the number of retries.

### 012. Research idempotency

Define requirements for the idempotent processing of commands, events, HTTP requests, and external side effects.

### 013. Research deduplication

Compare producer-side, broker-side, and consumer-side deduplication, including time windows and key storage.

### 014. Research dead letter queues

Examine the purpose of a DLQ, the reasons messages are placed in it, and the rules for storage, analysis, and reprocessing.

### 015. Research poison messages

Define the characteristics of messages that repeatedly break a consumer and methods for isolating them without stopping the main flow.

### 016. Research distributed transactions

Compare 2PC, transactional outbox, saga, and compensation-based approaches.

### 017. Research the saga pattern

Examine choreography and orchestration, local transactions, compensating operations, and saga state.

### 018. Research stream processing and message queuing

Compare the Kafka event log with RabbitMQ queues and Redis-based job queues.

### 019. Research push and pull consumption

Examine message retrieval models, consumer rate control, and the impact on backpressure.

### 020. Establish the platform's baseline guarantees

Document the selected delivery, ordering, consistency, retry, and durability guarantees for each transport.

## 2. Repository structure and basic infrastructure

### 021. Create a monorepo

Configure a workspace for multiple NestJS applications, shared libraries, contracts, infrastructure adapters, and testing utilities.

### 022. Define the application structure

Create separate applications for the Order, Inventory, Payment, Notification, Workflow, and Event Gateway services.

### 023. Define shared libraries

Extract libraries for event contracts, message envelope, observability, broker clients, idempotency, and testing.

### 024. Configure TypeScript

Enable strict mode, project references, path aliases, and separate configurations for applications and libraries.

### 025. Configure linting and formatting

Add ESLint, Prettier, and rules to prevent unsafe async code and unhandled Promises.

### 026. Configure environment validation

Implement validation of RabbitMQ, Kafka, Redis, PostgreSQL, and observability configuration through Zod or Joi.

### 027. Create a Docker Compose environment

Add PostgreSQL, RabbitMQ, Kafka, Redis, and the required administration interfaces.

### 028. Add container health checks

Configure infrastructure service readiness checks and startup order dependencies.

### 029. Configure local network aliases

Create stable service DNS names for identical local and containerized launch configuration.

### 030. Add development scripts

Implement commands for launching the entire platform, individual services, migrations, seeds, and an infrastructure-only environment.

### 031. Configure PostgreSQL migrations

Create independent schemas or databases for services and prohibit direct access to other services' tables.

### 032. Add seed data

Prepare reproducible data for order, inventory, payment, and workflow scenarios.

### 033. Implement graceful shutdown

Correctly stop HTTP servers, consumers, producers, workers, and database connections.

### 034. Implement startup sequencing

Do not start consumers until the database, broker connections, and required migrations are ready.

### 035. Add readiness probes

Check whether a service can accept traffic and process messages.

### 036. Add liveness probes

Detect hung processes without false positives during temporary broker unavailability.

### 037. Configure centralized transport configuration

Create typed settings for exchanges, queues, topics, consumer groups, and Redis queues.

### 038. Create an abstraction for the clock

Use ClockPort for deterministic testing of timeouts, retries, and scheduled jobs.

### 039. Create an abstraction for ID generation

Implement UUID or ULID generation through a separate port for testability and sortable identifiers.

### 040. Create an abstraction for transactions

Define TransactionPort, allowing the application layer to execute local transactions without depending on the ORM.

## 3. Domain model and service boundaries

### 041. Define the demonstration business flow

Use the Order → Inventory Reservation → Payment → Notification scenario as the primary distributed workflow.

### 042. Define bounded contexts

Separate Order, Inventory, Payment, Notification, and Workflow by responsibility and data ownership.

### 043. Define aggregate boundaries

Extract Order, InventoryItem, Payment, and SagaInstance as independent consistency boundaries.

### 044. Design the Order state machine

Define the CREATED, INVENTORY_PENDING, PAYMENT_PENDING, COMPLETED, CANCELLED, and FAILED states.

### 045. Design the Payment state machine

Define the CREATED, PROCESSING, SUCCEEDED, DECLINED, REFUND_PENDING, and REFUNDED states.

### 046. Design the Inventory reservation model

Implement reservation, confirmation, and release of stock without directly changing Order Service data.

### 047. Design the Notification model

Separate notification request, delivery attempt, and delivery result.

### 048. Define data ownership

Document which service is the source of truth for each data type.

### 049. Prohibit shared database integration

Ensure service interaction only through APIs, commands, and events.

### 050. Define public HTTP APIs

Create minimal synchronous endpoints for order creation, state viewing, and administrative operations.

### 051. Define asynchronous commands

Create a list of the ReserveInventory, ProcessPayment, ReleaseInventory, RefundPayment, and SendNotification commands.

### 052. Define domain events

Create a list of the OrderCreated, InventoryReserved, InventoryRejected, PaymentSucceeded, PaymentFailed, and OrderCompleted events.

### 053. Define integration events

Separate internal domain events from events published outside the service boundary.

### 054. Implement application handlers

Create separate handlers for HTTP commands, broker commands, and events.

### 055. Implement infrastructure ports

Define MessagePublisherPort, MessageConsumerPort, JobQueuePort, IdempotencyStorePort, and EventStorePort.

### 056. Implement transport adapters

Create separate adapters for RabbitMQ, Kafka, and Redis queues.

### 057. Implement repository ports

Isolate the domain and application layers from PostgreSQL and the selected ORM.

### 058. Introduce dependency rules

Prohibit dependencies of the domain/application layers on NestJS transport clients, broker SDKs, and the ORM.

### 059. Add architecture tests

Automatically verify permitted import directions between layers and modules.

### 060. Document service boundaries

Establish the responsibility, incoming commands, outgoing events, and owned data of each service.

## 4. Message contracts and schema governance

### 061. Create a common message envelope

Add messageId, messageType, messageVersion, occurredAt, producer, payload, and metadata.

### 062. Add correlationId

Pass the identifier of the entire distributed workflow between HTTP requests, events, and jobs.

### 063. Add causationId

Store the identifier of the message that caused publication of the current message.

### 064. Add trace context

Pass W3C traceparent and tracestate through broker headers.

### 065. Add tenant context

Implement secure transmission of tenantId without trusting an arbitrary payload.

### 066. Add actor context

Pass the minimum required information about the initiator of the action without publishing sensitive data.

### 067. Implement runtime validation

Validate the message envelope and payload before passing the message to the application handler.

### 068. Select a serialization format

Compare JSON, Avro, and Protobuf and implement the baseline format for the platform.

### 069. Introduce event versioning

Use an explicit contract version and prohibit uncontrolled modification of an existing schema.

### 070. Implement backward compatibility rules

Define permitted changes to fields, defaults, optional fields, and enum values.

### 071. Implement upcasting

Convert old event versions into the current internal representation.

### 072. Implement a schema registry abstraction

Create an interface for registering, retrieving, and checking compatibility of message schemas.

### 073. Add schema compatibility tests

Check backward and forward compatibility with every contract change.

### 074. Create an event catalog

Document producers, consumers, payload, version, ordering key, and retention for each event.

### 075. Add payload size limits

Reject excessively large messages and use a reference-based payload for large data.

## 5. RabbitMQ

### 076. Connect a RabbitMQ client

Implement managed connection, channels, reconnect, and shutdown for NestJS applications.

### 077. Research the AMQP model

Practically verify exchanges, queues, bindings, routing keys, acknowledgements, and publisher confirms.

### 078. Implement a direct exchange

Use direct routing for targeted business commands.

### 079. Implement a topic exchange

Use topic routing for domain and integration events.

### 080. Implement a fanout exchange

Demonstrate a broadcast event for independent subscribers.

### 081. Research a headers exchange

Implement a separate example of routing by message headers and evaluate its limitations.

### 082. Configure durable exchanges and queues

Ensure topology and messages are preserved after a broker restart.

### 083. Configure persistent messages

Verify the impact of deliveryMode and publisher confirms on durability.

### 084. Implement publisher confirms

Consider publication successful only after broker confirmation.

### 085. Handle publisher nacks

Implement republishing or writing to local recovery storage upon negative acknowledgement.

### 086. Implement mandatory publishing

Detect messages for which no suitable binding or queue exists.

### 087. Handle returned messages

Log and store unroutable messages for analysis and rerouting.

### 088. Implement manual acknowledgement

Acknowledge a message only after successful application and persistence processing.

### 089. Implement negative acknowledgement

Separate requeue, reject, and dead-letter behavior for different error types.

### 090. Configure consumer prefetch

Limit the number of unacknowledged messages and verify the impact on throughput and fairness.

### 091. Implement competing consumers

Launch several instances of one consumer and verify load distribution.

### 092. Verify ordering with competing consumers

Establish the conditions under which RabbitMQ stops providing the expected order.

### 093. Implement routing by aggregateId

Create a strategy for directing related messages to one sequential consumer.

### 094. Configure message TTL

Delete or dead-letter expired commands and jobs.

### 095. Configure queue TTL

Automatically delete temporary queues after a period of inactivity.

### 096. Configure max-length policies

Limit queue size by message count and data volume.

### 097. Implement a dead-letter exchange

Create separate DLX and DLQ instances for each business queue.

### 098. Implement delayed retries through TTL queues

Create retry queues with multiple delay levels without infinite immediate requeue.

### 099. Research the delayed message exchange

Compare the RabbitMQ Delayed Message Plugin with TTL-based retry topology.

### 100. Implement a priority queue

Add a demonstration scenario for processing urgent commands and verify the risk of starvation.

### 101. Research quorum queues

Configure replicated quorum queues and verify behavior when a broker node fails.

### 102. Compare classic and quorum queues

Establish differences in latency, throughput, replication, and operational semantics.

### 103. Configure RabbitMQ policies

Manage TTL, max-length, DLX, and queue type through policies, not only application code.

### 104. Implement topology assertion

Automatically create and verify exchanges, queues, and bindings when the application starts.

### 105. Handle topology mismatch

Correctly terminate startup when existing queue or exchange parameters are incompatible.

### 106. Implement connection recovery

Restore connections and consumers after temporary RabbitMQ unavailability.

### 107. Handle channel failure

Recreate the channel and consumer registration without restarting the entire service.

### 108. Handle consumer cancellation

Register broker-initiated cancellation and restore consumption.

### 109. Research RabbitMQ flow control

Verify producer behavior during a memory alarm, disk alarm, and publisher blocking.

### 110. Configure the RabbitMQ Management API

Retrieve queue depth, consumer count, publish rate, delivery rate, and unacked messages.

## 6. Kafka

### 111. Deploy Kafka in KRaft mode

Configure a local cluster without ZooKeeper and document broker/controller roles.

### 112. Connect a Kafka client

Implement a producer, consumer, admin client, lifecycle hooks, and reconnect handling.

### 113. Create a topic naming convention

Define naming rules for domain events, integration events, retry topics, and dead-letter topics.

### 114. Research partitions

Verify the impact of partition count on parallelism, ordering, and scalability.

### 115. Implement a partition key strategy

Use aggregateId or sagaId to preserve the order of related events.

### 116. Configure the replication factor

Define replication parameters for development and production environments.

### 117. Research ISR

Verify the impact of in-sync replicas on write availability and durability.

### 118. Configure producer acknowledgements

Compare acks=0, acks=1, and acks=all in terms of latency and data-loss risk.

### 119. Configure min.insync.replicas

Prevent write acknowledgement when there are not enough up-to-date replicas.

### 120. Implement an idempotent producer

Enable producer idempotence and verify elimination of duplicate records during retries.

### 121. Configure producer retries

Define retry count, delivery timeout, request timeout, and backoff.

### 122. Research producer batching

Configure batch.size and linger.ms and measure the trade-off between throughput and latency.

### 123. Research compression

Compare gzip, snappy, lz4, and zstd under a demonstration load.

### 124. Implement a consumer group

Launch multiple consumers in one group and verify partition distribution.

### 125. Implement independent consumer groups

Connect several services to one topic without mutual impact on offsets.

### 126. Research offset management

Compare automatic and manual offset commits.

### 127. Implement commit after processing

Commit the offset only after a successful business transaction.

### 128. Research commit failure windows

Define duplicate processing and message-loss scenarios between processing and offset commit.

### 129. Implement batch consumption

Process a set of records with control over partial failure and offset progression.

### 130. Configure consumer concurrency

Align the number of consumer instances and handlers with the number of partitions.

### 131. Research rebalancing

Verify stop-the-world rebalance when consumers connect and disconnect.

### 132. Configure cooperative rebalancing

Reduce the volume of redistributed partitions when the consumer group changes.

### 133. Implement rebalance listeners

Correctly finish record processing before partition revocation.

### 134. Configure heartbeat and session timeout

Prevent false rebalances during long message processing.

### 135. Implement pause and resume

Temporarily stop consumption when the database or a downstream dependency is overloaded.

### 136. Configure the retention policy

Define the event retention period by time and volume.

### 137. Implement log compaction

Create a compacted topic for the latest state of an entity or configuration.

### 138. Verify tombstone records

Implement key deletion from a compacted topic through a null payload.

### 139. Implement retry topics

Create separate topics for multiple levels of delayed retry.

### 140. Implement a Kafka dead-letter topic

Move permanently unprocessable records together with failure metadata.

### 141. Implement a retry topic consumer

Republish messages to the main topic after nextAttemptAt is reached.

### 142. Research Kafka transactions

Implement atomic publication of multiple records within a producer transaction.

### 143. Research consume-transform-produce

Implement transactional processing that links consumed offsets and produced records.

### 144. Verify read_committed isolation

Exclude reading aborted transactional records.

### 145. Research Kafka exactly-once semantics

Document the scope of Kafka EOS and the limitations involving external side effects and PostgreSQL.

### 146. Implement event replay

Reprocess a topic with a new consumer group or a reset offset.

### 147. Implement selective replay

Reprocess records by offset ranges, timestamps, or business keys.

### 148. Verify partition expansion

Add partitions and research changes in key distribution and ordering guarantees.

### 149. Implement Kafka Admin tooling

Create topics and verify configuration, offsets, lag, and consumer groups through the CLI.

### 150. Configure consumer lag monitoring

Calculate lag by partition and aggregate it by consumer group.

## 7. Redis queues and background jobs

### 151. Connect BullMQ

Configure Queue, Worker, QueueEvents, and Redis connections.

### 152. Create background job contracts

Separate job name, payload schema, options, and result schema from the specific Worker.

### 153. Implement a basic Worker

Process jobs with controlled concurrency and graceful shutdown.

### 154. Research job states

Verify transitions between waiting, active, completed, failed, delayed, and stalled.

### 155. Implement delayed jobs

Schedule task execution after a specified interval.

### 156. Implement scheduled jobs

Create recurring jobs for reconciliation, cleanup, and reporting.

### 157. Research repeatable job uniqueness

Prevent accidental creation of multiple identical schedules.

### 158. Implement job priority

Process urgent and regular tasks with a documented risk of starvation.

### 159. Configure worker concurrency

Measure the impact of concurrency on CPU-bound, I/O-bound, and rate-limited jobs.

### 160. Implement queue rate limiting

Limit the frequency of calls to the external Notification Provider.

### 161. Implement per-tenant rate limiting

Prevent one tenant from occupying the entire queue throughput capacity.

### 162. Implement attempts and backoff

Configure fixed and exponential retries for background jobs.

### 163. Implement custom backoff

Account for error type, Retry-After, and business constraints.

### 164. Implement jobId deduplication

Use a deterministic jobId to prevent repeated addition of the same task.

### 165. Research BullMQ deduplication modes

Compare simple, throttle, and debounce semantics.

### 166. Handle stalled jobs

Detect jobs whose Worker terminated without recording a result.

### 167. Configure lock duration

Align lock renewal with the maximum job execution time.

### 168. Implement job timeout

Interrupt or mark hung jobs, taking irreversible side effects into account.

### 169. Implement job cancellation

Add cooperative cancellation through a cancellation flag and AbortSignal.

### 170. Implement job progress

Store the progress of long-running operations and provide an API for reading it.

### 171. Implement job result storage

Define the result retention period and result payload size limits.

### 172. Implement parent-child flows

Use BullMQ FlowProducer for multi-step dependent background jobs.

### 173. Handle a child job failure

Define parent job behavior when the workflow partially fails.

### 174. Implement sandboxed processors

Move a CPU-intensive job to a separate process or Worker Thread.

### 175. Configure job cleanup

Delete completed and failed jobs according to count or age policies.

### 176. Configure Redis persistence

Compare RDB, AOF, and combined mode for job durability.

### 177. Research Redis eviction policies

Prohibit dangerous eviction of queue metadata when the memory limit is reached.

### 178. Configure Redis Sentinel

Verify recovery of workers and producers after primary node failover.

### 179. Research Redis Cluster

Verify queue key compatibility, hash slots, and limitations of multi-key operations.

### 180. Compare BullMQ, RabbitMQ, and Kafka

Create a decision matrix for scheduling, routing, replay, ordering, durability, and operational complexity.

## 8. Transactional Outbox and Inbox

### 181. Design the outbox table

Add id, aggregateId, messageType, version, payload, headers, status, attempts, and timestamps.

### 182. Implement an atomic business transaction

Store domain state and the outbox record in one PostgreSQL transaction.

### 183. Implement the outbox repository

Add operations for insertion, selection, locking, acknowledgement, and recording publication errors.

### 184. Implement a polling publisher

Periodically retrieve unprocessed outbox records and publish them to the broker.

### 185. Use FOR UPDATE SKIP LOCKED

Allow several outbox workers to safely process different records in parallel.

### 186. Implement outbox batch publishing

Publish records in batches with limits on batch size and transaction holding time.

### 187. Separate publication and the database transaction

Do not keep a PostgreSQL transaction open during a long network call without a justified strategy.

### 188. Implement an outbox claim model

Introduce claimedAt and claimedBy to recover stuck records.

### 189. Implement publisher confirms for the outbox

Mark a RabbitMQ record as published only after broker confirmation.

### 190. Implement Kafka acknowledgement for the outbox

Mark a Kafka record as published only after successful producer acknowledgement.

### 191. Handle an ambiguous publish result

Consider a duplicate message possible if the producer did not receive confirmation but the broker may have stored the record.

### 192. Implement outbox retry

Retry transient publication failures with backoff and a maximum number of attempts.

### 193. Implement an outbox dead state

Move irreparable records to a separate state for manual analysis.

### 194. Implement outbox cleanup

Archive or delete successfully published records according to the retention policy.

### 195. Partition the outbox table

Partition a large table by time and verify the impact on cleanup and indexes.

### 196. Add outbox indexes

Optimize retrieval of pending records and lookup by aggregateId and messageId.

### 197. Research a CDC-based outbox

Compare the polling publisher with Debezium and PostgreSQL logical replication.

### 198. Implement the inbox table

Store messageId, consumer, receivedAt, processedAt, and processing result.

### 199. Implement atomic inbox processing

In one transaction, record the inbox entry and changes to business state.

### 200. Implement duplicate detection

Skip a message if the corresponding messageId and consumer pair has already been processed.

### 201. Implement inbox retention

Delete deduplication records only after a safe time window.

### 202. Research the deduplication window

Define the inbox record retention period based on broker retention and replay policy.

### 203. Implement inbox failure recovery

Distinguish messages whose processing has not started, is in progress, or ended with an error.

### 204. Add outbox and inbox metrics

Collect pending count, publication latency, retry count, duplicate count, and processing latency.

### 205. Test outbox/inbox crash windows

Verify process failure before commit, after commit, before publish acknowledgement, and after consumer processing.

## 9. Idempotency, retries, and fault tolerance

### 206. Implement HTTP idempotency keys

Store the result of repeatable create commands and return it for a repeated request with the same key.

### 207. Verify the idempotency payload fingerprint

Reject reuse of a key with a different request payload.

### 208. Implement command idempotency

Guarantee that repeated delivery of a command does not create repeated business side effects.

### 209. Implement event handler idempotency

Protect projection updates and downstream publications from duplicate events.

### 210. Implement external API idempotency

Pass an idempotency key to the Payment Provider and Notification Provider if supported.

### 211. Implement a side-effect ledger

Store the fact that irreversible external operations were performed.

### 212. Separate retryable and non-retryable errors

Create a typed classification of infrastructure, timeout, validation, and business failures.

### 213. Implement exponential backoff

Increase retry delay without overloading an unavailable dependency.

### 214. Add jitter

Prevent a synchronized retry storm when an external service recovers.

### 215. Implement a retry budget

Limit the total number of retries and the operation lifetime.

### 216. Implement a timeout policy

Set separate timeouts for broker operations, database queries, and external HTTP calls.

### 217. Pass the deadline

Stop downstream operations if the overall workflow deadline has already expired.

### 218. Implement a circuit breaker

Stop calls to a temporarily failing dependency and perform controlled recovery probes.

### 219. Implement bulkhead isolation

Limit concurrency and resources for independent downstream systems.

### 220. Implement producer rate limiting

Prevent a producer from generating load faster than the permitted processing rate.

### 221. Implement consumer load shedding

Defer or reject low-priority work under critical load.

### 222. Implement retry storm protection

Limit the simultaneous return of a large volume of messages from retry queues.

### 223. Implement DLQ metadata

Store original destination, exception type, stack trace, attempts, and failedAt.

### 224. Implement a DLQ inspection API

Provide filtering of dead-letter messages by service, type, cause, and time.

### 225. Implement DLQ redrive

Resend selected messages to the original queue or topic.

### 226. Validate the schema before redrive

Do not return messages with an incompatible or corrupted schema to the main flow.

### 227. Implement selective redrive

Reprocess only messages matching a specified failure reason or business key.

### 228. Implement quarantine storage

Isolate messages requiring manual correction of the payload or business data.

### 229. Add redrive audit

Record who resent the message, when, and why.

### 230. Implement graceful degradation

Continue the main business operation during temporary unavailability of optional consumers, such as Notification Service.

## 10. Saga and asynchronous workflows

### 231. Select a saga scenario

Use order creation with inventory reservation, payment charging, and notification sending.

### 232. Implement a choreography saga

Connect services through a sequence of events without a central orchestrator.

### 233. Document choreography dependencies

Establish hidden relationships between producers and consumers and the risk of cyclic event chains.

### 234. Implement an orchestration saga

Create a Workflow Service that sends commands and processes step results.

### 235. Create a saga state machine

Define states, transitions, terminal states, and permitted events.

### 236. Create a saga persistence model

Store sagaId, state, version, step data, deadlines, and timestamps.

### 237. Implement optimistic locking for the saga

Prevent concurrent processing of multiple events belonging to one saga.

### 238. Implement saga command dispatch

Publish the next command only after the saga state has been successfully changed.

### 239. Use an outbox in the orchestrator

Atomically store the new saga state and the command for the next step.

### 240. Implement an Inventory compensating transaction

Release the reservation when payment fails or the order is cancelled.

### 241. Implement a Payment compensating transaction

Refund funds if the workflow cannot complete after successful payment.

### 242. Make compensation idempotent

Safely execute repeated ReleaseInventory and RefundPayment operations.

### 243. Implement a saga timeout

Move a stuck saga into compensation or manual-review state.

### 244. Implement a scheduled timeout checker

Use a Redis queue or a separate scheduler to find expired sagas.

### 245. Handle late events

Ignore, compensate for, or separately record events received after timeout.

### 246. Handle out-of-order events

Validate the transition and temporarily defer events received before the expected step.

### 247. Implement a saga retry policy

Configure separate retry rules for each workflow step.

### 248. Implement saga cancellation

Allow the user to cancel the workflow before reaching an irreversible step.

### 249. Implement manual intervention

Add a state in which an operator must decide whether to continue or compensate.

### 250. Implement a saga audit trail

Store the complete history of transitions, commands, events, retries, and compensations.

### 251. Implement saga versioning

Support changes to the workflow definition for already-running saga instances.

### 252. Implement saga state migration

Convert persisted state from an older orchestration version.

### 253. Implement parallel saga steps

Execute independent actions simultaneously and wait for their combined result.

### 254. Implement join semantics

Define behavior for complete success, partial success, and failure of parallel steps.

### 255. Implement a long-running workflow

Create a workflow that can run for hours or days and recover after a restart.

### 256. Implement a human-in-the-loop step

Pause the workflow until external confirmation from a user or operator.

### 257. Implement a workflow query API

Return the current state, completed steps, pending actions, and failure reason.

### 258. Implement workflow resume

Continue execution after data is corrected or a dependency is restored.

### 259. Compare a custom orchestrator with Temporal

Research Temporal durable execution, retries, timers, state recovery, and operational complexity.

### 260. Compare a custom orchestrator with Camunda

Research BPMN, external tasks, human workflows, and the boundaries of process engine use.

## 11. Read models, projections, and reconciliation

### 261. Create an asynchronous read model

Build an aggregated order state from events of several services.

### 262. Implement a projection consumer

Update the PostgreSQL read model through idempotent event processing.

### 263. Store the projection offset

Record the consumer position for recovery and consistency control.

### 264. Implement projection rebuild

Fully rebuild the read model from Kafka event history.

### 265. Implement blue-green projection rebuild

Build a new projection version in parallel and switch reads after completion.

### 266. Add a projection version

Support several read-model implementations for one event stream.

### 267. Measure consistency lag

Calculate the delay between event occurredAt and projection update.

### 268. Implement a stale-data indicator

Show the client the age or freshness of the eventual-consistent read model.

### 269. Implement a reconciliation job

Periodically compare Order, Inventory, Payment, and Saga state.

### 270. Create consistency invariants

Formalize conditions such as “a COMPLETED order must have confirmed inventory and succeeded payment.”

### 271. Implement inconsistency detection

Find invariant violations and store them in a separate table.

### 272. Implement automated repair

Safely correct known discrepancy types through idempotent commands.

### 273. Implement a manual repair workflow

Create an operator flow for ambiguous consistency conflicts.

### 274. Add an anti-entropy process

Periodically synchronize state between source services and derived read models.

### 275. Document consistency windows

Define the permitted inconsistency period for each user scenario.

## 12. Testing

### 276. Create unit tests for domain state machines

Verify permitted and prohibited Order, Payment, and Saga transitions.

### 277. Create unit tests for message validation

Verify envelope, payload schemas, versions, and required metadata.

### 278. Create unit tests for routing logic

Verify RabbitMQ routing keys, Kafka partition keys, and queue names.

### 279. Create unit tests for retry policies

Verify backoff, jitter, retry classification, and retry budget.

### 280. Create unit tests for idempotency

Verify repeated processing of the same command, event, and job.

### 281. Create unit tests for the outbox publisher

Verify claim, publish, confirm, retry, and dead-state transitions.

### 282. Create unit tests for the saga orchestrator

Verify success, failure, timeout, compensation, and cancellation paths.

### 283. Create contract tests

Verify producer and consumer compatibility without launching the complete system.

### 284. Create consumer-driven contract tests

Allow consumers to define the expected shape of events and commands.

### 285. Configure Testcontainers PostgreSQL

Run integration tests against a real PostgreSQL version.

### 286. Configure Testcontainers RabbitMQ

Verify exchanges, queues, acknowledgements, retries, and DLQ on a real broker.

### 287. Configure Testcontainers Kafka

Verify partitions, offsets, consumer groups, and replay on a real broker.

### 288. Configure Testcontainers Redis

Verify BullMQ jobs, retries, delays, and stalled recovery.

### 289. Create RabbitMQ integration tests

Verify the publish-confirm-consume-ack workflow.

### 290. Create Kafka integration tests

Verify the producer-consumer-offset workflow and consumer group rebalancing.

### 291. Create BullMQ integration tests

Verify delayed jobs, attempts, custom backoff, and job deduplication.

### 292. Test duplicate delivery

Deliver one message several times and verify the absence of repeated side effects.

### 293. Test out-of-order delivery

Change the event order and verify state transition protection.

### 294. Test malformed messages

Send invalid JSON, an unknown version, and an invalid payload.

### 295. Test poison messages

Verify a limited number of retries and the subsequent move to the DLQ.

### 296. Test consumer crash before commit

Terminate the process after the business operation but before acknowledgement or offset commit.

### 297. Test consumer crash after commit

Verify that state is not corrupted when failure occurs immediately after processing is recorded.

### 298. Test producer crash

Terminate the outbox publisher at different stages of publication and confirmation.

### 299. Test broker restart

Restart RabbitMQ, Kafka, and Redis under active load.

### 300. Test PostgreSQL outage

Verify acknowledgement suspension, consumer pause, and recovery after the database returns.

### 301. Test network latency

Add delay and packet loss between services and brokers.

### 302. Test network partition

Isolate a producer or consumer from the broker and verify recovery behavior.

### 303. Test RabbitMQ node failure

Stop a quorum-cluster node and verify availability and data safety.

### 304. Test Kafka broker failure

Stop the partition leader and verify election, producer retries, and consumer recovery.

### 305. Test Redis failover

Switch the Sentinel primary during job processing.

### 306. Create an end-to-end happy path

Verify the complete order flow through all services, brokers, and the read model.

### 307. Create an end-to-end payment failure path

Verify Inventory compensation and the final order state.

### 308. Create an end-to-end timeout path

Verify saga timeout, retries, compensation, and audit trail.

### 309. Create an end-to-end duplicate path

Resend commands and events and verify the effectively-once result.

### 310. Create deterministic test fixtures

Use fixed clock, IDs, message timestamps, and broker payloads.

## 13. Performance and capacity planning

### 311. Define performance targets

Establish target throughput, p95/p99 latency, queue lag, and permitted workflow duration.

### 312. Create a RabbitMQ load test

Measure publish rate, consume rate, unacked count, and queue growth.

### 313. Create a Kafka load test

Measure throughput by partition, producer latency, consumer lag, and rebalance impact.

### 314. Create a BullMQ load test

Measure job throughput, Redis memory usage, and stalled-job rate.

### 315. Compare serialization formats

Measure payload size and CPU cost of JSON, Avro, and Protobuf.

### 316. Research message batching

Compare single and batch publication and processing.

### 317. Research payload size impact

Measure the impact of large messages on broker memory, network, and consumer latency.

### 318. Implement payload offloading

Store large data in object storage or a database and pass a reference.

### 319. Configure a consumer concurrency benchmark

Find the optimal number of consumers for each workload type.

### 320. Configure a prefetch benchmark

Measure RabbitMQ throughput and fairness with different prefetch values.

### 321. Configure a Kafka partition benchmark

Determine the required number of partitions for target parallelism.

### 322. Measure outbox publication latency

Determine the delay from business transaction commit to event publication.

### 323. Measure end-to-end event latency

Measure the time from the producer operation to completion by all required consumers.

### 324. Conduct a soak test

Run a prolonged load to detect memory leaks, connection leaks, and backlog growth.

### 325. Create a capacity model

Calculate the required number of brokers, partitions, queues, consumers, and database resources.

## 14. Observability

### 326. Implement structured logging

Use JSON logs with service, environment, messageId, correlationId, causationId, and traceId.

### 327. Add logging context propagation

Automatically transfer metadata between HTTP, RabbitMQ, Kafka, and BullMQ handlers.

### 328. Sanitize logs

Remove credentials, payment data, tokens, and sensitive payload fields.

### 329. Implement OpenTelemetry instrumentation

Configure traces, metrics, and context propagation for the entire platform.

### 330. Add HTTP tracing

Create server and client spans for synchronous API calls.

### 331. Add RabbitMQ tracing

Create producer and consumer spans with exchange, queue, and routing key attributes.

### 332. Add Kafka tracing

Create spans with topic, partition, offset, and consumer group attributes.

### 333. Add BullMQ tracing

Create spans for enqueue, waiting time, and job processing.

### 334. Link traces through message headers

Continue the trace between independent processes and delayed retries.

### 335. Add producer metrics

Collect published, failed, retried, confirmed, and unroutable message counters.

### 336. Add consumer metrics

Collect received, processed, failed, retried, and duplicate message counters.

### 337. Add processing histograms

Measure the duration of handlers, jobs, saga steps, and external API calls.

### 338. Add queue depth metrics

Collect ready, unacked, delayed, active, and failed counts.

### 339. Add Kafka lag metrics

Collect lag by topic, partition, and consumer group.

### 340. Add outbox metrics

Collect pending count, oldest pending age, publication latency, and dead records.

### 341. Add saga metrics

Collect active, completed, failed, compensating, and timed-out saga counts.

### 342. Add consistency metrics

Collect projection lag and the number of detected reconciliation conflicts.

### 343. Create a RabbitMQ Grafana dashboard

Show queue depth, publish rate, consume rate, unacked, and consumer count.

### 344. Create a Kafka Grafana dashboard

Show producer rate, consumer rate, partition lag, and rebalance events.

### 345. Create a BullMQ Grafana dashboard

Show waiting, active, delayed, failed, stalled, and completed jobs.

### 346. Create a workflows Grafana dashboard

Show saga duration, success rate, compensation rate, and timeout rate.

### 347. Create distributed trace examples

Document the trace of a complete order through HTTP, brokers, services, and background jobs.

### 348. Add correlation search

Provide search for all logs and traces of one distributed workflow by correlationId.

### 349. Implement alerting rules

Configure alerts for backlog growth, consumer lag, DLQ growth, outbox age, and saga timeouts.

### 350. Define SLI and SLO

Establish availability, processing latency, successful workflow rate, and event freshness.

## 15. Security

### 351. Implement authentication between services

Use service identity instead of trusting arbitrary broker connections.

### 352. Implement publisher authorization

Limit which services can publish to specific exchanges and topics.

### 353. Implement consumer authorization

Limit reading of queues, topics, and consumer groups according to the principle of least privilege.

### 354. Configure RabbitMQ users and vhosts

Separate environments and service credentials.

### 355. Configure RabbitMQ TLS

Encrypt producer, consumer, and Management API connections.

### 356. Configure Kafka SASL

Use an appropriate authentication mechanism for clients.

### 357. Configure Kafka ACL

Limit access to topics, groups, and transactional IDs.

### 358. Configure Kafka TLS

Encrypt broker and client traffic.

### 359. Configure Redis ACL

Create a separate user with minimal commands and key patterns.

### 360. Configure Redis TLS

Protect background job infrastructure connections.

### 361. Implement secrets management

Do not store broker credentials and TLS keys in the repository or Docker images.

### 362. Implement payload validation security

Reject unknown fields, dangerous types, and invalid enum values.

### 363. Limit message size

Protect consumers and brokers from memory exhaustion through oversized payloads.

### 364. Implement safe deserialization

Do not use unsafe object reconstruction or dynamic code execution.

### 365. Add event authenticity

Research HMAC signing or broker-level trust for events from external sources.

### 366. Implement replay attack protection

Validate messageId, timestamps, and the permitted time window for external commands.

### 367. Protect tenant isolation

Validate tenant scope in the application layer instead of trusting only message metadata.

### 368. Minimize PII in events

Publish identifiers and references instead of copying sensitive data.

### 369. Implement encryption for sensitive payloads

Research field-level encryption and key management.

### 370. Add a security audit log

Record administrative redrive, replay, cancellation, and manual saga actions.

## 16. Deployment and operations

### 371. Create production Dockerfiles

Use a multi-stage build, non-root user, and minimal runtime image.

### 372. Add container health checks

Check application state without performing heavy broker operations.

### 373. Configure resource limits

Limit CPU and memory for services, consumers, and workers.

### 374. Configure graceful deployment

Stop accepting new messages and finish active processing before shutdown.

### 375. Implement consumer draining

Wait for in-flight handlers to finish before removing an instance.

### 376. Configure rolling updates

Update consumers without message loss and mass rebalances.

### 377. Implement blue-green consumers

Launch a new consumer version in parallel with controlled switching.

### 378. Implement a canary consumer

Process a limited share of messages with the new handler version.

### 379. Implement feature flags

Enable new routing, consumers, and workflow steps without a complete redeploy.

### 380. Prepare broker topology migrations

Safely change queues, exchanges, bindings, topics, and partitions.

### 381. Implement zero-downtime event evolution

First update consumers to support the new version, then producers.

### 382. Implement dual publishing

Temporarily publish old and new event versions during migration.

### 383. Implement shadow consumption

Process events with the new version without changing production state.

### 384. Configure consumer autoscaling

Scale consumers by queue depth, message rate, and Kafka lag.

### 385. Limit autoscaling

Prevent PostgreSQL and downstream services from being overloaded by a sharp increase in consumers.

### 386. Create a RabbitMQ backup strategy

Define recovery of definitions, policies, and critical durable messages.

### 387. Create a Kafka backup strategy

Define recovery of topic configuration, metadata, and replicated data.

### 388. Create a Redis backup strategy

Configure storage and recovery of queue state.

### 389. Create a PostgreSQL backup strategy

Provide point-in-time recovery for business state, outbox, inbox, and saga tables.

### 390. Conduct a disaster recovery test

Restore the platform from backups and verify consistency between components.

## 17. Operational tooling

### 391. Create a platform CLI

Combine administrative commands for brokers, outbox, saga, DLQ, and replay.

### 392. Implement an outbox viewing CLI

Show pending, failed, and oldest records with filtering.

### 393. Implement an outbox republishing CLI

Safely republish selected records with audit metadata.

### 394. Implement a DLQ viewing CLI

Retrieve failure metadata and the original payload without automatic deletion.

### 395. Implement a DLQ redrive CLI

Resend messages with dry-run and confirmation.

### 396. Implement a Kafka offsets CLI

Show current offset, end offset, and lag by consumer group.

### 397. Implement a Kafka replay CLI

Create a temporary consumer group or reset offsets with safety checks.

### 398. Implement a RabbitMQ topology CLI

Verify the presence and parameters of exchanges, queues, bindings, and policies.

### 399. Implement a saga inspection CLI

Show state, history, pending step, retries, and compensation status.

### 400. Implement a saga recovery CLI

Resume, cancel, or move a saga into manual-review state.

### 401. Add dry-run mode

Show the consequences of replay, redrive, and repair without changing production state.

### 402. Add operator authorization

Restrict dangerous administrative operations by role.

### 403. Add an operator audit trail

Store the commands, parameters, user, time, and result of each operation.

### 404. Create an operational admin API

Provide a protected HTTP API for dashboards and automation scripts.

### 405. Create a read-only operations dashboard

Show the state of brokers, queues, topics, outbox, DLQ, and active workflows.

## 18. Runbooks and incident response

### 406. Create a RabbitMQ queue growth runbook

Describe diagnostics for producer rate, consumer count, unacked messages, and downstream latency.

### 407. Create a Kafka consumer lag runbook

Describe checks for partitions, rebalances, slow handlers, and database bottlenecks.

### 408. Create a BullMQ backlog runbook

Describe diagnostics for workers, stalled jobs, Redis memory, and rate limits.

### 409. Create a DLQ growth runbook

Describe failure classification, correction of the cause, and safe redrive.

### 410. Create a stuck outbox runbook

Describe checks for polling workers, locks, broker availability, and failed records.

### 411. Create a saga timeout runbook

Describe analysis of history, downstream state, and the choice between resume and compensation.

### 412. Create a duplicate side effect runbook

Describe finding an idempotency failure and restoring business consistency.

### 413. Create an event schema incident runbook

Describe stopping an incompatible producer, rollback, and consumer recovery.

### 414. Create a broker outage runbook

Describe graceful degradation, backlog control, and recovery after the broker returns.

### 415. Create a database outage runbook

Describe pausing consumers, withholding acknowledgements, and safely resuming processing.

### 416. Create a Redis failover runbook

Describe verification of Sentinel, stalled jobs, and reprocessing.

### 417. Create an excessive retries runbook

Describe stopping a retry storm and temporarily isolating the problematic dependency.

### 418. Create an incident severity model

Classify incidents by impact on delivery, consistency, and user operations.

### 419. Create an incident timeline template

Record the start, detection, mitigation, recovery, and corrective actions.

### 420. Conduct a game day

Simulate a broker failure, backlog growth, a DLQ incident, and recovery using the prepared runbooks.

## 19. Documentation and architecture governance

### 421. Create the repository README

Describe the platform purpose, components, infrastructure, and primary demonstration workflow.

### 422. Create a quick start

Document launching infrastructure, migrations, services, and the test scenario.

### 423. Create an architecture overview

Describe service boundaries, transports, databases, and message directions.

### 424. Create a C4 System Context diagram

Show users, external providers, and the platform as a system.

### 425. Create a C4 Container diagram

Show backend services, brokers, databases, workers, and the observability stack.

### 426. Create component diagrams

Show application, domain, ports, adapters, and handlers inside key services.

### 427. Create a happy-path sequence diagram

Show the Order workflow through commands, events, and saga.

### 428. Create a failure-path sequence diagram

Show payment failure, retries, compensation, and final events.

### 429. Create an outbox sequence diagram

Show the transaction, outbox polling, broker acknowledgement, and consumer inbox.

### 430. Create a DLQ redrive sequence diagram

Show failure, dead-lettering, operator action, and reprocessing.

### 431. Document RabbitMQ topology

Describe exchanges, queues, bindings, retry queues, and DLQ.

### 432. Document Kafka topology

Describe topics, partitions, keys, consumer groups, retention, and compaction.

### 433. Document Redis queues

Describe jobs, workers, schedules, retries, deduplication, and retention.

### 434. Document event contracts

For each event, specify the producer, consumers, schema, version, and ordering key.

### 435. Document delivery guarantees

Specify the actual guarantees for RabbitMQ, Kafka, BullMQ, outbox, and consumers.

### 436. Document the failure matrix

For each step, list the possible failure, retry, timeout, DLQ, and compensation.

### 437. Document the consistency model

Establish the source of truth, projections, reconciliation, and permitted consistency windows.

### 438. Create an ADR for choosing RabbitMQ

Justify the scenarios for which a routing-oriented message broker is used.

### 439. Create an ADR for choosing Kafka

Justify event streaming, replay, and long-term retention scenarios.

### 440. Create an ADR for choosing Redis queues

Justify scheduling and background job use cases.

### 441. Create a transactional outbox ADR

Establish the reasons for rejecting dual write between PostgreSQL and the broker.

### 442. Create a saga strategy ADR

Compare choreography and orchestration for the primary workflow.

### 443. Create an idempotency storage ADR

Justify PostgreSQL, Redis, or combined storage of deduplication records.

### 444. Create a schema format ADR

Justify the choice of JSON, Avro, or Protobuf.

### 445. Create architecture invariants

Establish mandatory rules: no shared database, atomic outbox, idempotent consumers, and versioned contracts.

## 20. Final production-style scenarios

### 446. Implement the complete Order workflow through RabbitMQ

Use RabbitMQ commands and events, outbox, inbox, retries, DLQ, and saga orchestration.

### 447. Implement event streaming through Kafka

Publish Order lifecycle history for analytics, projections, and replay.

### 448. Implement background jobs through BullMQ

Use a Redis queue for notifications, reconciliation, cleanup, and scheduled timeout checks.

### 449. Implement simultaneous publication to RabbitMQ and Kafka

Separate operational commands and the durable event stream without violating atomicity.

### 450. Implement broker adapter switching

Allow the application layer to publish messages through ports without depending on a specific SDK.

### 451. Implement end-to-end distributed tracing

Show a single trace from HTTP create order to the notification job.

### 452. Implement a controlled failure scenario

Artificially cause a Payment Service failure and show retries, timeout, and compensation.

### 453. Implement a duplicate delivery scenario

Redeliver a command and event and prove the absence of repeated charging and reservation.

### 454. Implement an out-of-order scenario

Change the event order and show state-machine and saga protection.

### 455. Implement a broker outage scenario

Stop the broker, continue local transactions through the outbox, and restore publication after startup.

### 456. Implement a consumer outage scenario

Stop the consumer, accumulate a backlog, and restore processing without message loss.

### 457. Implement a DLQ recovery scenario

Fix the broken handler and safely perform selective redrive.

### 458. Implement an event replay scenario

Rebuild the read model from a Kafka topic without changing source services.

### 459. Implement a reconciliation scenario

Detect an artificially created inconsistency and perform automated repair.

### 460. Implement a saga manual recovery scenario

Move a stuck workflow into manual-review and continue after operator action.

### 461. Conduct a full load test

Test the platform under target load with RabbitMQ, Kafka, Redis, and PostgreSQL simultaneously.

### 462. Conduct a full chaos test

Combine broker restart, service crash, network latency, and duplicate delivery.

### 463. Verify the recovery point

Confirm that after a crash or restart, every business operation has a defined final state.

### 464. Verify the absence of lost messages

Reconcile business records, outbox, broker records, inbox, and final projections.

### 465. Verify the absence of uncontrolled duplicates

Ensure that repeated deliveries do not create repeated irreversible side effects.

### 466. Verify a bounded backlog

Confirm operation of rate limits, backpressure, autoscaling, and load shedding.

### 467. Verify observability coverage

For each failure scenario, ensure the presence of logs, metrics, traces, and alerts.

### 468. Verify security boundaries

Verify that a service cannot read or publish messages outside the permitted scope.

### 469. Conduct an architecture review

Verify boundaries, dependency rules, contracts, failure handling, and operational readiness.

### 470. Prepare a production readiness checklist

Establish mandatory checks for configuration, reliability, security, monitoring, backups, runbooks, and recovery procedures.
