# Docker Compose
```
https://www.44bits.io/ko/post/almost-perfect-development-environment-with-docker-and-docker-compose
```

- Dockerfile
    - 1개의 이미지 생성 정의
- docker-compose.yml
    - N개의 이미지 생성 정의
    - 컨테이너 간 실행순서나 의존성을 관리하며 여러개의 컨테이너를 동시에 실행

```yaml
# docker-compose.yml
version: "3"
services:
  spring-app: # container name
    build:  # if images not exists
      context: .
      dockerfile: ./docker/Dockerfile
  nginx:
    image: nginx:20200320_145400  # if images exists
    ports:
      - "80:80"
      - "443:443"
    volumes:	# 필요시 fuse 가능
      - /docker/nginx/conf:/usr/local/etc/nginx/conf
```
```bash
$ docker-compose up
```

