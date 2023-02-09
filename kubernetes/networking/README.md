# Networking

```
@author: suktae.choi
- https://kubernetes.io/docs/concepts/services-networking/network-policies/
- https://kubernetes.io/ko/docs/concepts/architecture/control-plane-node-communication/
```

### Blog
- [쿠버네티스 네트워킹 이해하기](https://coffeewhale.com/k8s/network/2019/04/19/k8s-network-01/)
- [\[번역\]쿠버네티스 패킷의 삶](https://coffeewhale.com/packet-network1)

***

## kube-dns
https://mrkaran.dev/posts/ndots-kubernetes/

DNS resolution is configured in Kubernetes cluster through CoreDNS. The kubelet configures each Pod's /etc/resolv.conf to use the coredns pod as the nameserver. You can see the contents of /etc/resolv.conf inside any pod, they'll look something like:

```
search hello.svc.cluster.local svc.cluster.local cluster.local
nameserver 10.152.183.10
options ndots:5
```




11.5 kube-proxy 서비스와 (노드의) pod 을 연결 (POD 이 N 개인 경우 LB 의 역할도 수행)
서비스는 가상의 ip:port 를 할당받지만 실질적으로는 더미이다
서비스가 생성되면 apiserver 는 모든 워커노드의 kube-proxy 에 해당 서비스의 ip:port 를 통보한다
kube-proxy 는 iptables 에 해당 서비스의 ip:port 를 등록해서, in/out 가능하도록 한다
- 서비스의 ip:port 가 목적지로 packet sent
- (이미 워커노드의 kube-proxy 에 의해 갱신된 iptables 을 통해) 목적지가 해당 서비스의 matchLabel 에 해당하는 무작위 POD IP:PORT 로 치환
- 실질적으로 해당 POD 으로 dest ip:port 가 네트워크로 sent