# Kafka Connect

```
@author: suktae.choi
- https://docs.confluent.io/current/connect/concepts.html
- https://medium.com/clay-one/kafka-connect-cluster-an-introduction-26522e72a9af
```

<img src="1.png" width="75%">

## Mode
### Standalone
1개의 워커로 동작하는 모드 입니다. 메타 정보를 kafka topic 에 저장합니다.

### Distributed
N개의 워커로 동작하는 모드 입니다. 메타 정보를 kafka topic 에 저장합니다.

kafka connect cluster 를 구성하므로 connector 를 CRUD 할수 있는 API 를 제공합니다

> `Kafka Connect Cluster` is a collection of Kafka Connect worker nodes that work together to execute connectors and manage data

### Dedicated
N개의 워커로 동작하는 모드 입니다. 메타 정보를 kafka topic 에 저장합니다.

kafka connect cluster 를 구성하지 않습니다.

> MirrorMaker does not require an existing Connect cluster. Instead, a high-level driver manages a collection of Connect workers.

## 구성요소
### Worker
Workers are just simple Linux (or any other OS) processes and contains connect, task

<img src="2.png" width="75%">

- source 를 적절한 파티션으로 분할하고 (connector)
- 파티션단위의 작업을 실제 전송 (task)

connector 조회/추가/삭제하는 REST API 를 제공합니다.

- connect-configs
- connect-offsets
- connect-status

변경된 설정은 `config.refresh.interval.ms: 60s` 의 주기로 변경 감지되어 worker 에 전파됩니다. 

### Connector
source or sink 에 연결 및 작업을 정의합니다. mysql-source-connector, mongodb-sink-connector 등이 있습니다

```bash
{
  "name": "my-source-connect",
  "config": {
    "connector.class": "io.confluent.connect.jdbc.JdbcSourceConnector",
    "connection.url": "jdbc:mysql://localhost:3306/test",
    "connection.user":"root",
    "connection.password":"비밀번호",
    "mode":"incrementing",
    "incrementing.column.name" : "id",
    "table.whitelist" : "users",
    "topic.prefix" : "example_topic_",
    "tasks.max" : "1"
  }
}

curl -XPOST -d @- http://localhost:8083/connectors --header "content-Type:application/json"
```

### Task
커넥터가 정의한 작업을 직접 수행하는 작업 주체 입니다.