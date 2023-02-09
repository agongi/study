# Resources

```
@author: suktae.choi
- https://hmh.engineering/dive-into-managing-kubernetes-computational-resources-73283c048360
```

## 가용성
<img src="1.png" width="50%">

- kube-api
  - 상태가 없는 api 서버이므로 N 개를 운영해도 됨 
- etcd
  - raft protocol 을 통한 consensus
  - 1개의 리더가 CUD 처리 (write-concern: majority) (replicas 는 R 대응)
  - 과반수를 위한 홀수대 운영
- controller, scheduler
  - cluster 내 에서 1개의 controller, scheduler 만 active
  - 나머지는 standby

<img src="2.png" width="50%">

## requests
CPU 요청을 지정하지 않으면 컨테이너에서 실행중인 프로세스에 할당되는 CPU시간에 신경쓰지 않는다는 것과 같다. 

최악의 경우 CPU 시간을 전혀 할당받지 못할수도 있다.

> requests 가 잡혀있어도 (즉 해당 수치만큼의 time-slice 할당은 보장) 나머지 파드가 유휴 상태에 있다면 전체 CPU 시간을 사용할 수도 있다

```yaml

```

requests 는 node 총합을 초과할 수 없다 (scheduler 는 requests 를 만족하는 node 에 pod 할당)

> 이미 배포된 파드에 대한 리소스 requests 를 보장하기 위함

## limits
requests 를 지정하지 않으면, limits 와 같은 값이 설정

limits 은 node 의 총합을 초과할 수 있다 (overcommitted 가능)

> limists 이 pod 의 총합을 넘어선 경우, 특정 container 는 killed 된다 (QOS)

- cpu 
  - time-slice 를 지정된 수치만큼 할당받는다는 의미이다
  - `Runtime.getRuntime().availableProcessors()` 
- memory
  - `-XX:+UseContainerSupport` JVM Options 을 주지 않으면 node memory 를 본다 (JDK1.8 191 이상부터 지원)

```yaml

```

## LimitRange
모든 컨테이너에 리소스 요청,제한을 설정하는 대신 LimitRage 리소스를 생성해 이를 수행할 수 있다.

대신 namespace 의 총합을 제한하진 않는다 (개별 container 의 manifest 만 관리)

## ResourceQuota
namespace 의 총합을 제한한다