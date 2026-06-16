# Event Driven Async Platform Roadmap

## 1. Основы event-driven и asynchronous systems

### 001. Исследовать synchronous и asynchronous communication

Сравнить request-response взаимодействие с асинхронной передачей сообщений по связанности, latency, доступности, отказоустойчивости и сложности эксплуатации.

### 002. Исследовать event-driven architecture

Разобрать producers, consumers, brokers, event channels, subscriptions и влияние event-driven подхода на границы backend-сервисов.

### 003. Разделить command, event и job

Определить семантические различия между командой, фактом произошедшего события и фоновой задачей, закрепив правила их использования в платформе.

### 004. Исследовать message delivery guarantees

Разобрать at-most-once, at-least-once и effectively-once delivery, а также ограничения практической реализации exactly-once semantics.

### 005. Исследовать message ordering

Разобрать global ordering, partition ordering, aggregate ordering и причины нарушения порядка при параллельной обработке.

### 006. Исследовать eventual consistency

Определить модель согласованности между сервисами, возможные временные расхождения данных и способы их обнаружения и устранения.

### 007. Исследовать CAP theorem

Рассмотреть влияние network partition на доступность, согласованность и поведение распределённых asynchronous workflows.

### 008. Исследовать PACELC

Разобрать компромисс между latency и consistency при нормальной работе системы и при сетевых разделениях.

### 009. Исследовать backpressure

Определить способы ограничения producers, consumers и очередей при превышении пропускной способности платформы.

### 010. Составить taxonomy отказов

Классифицировать transient, permanent, infrastructure, business, serialization и poison-message failures.

### 011. Исследовать retry semantics

Разобрать immediate retry, delayed retry, exponential backoff, jitter, retry budget и ограничения количества повторов.

### 012. Исследовать idempotency

Определить требования к идемпотентной обработке команд, событий, HTTP-запросов и внешних side effects.

### 013. Исследовать deduplication

Сравнить producer-side, broker-side и consumer-side deduplication, включая временные окна и хранение ключей.

### 014. Исследовать dead letter queues

Разобрать назначение DLQ, причины попадания сообщений, правила хранения, анализа и повторной обработки.

### 015. Исследовать poison messages

Определить признаки сообщений, которые постоянно ломают consumer, и способы их изоляции без остановки основного потока.

### 016. Исследовать distributed transactions

Сравнить 2PC, transactional outbox, saga и compensation-based подходы.

### 017. Исследовать saga pattern

Разобрать choreography и orchestration, локальные транзакции, компенсирующие операции и состояние saga.

### 018. Исследовать stream processing и message queuing

Сравнить журнал событий Kafka с очередями RabbitMQ и Redis-based job queues.

### 019. Исследовать push и pull consumption

Разобрать модели получения сообщений, управление скоростью consumers и влияние на backpressure.

### 020. Зафиксировать базовые гарантии платформы

Документировать выбранные delivery, ordering, consistency, retry и durability guarantees для каждого транспорта.

## 2. Структура репозитория и базовая инфраструктура

### 021. Создать monorepo

Настроить workspace для нескольких NestJS-приложений, shared libraries, contracts, infrastructure adapters и тестовых утилит.

### 022. Определить структуру приложений

Создать отдельные приложения для Order, Inventory, Payment, Notification, Workflow и Event Gateway сервисов.

### 023. Определить shared libraries

Выделить библиотеки для event contracts, message envelope, observability, broker clients, idempotency и testing.

### 024. Настроить TypeScript

Включить strict mode, project references, path aliases и отдельные конфигурации для приложений и библиотек.

### 025. Настроить linting и formatting

Добавить ESLint, Prettier и правила для предотвращения небезопасного async-кода и необработанных Promise.

### 026. Настроить environment validation

Реализовать проверку конфигурации RabbitMQ, Kafka, Redis, PostgreSQL и observability через Zod или Joi.

### 027. Создать Docker Compose окружение

Добавить PostgreSQL, RabbitMQ, Kafka, Redis и необходимые административные интерфейсы.

### 028. Добавить health checks контейнеров

Настроить проверки готовности инфраструктурных сервисов и зависимости startup order.

### 029. Настроить локальные network aliases

Создать стабильные DNS-имена сервисов для одинаковой конфигурации локального и containerized запуска.

### 030. Добавить development scripts

Реализовать команды запуска всей платформы, отдельных сервисов, migrations, seeds и infrastructure-only окружения.

### 031. Настроить PostgreSQL migrations

Создать независимые схемы или базы данных для сервисов и запретить прямой доступ к чужим таблицам.

### 032. Добавить seed data

Подготовить воспроизводимые данные для order, inventory, payment и workflow сценариев.

### 033. Реализовать graceful shutdown

Корректно останавливать HTTP-серверы, consumers, producers, workers и database connections.

### 034. Реализовать startup sequencing

Не запускать consumers до готовности базы данных, broker connections и обязательных migrations.

### 035. Добавить readiness probes

Проверять возможность сервиса принимать трафик и обрабатывать сообщения.

### 036. Добавить liveness probes

Обнаруживать зависшие процессы без ложного срабатывания при временной недоступности broker.

### 037. Настроить централизованную конфигурацию transports

Создать типизированные настройки exchanges, queues, topics, consumer groups и Redis queues.

### 038. Создать abstraction для clock

Использовать ClockPort для детерминированного тестирования timeout, retries и scheduled jobs.

### 039. Создать abstraction для ID generation

Реализовать UUID или ULID generation через отдельный port для тестируемости и сортируемых идентификаторов.

### 040. Создать abstraction для transactions

Определить TransactionPort, позволяющий application layer выполнять локальные транзакции без зависимости от ORM.

## 3. Domain model и service boundaries

### 041. Определить демонстрационный business flow

Использовать сценарий Order → Inventory Reservation → Payment → Notification как основной распределённый workflow.

### 042. Определить bounded contexts

Разделить Order, Inventory, Payment, Notification и Workflow по ответственности и владению данными.

### 043. Определить aggregate boundaries

Выделить Order, InventoryItem, Payment и SagaInstance как независимые consistency boundaries.

### 044. Спроектировать Order state machine

Определить состояния CREATED, INVENTORY_PENDING, PAYMENT_PENDING, COMPLETED, CANCELLED и FAILED.

### 045. Спроектировать Payment state machine

Определить состояния CREATED, PROCESSING, SUCCEEDED, DECLINED, REFUND_PENDING и REFUNDED.

### 046. Спроектировать Inventory reservation model

Реализовать резервирование, подтверждение и освобождение остатков без прямого изменения данных Order Service.

### 047. Спроектировать Notification model

Разделить notification request, delivery attempt и delivery result.

### 048. Определить ownership данных

