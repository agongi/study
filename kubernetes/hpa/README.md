# HPA (Horizontal Pod Autoscaler)

```
@author: suktae.choi
- https://kubernetes.io/ko/docs/tasks/run-application/horizontal-pod-autoscale/
- https://github.com/kubernetes-sigs/metrics-server
```

<img src="1.png" width="50%">

- (HPA controller) `--horizontal-pod-autoscaler-sync-period=15s` 주기적으로 메트릭을 가져옴 
- (kubelet) 응답
- (HPA controller) 계산값이 설정한 scale-out 기준에 맞으면 deployment 에 명령을 내린다
  - deployment, replicaSet, statefulSet 이 대상 (daemonSet 은 안됨, node 당 1개만 존재해야하므로)


springboot 와 nginx 의 수집흐름정리



