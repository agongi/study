# Service

```
@author: suktae.choi
- https://kubernetes.io/docs/concepts/services-networking/service/
- https://kubernetes.io/docs/concepts/services-networking/ingress/
```

Service 는 Pod 으로 접근하는 정책을 정의하는 추상화 된 개념입니다. (ip:port 할당됨)

## NodePort
<img src='1.png' width='50%'/>

```yaml
apiVersion: v1
kind: Service
metadata:
  name: kubia-nodeport
spec:
  type: NodePort
  ports:
    - port: 80 # 서비스 클러스터 IP 포트
      targetPort: 8080 # 서비스 대상 파드의 포트
      nodePort: 30123 # 각 클러스터 노드의 포트 30123을 통해 서비스에 엑세스 할수 있음.
  selector:
    app: kubia
```

## HostPort - Service 의 유형이 아님
<img src='5.png' width='50%'/>

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-host-network
spec:
  hostNetwork: true # 호스트 노드 네트워크 네임스페이스 사용
  containers:
    - name: main
      image: alpine
      command: [ "/bin/sleep", "999999" ]
```

- spec.hostNetwork (== HostPort) 는 Kind: Service 이 아닌 Node 의 포드가 직접 1개의 Pod 으로 전달
  - Node -- Pod 으로 포트가 1:1 로 바인딩
- spec.type: NodePort 는 Kind: Service 를 통해 spec.matchLabel 이 동일한 임의의 Pod 으로 전달된다

## LoadBalancer
L4 LB

- 외부에 endpoint 를 노출하는 역할만 수행

```yaml
apiVersion: v1
kind: Service
metadata:
  name: kubia-loadbalancer
spec:
  type: LoadBalancer
  ports:
    - port: 80 # 서비스 클러스터 IP 포트
      targetPort: 8080 # 서비스 대상 파드의 포트
  selector:
    app: kubia
```

<img src='2.png' width='50%'/>

```yaml
spec:
  externalTrafficPolicy: Local (ClientIP)
```

## Ingress
L7 LB (Ingress Controller 필요)

- 외부에 endpoint 노출
- L7 정책에 따라 기능제공
  - Ingress: 정책 resource
  - Ingress Controller: Ingress (정책) 을 참조하여 동작하는 구현체 (ex. ingress-nginx)

<img src='3.png' width='50%'/>
<img src='4.png' width='50%'/>

```yaml
spec:
  rules:
    - host: foo.example.com
      http:
        paths:
          - path: /
            backend: # foo.example.com -> foo-service
            serviceName: foo
            servicePort: 80
    - host: bar.example.com
      http:
        paths:
          - path: /
            backend: # bar.example.com -> bar-service
            serviceName: bar
            servicePort: 80
```

- headless: service 의 단일 IP 를 노출하는게 아닌, 서비스에 연결된 (label-selector 통해) pod IP 를 직접 전달 (일반적인 A record DNS 처럼 동작)
  - endpoint 연동된 pod 에 직접 connection 을 맺어하는 경우 필요
  - 쿠버네티스 서비스에 클러스터 IP가 필요하지 않다면 ClusterIP 필드를 None으로 설정하여 DNS 서버는 하나의 서비스 IP 대신 파드 IP 목록들을 반환한다.
  - 이때 각 레코는 현재시점 기준으로 서비스를 지원하는 개별 파드의 IP를 가리킨다.
  - 따라서 클라이언트는 간단한 DNS A 레코드 조회를 수행하고 서비스에 포함된 모든 파드의 IP를 얻을 수 있다.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: kubia-headless
spec:
  clusterIP: None # 헤드리스 서비스로 만드는 spec 옵션
  ports:
    - port: 80
      targetPort: 8080
  selector:
    app: kubia
```

<img src='6.png' width='50%'/>

## Headless
Headless Service는 클러스터 내부에서만 사용할 수 있는 서비스 유형입니다.
- Headless Service 는 클러스터의 모든 Pod 의 DNS 레코드를 생성합니다. (클라이언트에게 단일 IP 주소를 제공하지 않음)
- (내부) 클라이언트는 Pod 의 이름을 사용하여 Pod 에 직접 연결할 수 있습니다
- statefulSet 은 headless service 를 통해 구성하는게 일반적 입니다. (특정 Pod 을 지정해서 외부에서 연결해야 하므로)

## Istio
Spring Cloud 에서 제공하는 discovery, config 등의 기능을 통합적으로 제공하는 service mesh 입니다.
- istio <- ingress 대체 (ingress)
- envoy <- nginx 대체 (ingress controller)
- Virtual Service <- Service 대체 (endpoint)

> envoy 에 대한 설정을 nginx.conf 와 달리, k8s resource.spec 으로 정의

Istio 는 아래의 역할들을 처리할 수 있습니다.
- canary 배포 (A/B)
- L7 기반 route (path, cookie)
- discovery (weight)
