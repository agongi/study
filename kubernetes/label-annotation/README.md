# Label & Annotation

```
@author: suktae.choi
- https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
- https://kubernetes.io/docs/concepts/overview/working-with-objects/annotations/
```

## Label
label, label-selector 는 오브젝트(a.k.a pod) 을 control-group 으로 묶고, 식별하기 위해 사용한다

```yaml
metadata:
  name: label-demo
  labels:
    environment: real
    app: nginx
```

```yaml
spec:
  selector:
    matchLabels:
      environment: real
      app: nginx
```

## Annotation
metadata > annotation 으로 (기능적은 아님) `extra 정보의 주석추가` 정도의 용도로 사용

```yaml
metadata:
  name: label-demo
  annotations:
    app: nginx
    image-registry: "https://hub.docker.com/"
```