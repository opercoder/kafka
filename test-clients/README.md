# Test Clients

Тестовые клиенты Producer и Consumer использующиеся в практиках.

## Сборка

Для сборки клиентов запустите `mvn package` в корневой директории проекта.

## Producer

Тестовый producer генерирует случайные сообщения типа "оплата" и отправляет их в заданный топик.

Позволяет динамически указывать acks, задержку между отправками и включать идемпотентность.

```
$ java -cp test-clients/target/test-clients-1.0-SNAPSHOT-jar-with-dependencies.jar io.slurm.kafka.TestProducer --help
Usage: <main class> [-hiV] [-a=<acks>] -b=<bootstrapServer> -c=<count>
                    [-s=<sleep>] -t=<topic>
Sends messages to a topic continuously
  -a, --acks=<acks>     Acks configuration, one of [1,0,-1]. If idempotence is
                          enabled "-1" will be enforced. (default: 1)
  -b, --bootstrap-server=<bootstrapServer>
                        Kafka Broker to connect to [HOST:PORT]
  -c, --count=<count>   Number of messages to send
  -h, --help            Show this help message and exit.
  -i, --idempotent      Enable Idempotence (default: false)
  -s, --sleep=<sleep>   Sleep time between message sends, in milliseconds
                          (default: 0ms)
  -t, --topic=<topic>   Kafka Topic to write into
  -V, --version         Print version information and exit.
```

## Consumer

Простое транзакционное приложение совмещающее в себе функционал Consumer и Transactional Producer для
Exactly-Once обработки случайно сгенерированных оплат.

Слушает заданный входной топик `--in` и отфильтровывает платежи с суммой свыше 90 000 USD в топик `--out`.

Позволяет динамически контролировать group id, static membership и cooperative rebalancing консюмера.

```
$ java -cp test-clients/target/test-clients-1.0-SNAPSHOT-jar-with-dependencies.jar io.slurm.kafka.ReadProcessWriteExactlyOnceApp --help
Usage: <main class> [-hV] [--[no-]cooperative-rebalancing] [--[no-]
                    static-membership] -b=<bootstrapServer> -g=<group>
                    --id=<id> --in=<inputTopic> --out=<outputTopic>
Reads messages from a topic and processes them
  -b, --bootstrap-server=<bootstrapServer>
                        Kafka Broker to connect to [HOST:PORT]
  -g, --group=<group>   Consumer Group ID
  -h, --help            Show this help message and exit.
      --id=<id>         Unique Processor ID
      --in, --input-topic=<inputTopic>
                        Kafka Topic to read from
      --[no-]cooperative-rebalancing
                        Enable Cooperative Rebalancing. Default: true
      --[no-]static-membership
                        Enable Static Membership. Default: true
      --out, --output-topic=<outputTopic>
                        Kafka Topic to write into
  -V, --version         Print version information and exit.
```