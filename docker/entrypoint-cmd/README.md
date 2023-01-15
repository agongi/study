## ENTRYPOINT vs CMD

```
ㅁ Author: suktae.choi
ㅁ References:
- https://stackoverflow.com/questions/40902445/using-variable-interpolation-in-string-in-docker
```

## ENTRYPOINT, 

컨테이너 시작될때 수행할 스크립트를 정의합니다.

```bash
$ docker run --entrypoint="echo hello" app
```

Dockerfile 파일에서 미리 정의할수 있고 아래와 같습니다:

```dockerfile
# without shell
ENTRYPOINT ["echo hello"]
# via shell
ENTRYPOINT echo hello
```

## CMD

컨테이너 시작될때 수행할 스크립트를 정의합니다.

```bash
$ docker run app echo hello
```

Dockerfile 파일에서 미리 정의할수 있고 아래와 같습니다:

```dockerfile
# without shell
CMD ["echo hello"]
# via shell
CMD echo hello
```

## DIFF

둘의 차이는 docker run / start 로 실행하면서 args 를 넘길때의 동작이 갈립니다.

```bash
CMD echo hi

$ docker run app echo hello
```

> hello: 재정의

```bash
ENTRYPOINT echo hi

$ docker run app echo hello
```

> hi echo hello: args 로 인지

그리고 쉘을 통하지 않고 명령어 실행시, ENV 을 사용 할 수 없습니다. (ENV, ENTRYPOINT 모두 동일)

```bash
# env APP_HOME 을 interpolation 하지 못함
ENTRYPOINT ["java", "jar", "$APP_HOME", "/app.jar"]

# 이렇게 써야함
ENTRYPOINT java -jar $APP_HOME/app.jar
```

