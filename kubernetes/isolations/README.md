# Isolations
```
https://itnext.io/chroot-cgroups-and-namespaces-an-overview-37124d995e3d
```

container 는 격리된 환경에서 실행되는 `process` 입니다.

동일 Node 에서 실행되는 Pod (Container) 의 적절한 격리/제한을 통해 안정적으로 운영할 수 있습니다.

## chroot
루트 디렉토리를 변경해서 격리

<img src="1.png" width="50%">

## cgroup
(사용할 수 있는) 리소스 격리
- cpu
- memory
- netowrk
- disk

## namespace
`unshare` cmd 를 통해 system-call 로 namespace 를 격리한다

```bash
# mount namespace 를 격리하는 cmd
$ unshare -m /bin/bash
```

### mount namespace + overlayFS
<img src="2.png" width="50%">

### pid namespace
<img src="3.png" width="50%">

### ipc namespace 를 공유하는 단위
- shared memory
- semaphore
- POSIX message queue

### network namespace
<img src="4.png" width="50%">

### IPC (inter process communication) namespace
- semaphore
- POSIX queue

### UTS (unix time-sharing) namespace
- hostname
- domainname 

### user namespace
uid 의 격리레벨 단위
- pid, network namespace 격리와의 호환성
- uid mapping 을 지원하지 않는 driver 와의 호환성

의 문제가 있어, docker container & kubernetes 는 user namespace 를 격리하지 않는다