Задокументировать, какой сервис является source of truth для каждого типа данных.

### 049. Запретить shared database integration

Обеспечить взаимодействие сервисов только через API, commands и events.

### 050. Определить публичные HTTP API

Создать минимальные synchronous endpoints для создания заказа, просмотра состояния и административных операций.

### 051. Определить asynchronous commands

Составить список команд ReserveInventory, ProcessPayment, ReleaseInventory, RefundPayment и SendNotification.

### 052. Определить domain events

Составить список OrderCreated, InventoryReserved, InventoryRejected, PaymentSucceeded, PaymentFailed и OrderCompleted.

### 053. Определить integration events

Отделить внутренние domain events от событий, публикуемых за границы сервиса.

### 054. Реализовать application handlers

Создать отдельные handlers для HTTP commands, broker commands и events.

### 055. Реализовать infrastructure ports

Определить MessagePublisherPort, MessageConsumerPort, JobQueuePort, IdempotencyStorePort и EventStorePort.

### 056. Реализовать transport adapters

Создать отдельные adapters для RabbitMQ, Kafka и Redis queues.

### 057. Реализовать repository ports

Изолировать domain и application layers от PostgreSQL и выбранного ORM.

### 058. Ввести dependency rules

Запретить зависимости domain/application слоёв от NestJS transport clients, broker SDK и ORM.

### 059. Добавить architecture tests

Автоматически проверять допустимые направления импортов между слоями и модулями.

### 060. Документировать service boundaries

Зафиксировать ответственность, входные команды, выходные события и owned data каждого сервиса.

## 4. Message contracts и schema governance

### 061. Создать общий message envelope

Добавить messageId, messageType, messageVersion, occurredAt, producer, payload и metadata.

### 062. Добавить correlationId

Передавать идентификатор всего distributed workflow между HTTP-запросами, событиями и jobs.

### 063. Добавить causationId

Хранить идентификатор сообщения, вызвавшего публикацию текущего сообщения.

### 064. Добавить trace context

Передавать W3C traceparent и tracestate через broker headers.

### 065. Добавить tenant context

Реализовать безопасную передачу tenantId без доверия к произвольному payload.

### 066. Добавить actor context

Передавать минимально необходимую информацию об инициаторе действия без публикации чувствительных данных.

### 067. Реализовать runtime validation

Проверять message envelope и payload до передачи сообщения application handler.

### 068. Выбрать формат сериализации

Сравнить JSON, Avro и Protobuf и реализовать базовый формат для платформы.

### 069. Ввести event versioning

Использовать явную версию contract и запретить неуправляемое изменение существующей схемы.

### 070. Реализовать backward compatibility rules

Определить допустимые изменения полей, defaults, optional fields и enum values.

### 071. Реализовать upcasting

Преобразовывать старые версии событий в актуальную internal representation.

### 072. Реализовать schema registry abstraction

Создать интерфейс регистрации, получения и проверки совместимости message schemas.

### 073. Добавить schema compatibility tests

Проверять backward и forward compatibility при каждом изменении contracts.

### 074. Создать event catalog

Документировать producers, consumers, payload, version, ordering key и retention для каждого события.

### 075. Добавить payload size limits

Отклонять чрезмерно большие сообщения и использовать reference-based payload для крупных данных.

## 5. RabbitMQ

### 076. Подключить RabbitMQ client

Реализовать управляемое соединение, channels, reconnect и shutdown для NestJS-приложений.

### 077. Исследовать AMQP model

Практически проверить exchanges, queues, bindings, routing keys, acknowledgements и publisher confirms.

### 078. Реализовать direct exchange

Использовать direct routing для адресных business commands.

### 079. Реализовать topic exchange

Использовать topic routing для domain и integration events.

### 080. Реализовать fanout exchange

Продемонстрировать broadcast-событие для независимых subscribers.

### 081. Исследовать headers exchange

Реализовать отдельный пример маршрутизации по message headers и оценить его ограничения.

### 082. Настроить durable exchanges и queues

Обеспечить сохранение topology и сообщений после перезапуска broker.

### 083. Настроить persistent messages

Проверить влияние deliveryMode и publisher confirms на durability.

### 084. Реализовать publisher confirms

Считать публикацию успешной только после broker confirmation.

### 085. Обработать publisher nacks

Реализовать повторную публикацию или запись в local recovery storage при отрицательном подтверждении.

### 086. Реализовать mandatory publishing

Обнаруживать сообщения, для которых отсутствует подходящий binding или queue.

### 087. Обработать returned messages

Логировать и сохранять unroutable messages для анализа и повторной маршрутизации.

### 088. Реализовать manual acknowledgement

Подтверждать сообщение только после успешной application и persistence обработки.

### 089. Реализовать negative acknowledgement

Разделить requeue, reject и dead-letter поведение для разных типов ошибок.

### 090. Настроить consumer prefetch

Ограничить количество unacknowledged сообщений и проверить влияние на throughput и fairness.

### 091. Реализовать competing consumers

Запустить несколько instances одного consumer и проверить распределение нагрузки.

### 092. Проверить ordering при competing consumers

Зафиксировать, при каких условиях RabbitMQ перестаёт обеспечивать ожидаемый порядок.

### 093. Реализовать routing по aggregateId

Создать стратегию направления связанных сообщений одному последовательному consumer.

### 094. Настроить message TTL

Удалять или dead-letter просроченные commands и jobs.

### 095. Настроить queue TTL

Автоматически удалять временные очереди после периода неактивности.

### 096. Настроить max-length policies

Ограничить размер очередей по количеству сообщений и объёму данных.

### 097. Реализовать dead-letter exchange

Создать отдельные DLX и DLQ для каждой business queue.

### 098. Реализовать delayed retries через TTL queues

Создать retry queues с несколькими уровнями задержки без бесконечного immediate requeue.

### 099. Исследовать delayed message exchange

Сравнить RabbitMQ Delayed Message Plugin с TTL-based retry topology.

### 100. Реализовать priority queue

Добавить демонстрационный сценарий обработки срочных команд и проверить риск starvation.

### 101. Исследовать quorum queues

Настроить replicated quorum queues и проверить поведение при отказе broker node.

### 102. Сравнить classic и quorum queues

Зафиксировать различия в latency, throughput, replication и operational semantics.

### 103. Настроить RabbitMQ policies

Управлять TTL, max-length, DLX и queue type через policies, а не только application code.

### 104. Реализовать topology assertion

Автоматически создавать и проверять exchanges, queues и bindings при старте приложения.

### 105. Обработать topology mismatch

Корректно завершать startup при несовместимых параметрах существующей queue или exchange.

### 106. Реализовать connection recovery

Восстанавливать соединения и consumers после временной недоступности RabbitMQ.

