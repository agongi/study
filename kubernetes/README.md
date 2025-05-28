# Kubernetes
```
https://kubernetes.io/ko/docs/concepts/
https://github.com/grepsean/k8skr-study-architecture
https://github.com/sungsu9022/study-kubernetes-in-action/issues
https://github.com/calofmijuck/kubernetes-in-action/tree/main
```

### Index
- [Network](network)
- [Label & Annotation](label-annotation)
- [Policies](policies)
- [HPA](hpa)
- [Helm Charts](helm-charts)
- [Service](service)
- [ConfigMap & Secrets](configmap-secrets)
- [API](api)
- [Isolations](isolations)

### Blog
- [Pod Lifecycle](https://kubernetes.io/ko/docs/concepts/workloads/pods/pod-lifecycle/)

***
**Kubernetes 는 `최대한 바라는 상태` (== spec) 로 컨테이너화된 `앱` (== object) 의 `실제 상태` (== status) 를 조율하는 플랫폼이다.**

## 구조
<img src='1.png' width="50%"/>

### Node
컨테이너화된 어플리케이션을 실행하는 단위 (VM and/or PM).

> 어플리케이션의 구성요소인 Pod 을 호스팅한다.

<img src='2.png' width="50%"/>

### Master Node
클러스터의 상태를 관리하는 단위

- **kube-apiserver**
  - kube api 제공 (ex. kubectl(CLI)/API 통신 & 변경통보)
- kube-controller-manager
  - kube-apiserver 를 통해 클러스터 상태를 감시하고, `최대한 바라는 상태` 로 만드는 event-loop
- kube-scheduler
  - 노드에 pod 을 배치하는 역할 (ex. 신규생성 or 재생성된 Pod 등)
- etcd
  - distributed key-value storage
  - kube-api 는 etcd 의 변경을 감지하여, controller 에게 통보한다

### Worker Node
kube-api 에서 CMD 를 전달받아, container 를 실행하는 단위

- **kubelet**
  - kube-api 와 통신
- kube-proxy
  - kube service 의 구현체 이다. (== network-proxy)
  - iptables, port-forwarding 등
- containerd
  - 실제 docker image 를 runtime 실행 

## 구성요소
### Container
Container 는 Pod 내에서 실행되는 프로세스 입니다. (Docker image 를 실행하는 단위)

### Pod
Container 의 집합으로, Kubernetes 에서 관리하는 가장 작은 단위 입니다.
- 1개의 Container 는 1개의 Process 만 실행하는 것이 일반적이므로, 여러개의 Container 를 묶어서 배포하는 최소 단위
  - 동일한 pod 내의 container 는 network/disk 을 공유 하므로 IPC 등에 제약은 없습니다.

> Node 는 물리적인 구분/Pod 은 논리적인 구분

| 항목              | Liveness Probe                              | Readiness Probe                               |
|-------------------|----------------------------------------------|------------------------------------------------|
| 목적              | 컨테이너가 살아있는지 확인                   | 컨테이너가 트래픽 받을 준비가 되었는지 확인    |
| 실패 시 동작      | 컨테이너를 재시작                            | 서비스에서 제외 (트래픽 안 받음)               |
| 재시작 여부       | 예                                           | 아니오                                         |

## ReplicaSet
- 단순히 Pod 을 복제/유지하는 역할 수행
- Deployment 를 통해 관리 됩니다

## Deployment
- ReplicaSet 을 관리
- 롤링 업데이트/롤백 기능을 제공 합니다

<img src='3.png' width='50%'/>

## DaemonSet
1개의 노드에 최소 1개의 Pod 을 실행하는 역할을 수행 합니다.

> 서비스에선 사용하진 않음. 인프라를 제공하는 곳에서 사용 할듯

## StatefulSet
Deployment (and/or ReplicaSet) 과 유사하지만, `Pod 에 유니크한 식별자가 부여`되어 기존 `PV 를 재사용` 할 수 있어 상태를 유지합니다 

<img src='3-1.png' width='50%'/>
<img src='3-2.png' width='50%'/>

- Identity
  - Deployment: 매번 새롭게 생성되는 식별자 (ex. as7flakjsdhf, 98h34rtsaduh)
  - StatefulSet: 순차적으로 채번되는 유니크 식별자 (ex. web-0, web-1, web-2)
- Storage
  - Deployment: 식별자가 변경되므로 새로운 PV 생성
  - StatefulSet: PersistentVolumeClaim 을 사용하여, 기존 사용했던 PV 재사용

## Job/CronJob
Job 은 일회성 작업을 수행합니다. CronJob 은 주기적으로 Job 을 수행합니다.

> 현업에서 CronJob 은 사용하지 않고 (스케쥴러는 airflow 등 사용) airflow 에서 Job 을 실행하는 형태 적용
