## TCP (Transmission Control Protocol)
TCP provides an effective abstraction of a reliable network running over an unreliable channel, hiding most of the complexity of network communication from our applications:
 - Retransmission of lost data
 - In-order delivery
 - Congestion control and avoidance
 - Data integrity

> TCP guarantee packet is transferred and arrive in the same order to the client with following technics.

```
@author: suktae.choi
- http://chimera.labs.oreilly.com/books/1230000000545/ch02.html
- https://en.wikipedia.org/wiki/Transmission_Control_Protocol
- http://tech.kakao.com/2016/04/21/closewait-timewait/
- http://d2.naver.com/helloworld/47667
```

#### Index
- [TCP Fast Open](https://github.com/agongi/study/tree/master/tcp/tcp-fast-open/)
- [TCP Connection Migration](https://github.com/agongi/study/tree/master/tcp/tcp-connection-migration/)
- [TCP Forward Error Correction](https://github.com/agongi/study/tree/master/tcp/tcp-forward-error-correction/)
- [TCP Head-Of-Line](https://github.com/agongi/study/tree/master/tcp/tcp-head-of-line/)

### 3-way-handshake
Origin 3-way-handshaking in TCP connection.

<img src="images/18338404268_f693b065d4_o.png">

> CLOSE_WAIT 종료 : 커널 옵션으로 타임아웃 조절이 가능한 FIN_WAIT이나 재사용이 가능한 TIME_WAIT과 달리, CLOSE_WAIT는 포트를 잡고 있는 프로세스의 종료 또는 네트워크 재시작 외에는 제거할 방법이 없습니다. 즉, 로컬 어플리케이션이 정상적으로 close()를 요청하는 것이 가장 좋은 방법입니다.

accept() 이후 서버는 listen() 중인 소켓과 별도로 accept() 소켓을 추가로 생성해 클라이언트를 할당합니다. 서버가 클라이언트를 관리하는 방식은 크게 4가지로 구분할 수 있습니다.

 - 요청 당 프로세스 할당, fork()로 자식 프로세스를 만들어 클라이언트를 담당합니다. 전통적인 blocking I/O 방식입니다.
 - 요청 당 쓰레드 할당. 위 예제에서 사용한 방식으로 pthread_create()를 통해 쓰레드를 생성, 클라이언트를 담당합니다. 마찬가지로 blocking I/O 방식입니다.
 - 쓰레드풀을 구성해 각각의 쓰레드가 여러 커넥션을 asynchronous I/O 방식으로 담당합니다.
 - 쓰레드풀을 구성해 각각의 쓰레드가 여러 커넥션을 select(), poll(), nonblocking I/O 같은 이벤트 기반 방식으로 담당합니다.

### Flow Control (Host-To-Host)
 - rwnd (window size)

### Congestion Control (Host-To-Network)
 - Slow Start
 - Congestion Avoidance
 - Fast Retransmission
 - Fast Recovery