### 107. Обработать channel failure

Пересоздавать channel и consumer registration без перезапуска всего сервиса.

### 108. Обработать consumer cancellation

Регистрировать broker-initiated cancellation и восстанавливать consumption.

### 109. Исследовать RabbitMQ flow control

Проверить поведение producers при memory alarm, disk alarm и publisher blocking.

### 110. Настроить RabbitMQ Management API

Получать queue depth, consumer count, publish rate, delivery rate и unacked messages.

## 6. Kafka

### 111. Развернуть Kafka в KRaft mode

Настроить локальный кластер без ZooKeeper и документировать broker/controller roles.

### 112. Подключить Kafka client

Реализовать producer, consumer, admin client, lifecycle hooks и reconnect handling.

### 113. Создать topic naming convention

Определить правила имён для domain events, integration events, retry и dead-letter topics.

### 114. Исследовать partitions

Проверить влияние partition count на parallelism, ordering и scalability.

### 115. Реализовать partition key strategy

Использовать aggregateId или sagaId для сохранения порядка связанных событий.

### 116. Настроить replication factor

Определить параметры replication для development и production environments.

### 117. Исследовать ISR

Проверить влияние in-sync replicas на доступность записи и durability.

### 118. Настроить producer acknowledgements

Сравнить acks=0, acks=1 и acks=all по latency и риску потери данных.

### 119. Настроить min.insync.replicas

Предотвратить подтверждение записи при недостаточном количестве актуальных replicas.

### 120. Реализовать idempotent producer

Включить producer idempotence и проверить устранение duplicate records при retries.

### 121. Настроить producer retries

Определить retry count, delivery timeout, request timeout и backoff.

### 122. Исследовать producer batching

Настроить batch.size и linger.ms и измерить компромисс между throughput и latency.

### 123. Исследовать compression

Сравнить gzip, snappy, lz4 и zstd на демонстрационной нагрузке.

### 124. Реализовать consumer group

Запустить несколько consumers в одной группе и проверить распределение partitions.

### 125. Реализовать независимые consumer groups

Подключить несколько сервисов к одному topic без взаимного влияния offset.

### 126. Исследовать offset management

Сравнить automatic и manual offset commits.

### 127. Реализовать commit после обработки

Фиксировать offset только после успешной business transaction.

### 128. Исследовать commit failure windows

Определить сценарии duplicate processing и message loss между обработкой и offset commit.

### 129. Реализовать batch consumption

Обрабатывать набор records с контролем частичного failure и offset progression.

### 130. Настроить consumer concurrency

Соотнести число consumer instances и handlers с количеством partitions.

### 131. Исследовать rebalancing

Проверить stop-the-world rebalance при подключении и отключении consumers.

### 132. Настроить cooperative rebalancing

Снизить объём перераспределяемых partitions при изменении consumer group.

### 133. Реализовать rebalance listeners

Корректно завершать обработку records перед отзывом partition.

### 134. Настроить heartbeat и session timeout

Предотвратить ложные rebalances при долгой обработке сообщений.

### 135. Реализовать pause и resume

Временно останавливать consumption при перегрузке базы данных или downstream dependency.

### 136. Настроить retention policy

Определить срок хранения событий по времени и объёму.

### 137. Реализовать log compaction

Создать compacted topic для последнего состояния entity или configuration.

### 138. Проверить tombstone records

Реализовать удаление ключа из compacted topic через null payload.

### 139. Реализовать retry topics

Создать отдельные topics для нескольких уровней delayed retry.

### 140. Реализовать Kafka dead-letter topic

Перемещать окончательно необрабатываемые records вместе с failure metadata.

### 141. Реализовать retry topic consumer

Переиздавать сообщения в основной topic после наступления nextAttemptAt.

### 142. Исследовать Kafka transactions

Реализовать атомарную публикацию нескольких records в рамках producer transaction.

### 143. Исследовать consume-transform-produce

Реализовать transactional обработку, связывающую consumed offsets и produced records.

### 144. Проверить read_committed isolation

Исключить чтение aborted transactional records.

### 145. Исследовать exactly-once semantics Kafka

Документировать область действия Kafka EOS и ограничения внешних side effects и PostgreSQL.

### 146. Реализовать event replay

Повторно обработать topic с нового consumer group или сброшенного offset.

### 147. Реализовать selective replay

Повторно обработать records по диапазону offsets, timestamps или business keys.

### 148. Проверить partition expansion

Добавить partitions и исследовать изменение распределения ключей и ordering guarantees.

### 149. Реализовать Kafka Admin tooling

Создавать topics, проверять configuration, offsets, lag и consumer groups через CLI.

### 150. Настроить consumer lag monitoring

Вычислять lag по partitions и агрегировать его по consumer group.

## 7. Redis queues и background jobs

### 151. Подключить BullMQ

Настроить Queue, Worker, QueueEvents и Redis connections.

### 152. Создать background job contracts

Отделить job name, payload schema, options и result schema от конкретного Worker.

### 153. Реализовать базовый Worker

Обрабатывать jobs с контролируемой concurrency и graceful shutdown.

### 154. Исследовать job states

Проверить transitions между waiting, active, completed, failed, delayed и stalled.

### 155. Реализовать delayed jobs

Запланировать выполнение задачи через заданный интервал.

### 156. Реализовать scheduled jobs

Создать повторяющиеся jobs для reconciliation, cleanup и reporting.

### 157. Исследовать repeatable job uniqueness

Предотвратить случайное создание нескольких одинаковых schedules.

### 158. Реализовать job priority

Обработать срочные и обычные задачи с документированным риском starvation.

### 159. Настроить worker concurrency

Измерить влияние concurrency на CPU-bound, I/O-bound и rate-limited jobs.

### 160. Реализовать queue rate limiting

Ограничить частоту вызовов внешнего Notification Provider.

### 161. Реализовать per-tenant rate limiting

Не позволять одному tenant занять всю пропускную способность очереди.

### 162. Реализовать attempts и backoff

Настроить fixed и exponential retry для background jobs.

### 163. Реализовать custom backoff

Учитывать тип ошибки, Retry-After и business constraints.

### 164. Реализовать jobId deduplication

Использовать deterministic jobId для предотвращения повторного добавления одинаковой задачи.

### 165. Исследовать deduplication modes BullMQ

Сравнить simple, throttle и debounce semantics.

### 166. Обработать stalled jobs

Обнаруживать jobs, Worker которых завершился без фиксации результата.

### 167. Настроить lock duration

Соотнести lock renewal и максимальное время выполнения jobs.

### 168. Реализовать job timeout

Прерывать или помечать зависшие jobs с учётом необратимых side effects.

### 169. Реализовать job cancellation

