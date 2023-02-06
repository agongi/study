## Controllers

```
@author: suktae.choi
- https://kubernetes.io/docs/concepts/architecture/controller/
```

Controller 는 N 개의 Pod 을 생성/운영하는 단위이다. Pod 이나 Node 장애시 failover 처리등을 스케쥴링 한다.

### liveness vs readiness
- liveness: 주기적으로 컨테이너 상태를 체크해 응답이 없으면 컨테이너를 자동으로 재시작
- readiness: 응답이 없거나 실패 응답을 보낼 경우 서비스에 제거, 정상 응답인 경우 서비스에 추가 (pod 자체를 kill 하진 않음)

### ReplicationController -> ReplicaSet

replicas 로 명시된 Pod 개수를 유지하는 역할을 담당한다. (label & label-selector 을 통해 pod 제어)

- (RC) label-selector 에서 1개의 label 만 지정가능
- (RS) N 개의 label 지정가능

> A group of pods

하지만 rs 를 직접 생성하진 않고, 상위개념인 ₩Deployment 을 통한 rs 생성`으로 사용한다.

### Deployment

ReplicaSet 을 포함하는 개념으로, Replication & Pod 업데이트 & 스케일링 & Canary 배포 등을 지원하는 그룹이다.

> A group of pods with functionalities

<img src='images/5.png' width='75%'/>

### DaemonSet

replicas 로 명시된 Pod 개수를 유지하는 역할을 담당한다. (ReplicaSet 과의 차이점: 클러스터 모든(또는 일부) 노드에 1개의 POD 생성 보장)

### StatefulSet

Deployment 와 같지만, pod 의 배포/삭제 순서 보장 and/or 상태를 가지는 인프라의 성격이다.

- 배포(생성): 0 -> 1 -> 2
- 삭제: 2 -> 1 -> 0
- jenkins, elasticsearch, airflow 등

### Job/CronJob

`crontab`과 유사하게 지정된 시간/일자에 실행되는 Job 을 생성한다.

> 시간은 master:kube-controller-manager 의 시간대를 사용한다.

Job 은 하나 이상의 Pod 을 생성하고, 명령을 수행한 후 종료된다. 배치작업이나 간단한 lambda 작업을 수행하는데 적합하다.
