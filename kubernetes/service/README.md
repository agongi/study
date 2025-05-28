# Service
```
https://kubernetes.io/docs/concepts/services-networking/service/
https://kubernetes.io/docs/concepts/services-networking/ingress/
```

주기적으로 배포/삭제 되는 Pod 은 IP 가 변경될 수 있습니다.

서비스는 ELB 역할을 하며 (기본값: Round-Robin) `외부에 동일한 IP 를 제공하여 외부 접점을 담당`하는 리소스 입니다.

## Session affinity
nginx 의 sticky session 과 유사한 기능을 제공하는 옵션입니다

> TCP 레벨에서의 처리라서 (ClientIP 기반) HTTP 레벨의 쿠키 기반으로는 동작하지 않습니다

## 외부 연결
| 방식             | 외부 접속 | 포트 사용                        | 확장성       | 특징                                                           |
|------------------|-----------|------------------------------|--------------|--------------------------------------------------------------|
| **ClusterIP**     | ❌        | 클러스터 내부 IP                   | ✅           | 기본값. 클러스터 내부에서만 접근 가능                                        |
| **NodePort**      | ✅        | 각 노드의 IP + 30000~32767 포트 사용 | ✅           | 고정 포트로 모든 노드에 노출. 외부 트래픽 수신 가능                               |
| **HostPort**      | ✅        | 노드의 실제 OS 포트 사용              | ❌           | 직접 노드의 포트 점유 (포트 충돌 위험 있음)                                   |
| **LoadBalancer**  | ✅        | 일반적인 80/443 (L4 라우팅)         | ✅           | 서비스에 IP 가 할당되어 외부노출 (L4)                                     |
| **Ingress**       | ✅        | 일반적인 80/443 (L7 라우팅)         | ✅           | 서비스에 IP/도메인이 할당되어 외부노출 (L7) |
| **Headless Service** | ❌    | ✅                            | ✅       | `clusterIP: None`. 각 Pod에 고유 DNS 부여. StatefulSet에서 자주 사용     |

## Istio
k8s 환경에서 서비스 메쉬를 구현하는 플랫폼 입니다
- Envoy Proxy: 모든 서비스에 붙는 사이드카 프록시, 트래픽 관찰/제어/보안 수행
- Ingress: 외부 -> 내부로의 트래픽 제어
- Egress: 내부 -> 외부로의 트래픽 제어
