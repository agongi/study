# Isolations
```
https://itnext.io/chroot-cgroups-and-namespaces-an-overview-37124d995e3d
```

## Docker vs VM
Docker 는 `container engine` 을 통해 동일 OS 위에서 격리된 환경으로 (cgroup, namespace) 실행됩니다.

> rancher desktop 등으로 생각하면 됨

VM 은 `hypervisor`를 통해 하드웨어 레벨에서부터 구분되어 개별 OS 를 실행됩니다. (os)

> 기존 virtualbox 생각하면 됨

| 항목 | VM (Virtual Machine) | Docker (Container) |
| :--- | :--- | :--- |
| **구조** | 하드웨어 위에 **Hypervisor** 실행, 그 위에 **Guest OS** 설치 | Host OS 위에 **Container Engine** 실행, **OS 커널 공유** |
| **격리 수준** | **완전한 격리** (OS 레벨) | **프로세스 수준 격리** (Namespaces, Cgroups) |
| **오버헤드** | **높음** (각 VM마다 OS 전체 포함) | **낮음** (App 실행에 필요한 라이브러리/바이너리만 포함) |
| **부팅 속도** | **느림** (수 분) | **빠름** (수 초) |
| **리소스 사용량** | **많음** (GB 단위) | **적음** (MB 단위) |
| **배포** | OS를 포함한 이미지 전체를 배포 | App만 이미지로 만들어 배포 |

격리수준은 VM 이 높지만, OS 를 개별로 포함하므로 빠르고/가볍게 실행되지 않습니다. 그래서 Docker 을 MSA 에서 사용합니다.

## cgroup
`사용할 수 있는` 리소스 격리
- cpu
- memory
- network
- disk

## namespace
`접근할 수 있는` 리소스 격리
- pid
- net (네트워크)
- mount (파일시스템)
- user (root 로 보이지만 Node 의 root 는 아님)

## chroot
루트 디렉토리를 변경해서 격리

<img src="1.png" width="50%">

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