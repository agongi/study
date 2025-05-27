# ConfigMap & Secrets
```
https://kubernetes.io/docs/concepts/configuration/configmap
https://kubernetes.io/docs/concepts/configuration/secret
```

## ConfigMap
간단한 KEY-VALUE 형태의 저장소

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: fortune-config
data:
  sleep-interval: "25"
```
```shell
# from args
kubectl create configmap myconfigmap --from-literal=foo=bar --from-literal=bar=baz --from-literal=one=two

# from file 
kubectl create configmap my-config --from-file=config-file.conf

# from directory
kubectl create configmap my-config --from-file=/path/to/dir
```
```yaml
spec:
  containers:
  - image: some-image
    envForm:
    - prefix: CONFIG_ # 모든 환경변수는 CONFIG_ prefix로 설정됨.
        configMapRef: # my-config-map 이름의 컨피그맵 참조
          name: my-config-map
```

<img src="1.png" width="50%">

## Secrets
ConfigMap 과 동일 하지만 민감정보 저장소
