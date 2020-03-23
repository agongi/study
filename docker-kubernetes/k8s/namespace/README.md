## Namespace

```
ㅁ Author: suktae.choi
ㅁ References:
- https://kubernetes.io/docs/tasks/administer-cluster/namespaces
```

네임스페이스가 필요한 경우는 아래와 같다:

- Resource Quota 를 각각 적용
- 별도의 권한/인증 체계
- 그에따른 리소스 격리

동일한 Cluster 내에서 격리/리소스제한 등이 필요할때 namespace 로 구분하면된다.

## Useage

List the current namespaces in a cluster using:

```shell
$ kubectl get namespaces
NAME          STATUS    AGE
default       Active    11d
kube-system   Active    11d
kube-public   Active    11d
```

Kubernetes starts with three initial namespaces:

- `default` The default namespace for objects with no other namespace
- `kube-system` The namespace for objects created by the Kubernetes system
- `kube-public` This namespace is created automatically and is readable by all users (including those not authenticated). This namespace is mostly reserved for cluster usage, in case that some resources should be visible and readable publicly throughout the whole cluster. The public aspect of this namespace is only a convention, not a requirement.