Добавить cooperative cancellation через cancellation flag и AbortSignal.

### 170. Реализовать job progress

Сохранять прогресс длительных операций и предоставлять API его чтения.

### 171. Реализовать job result storage

Определить срок хранения результатов и ограничения размера result payload.

### 172. Реализовать parent-child flows

Использовать BullMQ FlowProducer для многошаговых зависимых background jobs.

### 173. Обработать failure дочернего job

Определить поведение parent job при частичном failure workflow.

### 174. Реализовать sandboxed processors

Вынести CPU-intensive job в отдельный process или Worker Thread.

### 175. Настроить job cleanup

Удалять завершённые и failed jobs по count или age policies.

### 176. Настроить Redis persistence

Сравнить RDB, AOF и комбинированный режим для job durability.

### 177. Исследовать Redis eviction policies

Запретить опасное вытеснение queue metadata при достижении memory limit.

### 178. Настроить Redis Sentinel

Проверить восстановление workers и producers после failover primary node.

### 179. Исследовать Redis Cluster

Проверить совместимость queue keys, hash slots и ограничения multi-key operations.

### 180. Сравнить BullMQ, RabbitMQ и Kafka

Создать decision matrix по scheduling, routing, replay, ordering, durability и operational complexity.

## 8. Transactional Outbox и Inbox

### 181. Спроектировать outbox table

Добавить id, aggregateId, messageType, version, payload, headers, status, attempts и timestamps.

### 182. Реализовать atomic business transaction

Сохранять domain state и outbox record в одной PostgreSQL transaction.

### 183. Реализовать outbox repository

Добавить операции вставки, выборки, блокировки, подтверждения и фиксации ошибки публикации.

### 184. Реализовать polling publisher

Периодически получать необработанные outbox records и публиковать их в broker.

### 185. Использовать FOR UPDATE SKIP LOCKED

Позволить нескольким outbox workers безопасно обрабатывать разные records параллельно.

### 186. Реализовать outbox batch publishing

Публиковать records пакетами с ограничением batch size и времени удержания transaction.

### 187. Разделить publication и database transaction

Не удерживать открытую PostgreSQL transaction во время долгого сетевого вызова без обоснованной стратегии.

### 188. Реализовать outbox claim model

Вводить claimedAt и claimedBy для восстановления зависших records.

### 189. Реализовать publisher confirms для outbox

Помечать RabbitMQ record опубликованным только после broker confirm.

### 190. Реализовать Kafka acknowledgement для outbox

Помечать Kafka record опубликованным только после успешного producer acknowledgement.

### 191. Обработать ambiguous publish result

Считать возможным duplicate message, если producer не получил подтверждение, но broker мог сохранить запись.

### 192. Реализовать outbox retry

Повторять transient publication failures с backoff и максимальным числом попыток.

### 193. Реализовать outbox dead state

Перемещать неисправимые records в отдельное состояние для ручного анализа.

### 194. Реализовать outbox cleanup

Архивировать или удалять успешно опубликованные records по retention policy.

### 195. Партиционировать outbox table

Разделить крупную таблицу по времени и проверить влияние на cleanup и indexes.

### 196. Добавить outbox indexes

Оптимизировать выборку pending records и поиск по aggregateId и messageId.

### 197. Исследовать CDC-based outbox

Сравнить polling publisher с Debezium и PostgreSQL logical replication.

### 198. Реализовать inbox table

Хранить messageId, consumer, receivedAt, processedAt и processing result.

### 199. Реализовать atomic inbox processing

В одной transaction фиксировать inbox record и изменения business state.

### 200. Реализовать duplicate detection

Пропускать сообщение, если соответствующая пара messageId и consumer уже обработана.

### 201. Реализовать inbox retention

Удалять deduplication records только после безопасного временного окна.

### 202. Исследовать deduplication window

Определить срок хранения inbox records на основе broker retention и replay policy.

### 203. Реализовать inbox failure recovery

Различать сообщения, обработка которых не начиналась, выполняется или завершилась ошибкой.

### 204. Добавить outbox и inbox metrics

Собирать pending count, publication latency, retry count, duplicate count и processing latency.

### 205. Протестировать crash windows outbox/inbox

Проверить падение процесса до commit, после commit, до publish acknowledgement и после обработки consumer.

## 9. Idempotency, retries и fault tolerance

### 206. Реализовать HTTP idempotency keys

Сохранять результат повторяемых create-команд и возвращать его при повторном запросе с тем же ключом.

### 207. Проверять idempotency payload fingerprint

Отклонять повторное использование ключа с другим request payload.

### 208. Реализовать command idempotency

Гарантировать, что повторная delivery команды не создаёт повторные business side effects.

### 209. Реализовать event handler idempotency

Защитить projection updates и downstream publications от duplicate events.

### 210. Реализовать external API idempotency

Передавать idempotency key в Payment Provider и Notification Provider, если это поддерживается.

### 211. Реализовать side-effect ledger

Сохранять факт выполнения необратимых внешних операций.

### 212. Разделить retryable и non-retryable errors

Создать типизированную классификацию infrastructure, timeout, validation и business failures.

### 213. Реализовать exponential backoff

Увеличивать задержку повторов без перегрузки недоступной зависимости.

### 214. Добавить jitter

Предотвратить synchronized retry storm при восстановлении внешнего сервиса.

### 215. Реализовать retry budget

Ограничить суммарное число повторов и время жизни операции.

### 216. Реализовать timeout policy

Задать отдельные timeout для broker operations, database queries и external HTTP calls.

### 217. Передавать deadline

Останавливать downstream operations, если общий deadline workflow уже истёк.

### 218. Реализовать circuit breaker

Прекращать обращения к временно неисправной зависимости и выполнять controlled recovery probes.

### 219. Реализовать bulkhead isolation

Ограничить concurrency и ресурсы для независимых downstream systems.

### 220. Реализовать rate limiting producers

Не позволять producer создавать нагрузку быстрее допустимой скорости обработки.

### 221. Реализовать consumer load shedding

Откладывать или отклонять низкоприоритетную работу при критической загрузке.

### 222. Реализовать retry storm protection

Ограничить одновременный возврат большого объёма сообщений из retry queues.

### 223. Реализовать DLQ metadata

Сохранять original destination, exception type, stack trace, attempts и failedAt.

### 224. Реализовать DLQ inspection API

Предоставить фильтрацию dead-letter messages по сервису, типу, причине и времени.

### 225. Реализовать DLQ redrive

Повторно отправлять выбранные сообщения в исходную queue или topic.

### 226. Проверять schema перед redrive

Не возвращать в основной поток сообщения с несовместимой или повреждённой схемой.

### 227. Реализовать selective redrive

