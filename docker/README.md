# Docker
```
https://docs.docker.com/reference/
https://pyrasis.com/jHLsAlwaysUpToDateDocker
https://velog.io/@choidongkuen/%EC%84%9C%EB%B2%84-Docker-Network-%EC%97%90-%EB%8C%80%ED%95%B4
```

## 1. 핵심 개념
Docker는 애플리케이션을 신속하게 구축, 테스트 및 배포할 수 있는 컨테이너 기반의 오픈소스 가상화 플랫폼입니다. OS 수준의 가상화 기술을 사용하여 호스트 시스템의 커널을 공유하면서도, 프로세스, 네트워크, 파일 시스템 등은 독립적으로 격리된 환경에서 애플리케이션을 실행합니다.

- **격리 (Isolation)**: Linux OS 의 `namespaces`, `cgroups` 커널기능을 사용해 격리된 환경을 제공합니다.
  - **`namespaces`**: 컨테이너가 접근할 수 있는 `논리적인 리소스를 격리`합니다:
    - pid: 프로세스 격리
    - net: 네트워크 인터페이스, IP 주소 테이블, 라우팅 테이블 등 네트워크 관리
    - ipc: 프로세스 간 통신(IPC) 객체에 대한 접근 격리
    - mnt: 파일 시스템 마운트 포인트 격리
  - **`cgroups` (Control Groups)**: 컨테이너가 사용할 수 있는 `물리적인 리소스를 제한`합니다:
    - cpu
    - memory
- **공유 (Sharing)**: 호스트 OS의 커널을 모든 컨테이너가 공유합니다. 이로 인해 VM(가상 머신) 방식보다 훨씬 가볍고 빠르게 동작합니다.

<img src="1.png" width="50%">

## 2. 구성요소
- **Docker Engine (Server)**
  - `dockerd`라는 데몬 프로세스를 통해 관리됩니다.
  - 이미지, 컨테이너, 네트워크, 볼륨 등 Docker 객체를 생성하고 관리하는 핵심 구성요소입니다.
- **Docker CLI (Client)**
  - 사용자가 Docker와 상호작용하기 위해 사용하는 커맨드 라인 인터페이스입니다.
  - `docker build`, `docker run` 등의 명령어를 입력하면, Docker CLI는 Docker 데몬에게 API 요청을 보내 작업을 수행합니다.
- **Docker Compose**
  - **여러 컨테이너로 구성된 애플리케이션을 정의하고 실행**하는 도구입니다.
  - `docker-compose.yml` YAML 파일을 통해 다중 컨테이너 애플리케이션의 서비스, 네트워크, 볼륨을 선언적으로 구성하고, 단일 명령어로 전체 애플리케이션 스택을 시작하거나 중지할 수 있습니다.

## 3. Dockerfile과 이미지 최적화
이미지를 생성하기 위한 명세를 정의하는 파일입니다. Dockerfile을 통해 애플리케이션 환경을 코드로 관리(Infrastructure as Code)할 수 있습니다.

```dockerfile
# Dockerfile
# 1. Base Image: 구체적인 버전의 공식 이미지를 사용한다.
FROM nginx:1.21.6-alpine

# 2. Build Context 최소화: .dockerignore 파일을 사용하여 불필요한 파일이 빌드 컨텍스트에 포함되지 않도록 한다.

# 3. Non-Root User 사용: 보안을 위해 root가 아닌 별도의 사용자를 생성하고 전환한다.
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
USER appuser

# 4. Layer 최소화: RUN, COPY, ADD 명령어는 각각 레이어를 생성하므로, 연관된 명령어는 '&&'를 사용해 묶어준다.
WORKDIR /usr/local/etc/nginx/conf
COPY --chown=appuser:appgroup /docker/nginx/conf .

# 5. Multi-stage Build 활용: 빌드 환경과 런타임 환경을 분리하여 최종 이미지 크기를 줄이고 보안을 강화한다.
# (아래 Advanced 섹션 참고)

EXPOSE 80 443
CMD ["nginx", "-g", "daemon off;"]
```

- **이미지 빌드 및 실행**

```bash
# 이미지 빌드 (태그 지정)
$ docker build --tag my-nginx:1.0 .

# 컨테이너 실행
$ docker run -d -p 80:80 --name webserver my-nginx:1.0
```

## 4. 데이터 영속성 (Volumes)
컨테이너는 삭제될 때 내부의 데이터도 함께 사라지는 비영속적인(stateless) 특징을 가집니다. 데이터를 영구적으로 저장하기 위해 Volume을 사용합니다.

