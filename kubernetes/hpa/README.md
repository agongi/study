## HPA (Horizontal Pod Autoscaler)

```
ㅁ Author: suktae.choi
- https://kubernetes.io/ko/docs/tasks/run-application/horizontal-pod-autoscale/
- https://github.com/kubernetes-sigs/metrics-server
```

## Metrics Server

Metrics server is one of the components in core metrics pipeline described in [Kubernetes monitoring architecture](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/instrumentation/monitoring_architecture.md). Metrics server is responsible for collecting resource metrics from kubelets and exposing them in Kubernetes Apiserver through [Metrics API](https://github.com/kubernetes/metrics). Main consumers of those metrics are `kubectl top`, [HPA](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) and [VPA](https://github.com/kubernetes/autoscaler/tree/master/vertical-pod-autoscaler).

## HPA Controller

kubelets --(수집)-- metrics server --(expose)-- kube-apiserver --(monitor/control)-- HPA controller

`--horizontal-pod-autoscaler-sync-period=15s` 주기적으로 HPA Controller 가 메트릭을 가져 오고

해당값이 설정한 scale-out 기준에 맞으면 deployment 에 명령을 내린다.