Повторно обрабатывать только сообщения, соответствующие заданному failure reason или business key.

### 228. Реализовать quarantine storage

Изолировать сообщения, требующие ручного исправления payload или business data.

### 229. Добавить redrive audit

Фиксировать кто, когда и почему повторно отправил сообщение.

### 230. Реализовать graceful degradation

Продолжать основную business operation при временной недоступности необязательных consumers, например Notification Service.

## 10. Saga и asynchronous workflows

### 231. Выбрать saga scenario

Использовать создание заказа с резервированием inventory, списанием payment и отправкой notification.

### 232. Реализовать choreography saga

Связать сервисы через последовательность событий без центрального orchestrator.

### 233. Документировать choreography dependencies

Зафиксировать скрытые связи между producers и consumers и риск циклических event chains.

### 234. Реализовать orchestration saga

Создать Workflow Service, отправляющий commands и обрабатывающий результаты шагов.

### 235. Создать saga state machine

Определить состояния, transitions, terminal states и допустимые события.

### 236. Создать saga persistence model

Сохранять sagaId, state, version, step data, deadlines и timestamps.

### 237. Реализовать optimistic locking saga

Предотвратить конкурентную обработку нескольких событий одной saga.

### 238. Реализовать saga command dispatch

Публиковать следующий command только после успешного изменения состояния saga.

### 239. Использовать outbox в orchestrator

Атомарно сохранять новое состояние saga и команду следующего шага.

### 240. Реализовать compensating transaction Inventory

Освобождать reservation при отказе оплаты или отмене заказа.

### 241. Реализовать compensating transaction Payment

Возвращать средства, если workflow не может завершиться после успешной оплаты.

### 242. Сделать compensation идемпотентной

Безопасно выполнять повторное ReleaseInventory и RefundPayment.

### 243. Реализовать saga timeout

Переводить зависшую saga в compensation или manual-review состояние.

### 244. Реализовать scheduled timeout checker

Использовать Redis queue или отдельный scheduler для поиска просроченных saga.

### 245. Обрабатывать late events

Игнорировать, компенсировать или отдельно фиксировать события, пришедшие после timeout.

### 246. Обрабатывать out-of-order events

Проверять допустимость transition и временно откладывать события, пришедшие раньше ожидаемого шага.

### 247. Реализовать saga retry policy

Настроить отдельные retry rules для каждого шага workflow.

### 248. Реализовать saga cancellation

Позволить пользователю отменить workflow до достижения необратимого шага.

### 249. Реализовать manual intervention

Добавить состояние, в котором operator должен принять решение о продолжении или компенсации.

### 250. Реализовать saga audit trail

Сохранять полную историю transitions, commands, events, retries и compensations.

### 251. Реализовать saga versioning

Поддержать изменение workflow definition для уже запущенных saga instances.

### 252. Реализовать migration saga state

Преобразовывать persisted state старой версии orchestration.

### 253. Реализовать parallel saga steps

Одновременно выполнять независимые действия и ожидать их объединённого результата.

### 254. Реализовать join semantics

Определить поведение при полном успехе, частичном успехе и failure параллельных шагов.

### 255. Реализовать long-running workflow

Создать workflow, который может выполняться часы или дни и восстанавливаться после рестарта.

### 256. Реализовать human-in-the-loop step

Приостанавливать workflow до внешнего подтверждения пользователя или operator.

### 257. Реализовать workflow query API

Возвращать текущее состояние, завершённые шаги, pending actions и failure reason.

### 258. Реализовать workflow resume

Продолжать выполнение после исправления данных или восстановления зависимости.

### 259. Сравнить custom orchestrator с Temporal

Исследовать durable execution, retries, timers, state recovery и operational complexity Temporal.

### 260. Сравнить custom orchestrator с Camunda

Исследовать BPMN, external tasks, human workflows и границы применения process engine.

## 11. Read models, projections и reconciliation

### 261. Создать asynchronous read model

Собирать агрегированное состояние заказа из событий нескольких сервисов.

### 262. Реализовать projection consumer

Обновлять PostgreSQL read model через идемпотентную обработку событий.

### 263. Хранить projection offset

Фиксировать позицию consumer для восстановления и контроля consistency.

### 264. Реализовать projection rebuild

Полностью перестраивать read model из Kafka event history.

### 265. Реализовать blue-green projection rebuild

Строить новую версию projection параллельно и переключать чтение после завершения.

### 266. Добавить projection version

Поддержать несколько реализаций read model для одной event stream.

### 267. Измерять consistency lag

Вычислять задержку между occurredAt события и обновлением projection.

### 268. Реализовать stale-data indicator

Показывать клиенту возраст или актуальность eventual-consistent read model.

### 269. Реализовать reconciliation job

Периодически сравнивать Order, Inventory, Payment и Saga state.

### 270. Создать consistency invariants

Формализовать условия вроде «COMPLETED order должен иметь confirmed inventory и succeeded payment».

### 271. Реализовать inconsistency detection

Находить нарушения invariants и сохранять их в отдельную таблицу.

### 272. Реализовать automated repair

Безопасно исправлять известные типы расхождений через идемпотентные commands.

### 273. Реализовать manual repair workflow

Создать operator flow для неоднозначных consistency conflicts.

### 274. Добавить anti-entropy process

Периодически синхронизировать состояние между source services и derived read models.

### 275. Документировать consistency windows

Определить допустимое время рассогласования для каждого пользовательского сценария.

## 12. Testing

### 276. Создать unit tests domain state machines

Проверить допустимые и запрещённые transitions Order, Payment и Saga.

### 277. Создать unit tests message validation

Проверить envelope, payload schemas, versions и обязательные metadata.

### 278. Создать unit tests routing logic

Проверить RabbitMQ routing keys, Kafka partition keys и queue names.

### 279. Создать unit tests retry policies

Проверить backoff, jitter, retry classification и retry budget.

### 280. Создать unit tests idempotency

Проверить повторную обработку одинакового command, event и job.

### 281. Создать unit tests outbox publisher

Проверить claim, publish, confirm, retry и dead-state transitions.

### 282. Создать unit tests saga orchestrator

Проверить success, failure, timeout, compensation и cancellation paths.

### 283. Создать contract tests

Проверить совместимость producers и consumers без запуска полной системы.

### 284. Создать consumer-driven contract tests

Позволить consumers фиксировать ожидаемую форму events и commands.

### 285. Настроить Testcontainers PostgreSQL

Запускать integration tests на реальной версии PostgreSQL.

### 286. Настроить Testcontainers RabbitMQ

Проверять exchanges, queues, acknowledgements, retries и DLQ на реальном broker.

### 287. Настроить Testcontainers Kafka

Проверять partitions, offsets, consumer groups и replay на реальном broker.

