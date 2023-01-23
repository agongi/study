## ConfigMap & Secrets

```
ㅁ Author: suktae.choi
ㅁ References:
- https://kubernetes.io/docs/concepts/configuration/configmap
- https://kubernetes.io/docs/concepts/configuration/secret
```

### ConfigMap
yaml 에 환경변수 or ARGS 로 전달할때 phase 에 따라 분기처리필요함
그때 configmap 을 통해 처리가능 (아니면 helpers.tpl 로 매크로 작성)

### Secrets
configmap 과 동일하지만 credentials 처럼 민감정보 저장하는 key-value resource