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

- nameserver
  - lookup 할 DNS 서버
- search
  - ndots 를 충족하지 못할경우, 순차적으로 suffix 로 검색할 패턴
  - {domain}.hello.svc.cluster.local -> {domain}.svc.cluster.local -> {domain}.cluster.local 정의순서대로 검색한다
- options ndots:5
  - FQDN (fully qualified domain name) 을 충족하기 위한 최소 `.` 의 개수
  - 다만 FQDN 은 domain 이 . 으로 끝날경우 FQDN 으로 인지한다 (ex. **naver.com.** 은 ndots:1 이지만 . 으로 끝나므로 FQDN)

## kube-proxy

kube-proxy 는 서비스와 (노드의) pod 을 연결한다 (POD 이 N 개인 경우 LB 의 역할도 수행)

> 서비스는 가상의 ip:port 를 할당받지만 실질적으로는 더미. 즉 아무일도 하지 않음

서비스가 생성되면 kube-apiserver 는 (모든 워커노드의) kube-proxy 에 해당 서비스의 ip:port 를 통보하고, kube-proxy 는 iptables 에 해당 서비스의 ip:port 를 등록한다
- 서비스의 ip:port 가 목적지로 packet sent
- (이미 워커노드의 kube-proxy 에 의해 갱신된 iptables 을 통해) 목적지가 해당 서비스의 matchLabel 에 해당하는 무작위 POD IP:PORT 로 치환
- 실질적으로 해당 POD 으로 dest ip:port 가 네트워크로 sent