### 288. Настроить Testcontainers Redis

Проверять BullMQ jobs, retries, delays и stalled recovery.

### 289. Создать RabbitMQ integration tests

Проверить publish-confirm-consume-ack workflow.

### 290. Создать Kafka integration tests

Проверить producer-consumer-offset workflow и consumer group rebalancing.

### 291. Создать BullMQ integration tests

Проверить delayed jobs, attempts, custom backoff и job deduplication.

### 292. Протестировать duplicate delivery

Доставлять одно сообщение несколько раз и проверять отсутствие повторных side effects.

### 293. Протестировать out-of-order delivery

Изменять порядок событий и проверять state transition protection.

### 294. Протестировать malformed messages

Отправлять некорректный JSON, неизвестную версию и invalid payload.

### 295. Протестировать poison messages

Проверить ограниченное число retries и последующий переход в DLQ.

### 296. Протестировать consumer crash до commit

Завершать процесс после business operation, но до acknowledgement или offset commit.

### 297. Протестировать consumer crash после commit

Проверить отсутствие повреждения state при падении сразу после фиксации обработки.

### 298. Протестировать producer crash

Завершать outbox publisher на разных этапах публикации и подтверждения.

### 299. Протестировать broker restart

Перезапускать RabbitMQ, Kafka и Redis во время активной нагрузки.

### 300. Протестировать PostgreSQL outage

Проверить остановку acknowledgements, pause consumers и восстановление после возврата базы.

### 301. Протестировать network latency

Добавить задержку и packet loss между сервисами и brokers.

### 302. Протестировать network partition

Изолировать producer или consumer от broker и проверить recovery behavior.

### 303. Протестировать RabbitMQ node failure

Остановить node quorum-кластера и проверить availability и data safety.

### 304. Протестировать Kafka broker failure

Остановить leader partition и проверить election, producer retries и consumer recovery.

### 305. Протестировать Redis failover

Переключить Sentinel primary во время обработки jobs.

### 306. Создать end-to-end happy path

Проверить полное прохождение заказа через все services, brokers и read model.

### 307. Создать end-to-end payment failure path

Проверить compensation Inventory и итоговое состояние заказа.

### 308. Создать end-to-end timeout path

Проверить saga timeout, retries, compensation и audit trail.

### 309. Создать end-to-end duplicate path

Повторно отправить commands и events и проверить effectively-once результат.

### 310. Создать deterministic test fixtures

Использовать фиксированные clock, IDs, message timestamps и broker payloads.

## 13. Performance и capacity planning

### 311. Определить performance targets

Зафиксировать target throughput, p95/p99 latency, queue lag и допустимую длительность workflow.

### 312. Создать RabbitMQ load test

Измерить publish rate, consume rate, unacked count и queue growth.

### 313. Создать Kafka load test

Измерить throughput по partitions, producer latency, consumer lag и rebalance impact.

### 314. Создать BullMQ load test

Измерить job throughput, Redis memory usage и stalled-job rate.

### 315. Сравнить serialization formats

Измерить размер payload и CPU cost JSON, Avro и Protobuf.

### 316. Исследовать message batching

Сравнить одиночную и пакетную публикацию и обработку.

### 317. Исследовать payload size impact

Измерить влияние крупных сообщений на broker memory, network и consumer latency.

### 318. Реализовать payload offloading

Сохранять крупные данные в object storage или database и передавать reference.

### 319. Настроить consumer concurrency benchmark

Найти оптимальное число consumers для каждого типа workload.

### 320. Настроить prefetch benchmark

Измерить RabbitMQ throughput и fairness при разных prefetch values.

### 321. Настроить Kafka partition benchmark

Определить необходимое количество partitions для целевого parallelism.

### 322. Измерить outbox publication latency

Определить задержку от commit business transaction до публикации события.

### 323. Измерить end-to-end event latency

Измерить время от producer operation до завершения всех обязательных consumers.

### 324. Провести soak test

Запустить длительную нагрузку для обнаружения memory leaks, connection leaks и backlog growth.

### 325. Создать capacity model

Рассчитать необходимое количество brokers, partitions, queues, consumers и database resources.

## 14. Observability

### 326. Реализовать structured logging

Использовать JSON logs с service, environment, messageId, correlationId, causationId и traceId.

### 327. Добавить logging context propagation

Автоматически переносить metadata между HTTP, RabbitMQ, Kafka и BullMQ handlers.

### 328. Санитизировать logs

Удалять credentials, payment data, tokens и чувствительные payload fields.

### 329. Реализовать OpenTelemetry instrumentation

Настроить traces, metrics и context propagation для всей платформы.

### 330. Добавить HTTP tracing

Создавать server и client spans для synchronous API calls.

### 331. Добавить RabbitMQ tracing

Создавать producer и consumer spans с exchange, queue и routing key attributes.

### 332. Добавить Kafka tracing

Создавать spans с topic, partition, offset и consumer group attributes.

### 333. Добавить BullMQ tracing

Создавать spans для enqueue, waiting time и job processing.

### 334. Связать traces через message headers

Продолжать trace между независимыми процессами и отложенными retries.

### 335. Добавить producer metrics

Собирать published, failed, retried, confirmed и unroutable message counters.

### 336. Добавить consumer metrics

Собирать received, processed, failed, retried и duplicate message counters.

### 337. Добавить processing histograms

Измерять duration handlers, jobs, saga steps и external API calls.

### 338. Добавить queue depth metrics

Собирать ready, unacked, delayed, active и failed counts.

### 339. Добавить Kafka lag metrics

Собирать lag по topic, partition и consumer group.

### 340. Добавить outbox metrics

Собирать pending count, oldest pending age, publication latency и dead records.

### 341. Добавить saga metrics

Собирать active, completed, failed, compensating и timed-out saga counts.

### 342. Добавить consistency metrics

Собирать projection lag и число обнаруженных reconciliation conflicts.

### 343. Создать Grafana dashboard RabbitMQ

Показать queue depth, publish rate, consume rate, unacked и consumer count.

### 344. Создать Grafana dashboard Kafka

Показать producer rate, consumer rate, partition lag и rebalance events.

### 345. Создать Grafana dashboard BullMQ

Показать waiting, active, delayed, failed, stalled и completed jobs.

### 346. Создать Grafana dashboard workflows

Показать saga duration, success rate, compensation rate и timeout rate.

### 347. Создать distributed trace examples

Документировать trace полного заказа через HTTP, brokers, services и background jobs.

### 348. Добавить correlation search

Обеспечить поиск всех logs и traces одного distributed workflow по correlationId.

### 349. Реализовать alerting rules

Настроить alerts на backlog growth, consumer lag, DLQ growth, outbox age и saga timeouts.

