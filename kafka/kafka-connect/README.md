# Kafka Connect

```
@author: suktae.choi
- https://docs.confluent.io/current/connect/concepts.html
- https://www.baeldung.com/kafka-connectors-guide
- https://swalloow.github.io/kafka-connect
- https://kimseunghyun76.tistory.com/463
```

**Kafka Connect is a framework for connecting Kafka with external systems.**

- SourceConnector: (external) to kafka
- SinkConnector: kafka to (external)
- Worker: joint connectors to broker

## Worker
### Standalone
단독 worker 로 connectors 를 broker 에 연결한다.

```properties
$ cat << EOF > connect-standalone.properties
bootstrap.servers=localhost:9092
key.converter=org.apache.kafka.connect.json.JsonConverter
value.converter=org.apache.kafka.connect.json.JsonConverter
key.converter.schemas.enable=false
value.converter.schemas.enable=false
offset.storage.file.filename=/tmp/connect.offsets
offset.flush.interval.ms=10000
plugin.path=/share/java
EOF
```

각각의 properties 는 reference 참조하고, 실행은 아래와 같다.

```bash
$ ./connect-standalone \
./connect-standalone.properties \
./connect-file-source.properties \
./connect-file-sink.properties
```

### Distributed
Standalone 은 단독서버에서 worker 가 동작하는 모드이다. SPOF 가 되므로 분산시켜보자.

```bash
$ ./connect-distributed \
./connect-distributed.properties \
```

## Source Connectors
```properties
name=local-file-source
connector.class=FileStreamSource
tasks.max=1
topic=connect-test
file=test.txt
```

## Sink Connectors
```properties
name=local-file-sink
connector.class=FileStreamSink
tasks.max=1
file=test.sink.txt
topics=connect-test
```