- **Volume**: Docker가 관리하는 호스트의 특정 영역(`_data`)에 데이터를 저장합니다. 컨테이너와 독립적인 생명주기를 가지며, 여러 컨테이너 간에 안전하게 데이터를 공유할 수 있습니다. 가장 권장되는 방식입니다.
  - `docker volume create my-volume`
  - `docker run -v my-volume:/app/data ...`
- **Bind Mount**: 호스트 머신의 파일이나 디렉토리를 컨테이너에 직접 마운트합니다. 호스트의 파일 시스템에 의존하므로 경로 관리에 주의가 필요합니다.
  - `docker run -v /path/on/host:/app/data ...`

<img src="2.png" width="50%">

## 5. 네트워크
컨테이너가 외부 및 다른 컨테이너와 통신할 수 있도록 네트워크 환경을 제공합니다.

- **`bridge` (기본값)**:
  - 컨테이너는 Docker가 생성한 가상 브릿지(`docker0`)에 연결됩니다.
  - 동일한 호스트 내의 컨테이너들은 서로 통신할 수 있지만, 외부와 통신하려면 포트 포워딩(`-p` 옵션)이 필요합니다.
  - `호스트의 veth* <--> 컨테이너의 eth0` 인터페이스가 페어로 연결됩니다.
- **`host`**:
  - 컨테이너가 호스트의 네트워크 스택을 그대로 사용합니다.
  - 별도의 네트워크 격리 없이 호스트와 동일한 IP를 가지므로 네트워크 성능은 가장 좋지만, 보안적으로는 취약할 수 있습니다.
- **`overlay`**:
  - 여러 Docker 호스트에 걸쳐 분산된 컨테이너들 간의 통신을 가능하게 하는 오버레이 네트워크를 생성합니다.
  - Docker Swarm이나 Kubernetes와 같은 컨테이너 오케스트레이션 환경에서 주로 사용됩니다.
- **`none`**:
  - 컨테이너에 네트워크 인터페이스를 할당하지 않습니다. 외부와 통신이 불가능한 격리된 환경이 필요할 때 사용됩니다.

<img src="2-2.png" width="50%">

## 6. 컨테이너 오케스트레이션 (Container Orchestration)
수십, 수백 개의 컨테이너를 프로덕션 환경에서 안정적으로 관리하고 운영하기 위해 컨테이너 오케스트레이션 도구가 필요합니다.

- **필요성**:
  - **가용성(High Availability)**: 특정 컨테이너나 노드에 장애가 발생했을 때 자동으로 복구하고 서비스를 유지합니다.
  - **확장성(Scalability)**: 트래픽 부하에 따라 컨테이너 수를 동적으로 조절(Auto-scaling)합니다.
  - **서비스 디스커버리 및 로드 밸런싱**: 여러 컨테이너에 걸쳐 네트워크 트래픽을 분산하고, 컨테이너가 서로를 찾을 수 있도록 지원합니다.
- **대표적인 도구**:
  - **Kubernetes (K8s)**: 현재 컨테이너 오케스트레이션의 사실상 표준(De facto standard)입니다.
  - **Docker Swarm**: Docker에서 자체적으로 제공하는 오케스트레이션 도구로, 사용법이 비교적 간단합니다.

## 7. 고급 주제 및 모범 사례 (Advanced)
### Multi-stage builds
빌드 단계와 런타임 단계를 분리하여 최종 이미지의 크기를 최적화하고, 빌드에만 필요했던 의존성이나 도구를 최종 이미지에서 제외하여 보안을 강화하는 기법입니다.

```dockerfile
# 1. Build Stage
FROM golang:1.17 AS builder
WORKDIR /go/src/app
COPY . .
RUN go build -o myapp

# 2. Runtime Stage
FROM alpine:latest
WORKDIR /root/
COPY --from=builder /go/src/app/myapp .
CMD ["./myapp"]
```
<img src="3.png" width="50%">

### RUN 명령어 최적화
`RUN` 명령어는 실행될 때마다 새로운 이미지 레이어를 생성하고 캐시합니다. 패키지 설치 시 `update`와 `install`을 하나의 `RUN` 명령어로 묶어야 캐시 문제를 피하고 최신 버전의 패키지를 설치할 수 있습니다.

- **잘못된 예**: `update`가 캐시되어 `htop`이 이전 패키지 목록에서 설치될 수 있음
  ```dockerfile
  FROM rhel:8.10
  RUN yum update -y
  RUN yum install -y curl htop
  ```
- **올바른 예**: `RUN` 명령어를 하나로 묶어 항상 `update`와 `install`이 함께 실행되도록 함
  ```dockerfile
  FROM rhel:8.10
  RUN yum update -y && yum install -y curl htop
  ```