### 350. Определить SLI и SLO

Зафиксировать availability, processing latency, successful workflow rate и event freshness.

## 15. Security

### 351. Реализовать authentication между сервисами

Использовать service identity вместо доверия к произвольным broker connections.

### 352. Реализовать authorization publishers

Ограничить, какие сервисы могут публиковать в конкретные exchanges и topics.

### 353. Реализовать authorization consumers

Ограничить чтение queues, topics и consumer groups по принципу least privilege.

### 354. Настроить RabbitMQ users и vhosts

Разделить environments и сервисные credentials.

### 355. Настроить RabbitMQ TLS

Шифровать соединения producers, consumers и management API.

### 356. Настроить Kafka SASL

Использовать подходящий механизм authentication для clients.

### 357. Настроить Kafka ACL

Ограничить доступ к topics, groups и transactional IDs.

### 358. Настроить Kafka TLS

Шифровать broker и client traffic.

### 359. Настроить Redis ACL

Создать отдельного пользователя с минимальными командами и key patterns.

### 360. Настроить Redis TLS

Защитить connections background job infrastructure.

### 361. Реализовать secrets management

Не хранить broker credentials и TLS keys в репозитории или Docker images.

### 362. Реализовать payload validation security

Отклонять неизвестные поля, опасные типы и некорректные enum values.

### 363. Ограничить message size

Защитить consumers и brokers от memory exhaustion через oversized payload.

### 364. Реализовать safe deserialization

Не использовать unsafe object reconstruction и dynamic code execution.

### 365. Добавить event authenticity

Исследовать HMAC signing или broker-level trust для событий из внешних источников.

### 366. Реализовать replay attack protection

Проверять messageId, timestamps и допустимое временное окно для внешних commands.

### 367. Защитить tenant isolation

Проверять tenant scope в application layer, а не доверять только message metadata.

### 368. Минимизировать PII в events

Публиковать identifiers и references вместо копирования чувствительных данных.

### 369. Реализовать encryption для чувствительных payload

Исследовать field-level encryption и управление ключами.

### 370. Добавить security audit log

Фиксировать административный redrive, replay, cancellation и manual saga actions.

## 16. Deployment и эксплуатация

### 371. Создать production Dockerfiles

Использовать multi-stage build, non-root user и минимальный runtime image.

### 372. Добавить container health checks

Проверять состояние приложения без выполнения тяжёлых broker operations.

### 373. Настроить resource limits

Ограничить CPU и memory для services, consumers и workers.

### 374. Настроить graceful deployment

Останавливать приём новых сообщений и завершать активную обработку перед shutdown.

### 375. Реализовать consumer draining

Дождаться завершения in-flight handlers перед удалением instance.

### 376. Настроить rolling updates

Обновлять consumers без потери сообщений и массовых rebalances.

### 377. Реализовать blue-green consumers

Запускать новую версию consumer параллельно с контролируемым переключением.

### 378. Реализовать canary consumer

Обрабатывать ограниченную долю сообщений новой версией handler.

### 379. Реализовать feature flags

Включать новые routing, consumers и workflow steps без полного redeploy.

### 380. Подготовить broker topology migrations

Безопасно изменять queues, exchanges, bindings, topics и partitions.

### 381. Реализовать zero-downtime event evolution

Сначала обновлять consumers для поддержки новой версии, затем producers.

### 382. Реализовать dual publishing

Временно публиковать старую и новую версии события во время миграции.

### 383. Реализовать shadow consumption

Обрабатывать события новой версией без изменения production state.

### 384. Настроить consumer autoscaling

Масштабировать consumers по queue depth, message rate и Kafka lag.

### 385. Ограничить autoscaling

Предотвратить перегрузку PostgreSQL и downstream services при резком увеличении consumers.

### 386. Создать backup strategy RabbitMQ

Определить восстановление definitions, policies и критичных durable messages.

### 387. Создать backup strategy Kafka

Определить восстановление topic configuration, metadata и replicated data.

### 388. Создать backup strategy Redis

Настроить сохранение и восстановление queue state.

### 389. Создать backup strategy PostgreSQL

Обеспечить point-in-time recovery для business state, outbox, inbox и saga tables.

### 390. Провести disaster recovery test

Восстановить платформу из backups и проверить consistency между компонентами.

## 17. Operational tooling

### 391. Создать platform CLI

Объединить административные команды для brokers, outbox, saga, DLQ и replay.

### 392. Реализовать CLI просмотра outbox

Показывать pending, failed и oldest records с фильтрацией.

### 393. Реализовать CLI повторной публикации outbox

Безопасно переиздавать выбранные records с audit metadata.

### 394. Реализовать CLI просмотра DLQ

Получать failure metadata и исходный payload без автоматического удаления.

### 395. Реализовать CLI redrive DLQ

Повторно отправлять сообщения с dry-run и подтверждением.

### 396. Реализовать CLI Kafka offsets

Показывать current offset, end offset и lag по consumer groups.

### 397. Реализовать CLI Kafka replay

Создавать временную consumer group или сбрасывать offsets с защитными проверками.

### 398. Реализовать CLI RabbitMQ topology

Проверять наличие и параметры exchanges, queues, bindings и policies.

### 399. Реализовать CLI saga inspection

Показывать state, history, pending step, retries и compensation status.

### 400. Реализовать CLI saga recovery

Возобновлять, отменять или переводить saga в manual-review состояние.

### 401. Добавить dry-run mode

Показывать последствия replay, redrive и repair без изменения production state.

### 402. Добавить operator authorization

Ограничить опасные административные операции по ролям.

### 403. Добавить operator audit trail

Сохранять команды, параметры, пользователя, время и результат каждой операции.

### 404. Создать operational admin API

Предоставить защищённый HTTP API для dashboard и automation scripts.

### 405. Создать read-only operations dashboard

Показать состояние brokers, queues, topics, outbox, DLQ и active workflows.

## 18. Runbooks и incident response

### 406. Создать runbook роста RabbitMQ queue

Описать диагностику producer rate, consumer count, unacked messages и downstream latency.

### 407. Создать runbook Kafka consumer lag

Описать проверку partitions, rebalances, slow handlers и database bottlenecks.

### 408. Создать runbook BullMQ backlog

Описать диагностику workers, stalled jobs, Redis memory и rate limits.

### 409. Создать runbook роста DLQ

Описать классификацию failures, исправление причины и безопасный redrive.

### 410. Создать runbook зависшего outbox

Описать проверку polling workers, locks, broker availability и failed records.

### 411. Создать runbook saga timeout

Описать анализ истории, downstream state и выбор resume или compensation.

### 412. Создать runbook duplicate side effect

Описать поиск idempotency failure и восстановление business consistency.

