## ARG vs ENV

```
ㅁ Author: suktae.choi
ㅁ References:
- https://stackoverflow.com/questions/40902445/using-variable-interpolation-in-string-in-docker
```

## ARG

build args 이다. 빌드시점에 전달되어 사용 가능하다.

```bash
$ docker build Dockerfile --build-arg APP_VERSION=1.0.1
```

Dockerfile 에서는 아래와 같이 사용한다.

```dockerfile
# --build-arg 로 전달되면 덮어쓰고, 기본값은: 1.0.0 으로 설정
ARG APP_VERSION=1.0.0
# 빌드시점에 전달된 arg 를 env 로 설정 (optional)
ENV APP_VERSION $APP_VERSION

RUN ${APP_VERSION}
```

빌드시점에만 사용되므로 (즉 dockerfile 내부에서만) ENTRYPOINT, CMD 에서는 인식되지 않는다.

```dockerfile
ARG APP_VERSION=1.0.0
ENTRYPOINT java -jar /workspace/app-${APP_VERSION}.jar
```

> 결과: java -jar /workspace/app-.jar 

## ENV

환경변수이다. 빌드 및 런타임에 사용 가능하다.

```bash
$ docker run -e APP_VERSION=1.0.1 app
```

Dockerfile 에서는 아래와 같이 사용한다.

```dockerfile
# -e 로 전달되면 덮어쓰고, 기본값은: 1.0.0 으로 설정
ENV APP_VERSION 1.0.0
# 빌드시점에 전달된 env 설정
RUN ${APP_VERSION}
```

빌드 & 런타임에 사용되므로, ENTRYPOINT/CMD 에서 사용가능하다.





셸 없이 바로 실행하기

ENTRYPOINT ["java", "-jar", "${USER_HOME}/app.jar"] x



셸(/bin/sh)로 명령 실행하기

\- ARG 는 빌드타임

\- ENG 는 빌드+런타임 (env 에 정의됨)



ARG USER_HOME=/home/user

ENTRYPOINT java -jar ${USER_HOME}/ROOT.jar x



ENV USER_HOME=/home/user

ENTRYPOINT java -jar ${USER_HOME}/ROOT.jar o