### 413. Создать runbook event schema incident

Описать остановку несовместимого producer, rollback и recovery consumers.

### 414. Создать runbook broker outage

Описать graceful degradation, backlog control и восстановление после возврата broker.

### 415. Создать runbook database outage

Описать pause consumers, отказ acknowledgements и безопасное возобновление обработки.

### 416. Создать runbook Redis failover

Описать проверку Sentinel, stalled jobs и повторную обработку.

### 417. Создать runbook excessive retries

Описать остановку retry storm и временную изоляцию проблемной зависимости.

### 418. Создать incident severity model

Классифицировать события по влиянию на delivery, consistency и пользовательские операции.

### 419. Создать incident timeline template

Фиксировать начало, обнаружение, mitigation, recovery и corrective actions.

### 420. Провести game day

Сымитировать отказ broker, рост backlog, DLQ incident и recovery по подготовленным runbooks.

## 19. Документация и architecture governance

### 421. Создать README репозитория

Описать назначение платформы, компоненты, инфраструктуру и основной демонстрационный workflow.

### 422. Создать quick start

Документировать запуск infrastructure, migrations, services и тестового сценария.

### 423. Создать architecture overview

Описать service boundaries, transports, databases и направления сообщений.

### 424. Создать C4 System Context diagram

Показать пользователей, внешние providers и платформу как систему.

### 425. Создать C4 Container diagram

Показать backend services, brokers, databases, workers и observability stack.

### 426. Создать component diagrams

Показать application, domain, ports, adapters и handlers внутри ключевых сервисов.

### 427. Создать sequence diagram happy path

Показать прохождение Order workflow через commands, events и saga.

### 428. Создать sequence diagram failure path

Показать payment failure, retries, compensation и итоговые события.

### 429. Создать sequence diagram outbox

Показать transaction, outbox polling, broker acknowledgement и consumer inbox.

### 430. Создать sequence diagram DLQ redrive

Показать failure, dead-lettering, operator action и повторную обработку.

### 431. Документировать RabbitMQ topology

Описать exchanges, queues, bindings, retry queues и DLQ.

### 432. Документировать Kafka topology

Описать topics, partitions, keys, consumer groups, retention и compaction.

### 433. Документировать Redis queues

Описать jobs, workers, schedules, retries, deduplication и retention.

### 434. Документировать event contracts

Для каждого события указать producer, consumers, schema, version и ordering key.

### 435. Документировать delivery guarantees

Указать фактические гарантии для RabbitMQ, Kafka, BullMQ, outbox и consumers.

### 436. Документировать failure matrix

Для каждого шага перечислить возможный failure, retry, timeout, DLQ и compensation.

### 437. Документировать consistency model

Зафиксировать source of truth, projections, reconciliation и допустимые consistency windows.

### 438. Создать ADR выбора RabbitMQ

Обосновать сценарии, для которых используется routing-oriented message broker.

### 439. Создать ADR выбора Kafka

Обосновать сценарии event streaming, replay и long-term retention.

### 440. Создать ADR выбора Redis queues

Обосновать scheduling и background job use cases.

### 441. Создать ADR transactional outbox

Зафиксировать причины отказа от dual write между PostgreSQL и broker.

### 442. Создать ADR saga strategy

Сравнить choreography и orchestration для основного workflow.

### 443. Создать ADR idempotency storage

Обосновать PostgreSQL, Redis или комбинированное хранение deduplication records.

### 444. Создать ADR schema format

Обосновать выбор JSON, Avro или Protobuf.

### 445. Создать architecture invariants

Зафиксировать обязательные правила: no shared database, atomic outbox, idempotent consumers и versioned contracts.

## 20. Итоговые production-style сценарии

### 446. Реализовать полный Order workflow через RabbitMQ

Использовать RabbitMQ commands и events, outbox, inbox, retries, DLQ и saga orchestration.

### 447. Реализовать event streaming через Kafka

Публиковать историю Order lifecycle для analytics, projections и replay.

### 448. Реализовать background jobs через BullMQ

Использовать Redis queue для notifications, reconciliation, cleanup и scheduled timeout checks.

### 449. Реализовать одновременную публикацию в RabbitMQ и Kafka

Разделить operational commands и durable event stream без нарушения atomicity.

### 450. Реализовать broker adapter switching

Позволить application layer публиковать сообщения через ports без зависимости от конкретного SDK.

### 451. Реализовать end-to-end distributed tracing

Показать единый trace от HTTP create order до notification job.

### 452. Реализовать controlled failure scenario

Искусственно вызвать отказ Payment Service и показать retries, timeout и compensation.

### 453. Реализовать duplicate delivery scenario

Повторно доставить command и event и доказать отсутствие повторного списания и резервирования.

### 454. Реализовать out-of-order scenario

Изменить порядок событий и показать защиту state machine и saga.

### 455. Реализовать broker outage scenario

Остановить broker, продолжить локальные transactions через outbox и восстановить публикацию после запуска.

### 456. Реализовать consumer outage scenario

Остановить consumer, накопить backlog и восстановить обработку без потери сообщений.

### 457. Реализовать DLQ recovery scenario

Исправить broken handler и безопасно выполнить selective redrive.

### 458. Реализовать event replay scenario

Перестроить read model из Kafka topic без изменения source services.

### 459. Реализовать reconciliation scenario

Обнаружить искусственно созданную inconsistency и выполнить automated repair.

### 460. Реализовать saga manual recovery scenario

Перевести зависший workflow в manual-review и продолжить после operator action.

### 461. Провести full load test

Проверить платформу под целевой нагрузкой с RabbitMQ, Kafka, Redis и PostgreSQL одновременно.

### 462. Провести full chaos test

Комбинировать broker restart, service crash, network latency и duplicate delivery.

### 463. Проверить recovery point

Подтвердить, что после crash или restart каждое business operation имеет определённое итоговое состояние.

### 464. Проверить отсутствие lost messages

Сопоставить business records, outbox, broker records, inbox и final projections.

### 465. Проверить отсутствие uncontrolled duplicates

Убедиться, что повторные deliveries не создают повторных необратимых side effects.

### 466. Проверить bounded backlog

Подтвердить работу rate limits, backpressure, autoscaling и load shedding.

### 467. Проверить observability coverage

Для каждого failure scenario убедиться в наличии logs, metrics, traces и alerts.

### 468. Проверить security boundaries

Проверить, что сервис не может читать или публиковать сообщения вне разрешённого scope.

### 469. Провести architecture review

Проверить boundaries, dependency rules, contracts, failure handling и operational readiness.

### 470. Подготовить production readiness checklist

Зафиксировать обязательные проверки конфигурации, reliability, security, monitoring, backups, runbooks и recovery procedures.
