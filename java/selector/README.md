# Selector

```
@author: suktae.choi
- https://engineering.linecorp.com/ko/blog/do-not-block-the-event-loop-part2
```

## Multi-processor

fork 해서 processor 를 N 개로 처리

## Multi-thread

threadpool 에서 처리하는 일반적인 방식

## Multiplexing

멀티플렉싱 기반 하나의 스레드에서 다중 접속을 처리하는 방법

- Selector
  - 셀렉터를 사용하면 하나의 스레드가 여러 채널을 처리할 수 있습니다
  - 하나 이상의 채널을 셀렉터에 등록하고 select() 메서드를 호출하면, 등록된 채널 중 이벤트 준비가 채널이 생길 때까지 블록됩니다 (즉, 하나의 스레드에서 여러 채널을 관리)
- Channel
  - read/write 를 같이 하는 연결단위 (기존 stream 은 input/output 구분되어 있었음)
    - FileChannel: file 연결
    - DatagramChannel: UDP 연결
    - SocketChannel: TCP 연결
    - ServerSocketChannel: TCP 연결 수신(listen) 및 수신된 serverSocketChannel 에서 `SocketChannel` 생성
- Buffer
  - read/write 데이터의 저장단위
  - 채널은 양방향 이므로 버퍼에 데이터를 쓰다가 읽어야 한다면 flip() 을 호출해서 write -> read mode 로 전환해야 합니다

```java
import java.io.IOException;
import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.SelectionKey;
import java.nio.channels.Selector;
import java.nio.channels.ServerSocketChannel;
import java.nio.channels.SocketChannel;
import java.util.Iterator;
import java.util.Set;
 
public class EchoServer {
 
    private static final String EXIT = "EXIT";
 
    public static void main(String[] args) throws IOException {
        // Create Selector
        Selector selector = Selector.open();
        // Create ServerSocketChannel
        ServerSocketChannel serverSocket = ServerSocketChannel.open();
        serverSocket.bind(new InetSocketAddress("localhost", 8080));
        serverSocket.configureBlocking(false);
        // Register ServerSocketChannel to the Selector
        serverSocket.register(selector, SelectionKey.OP_ACCEPT);
        ByteBuffer buffer = ByteBuffer.allocate(256);
 
        while (true) {
            selector.select();
            Set<SelectionKey> selectedKeys = selector.selectedKeys();
            Iterator<SelectionKey> iter = selectedKeys.iterator();
            while (iter.hasNext()) {
                SelectionKey key = iter.next();
 
                // Case) ServerSocketChannel
                if (key.isAcceptable()) {
                    register(selector, serverSocket);
                }
 
                // Case) SocketChannel
                if (key.isReadable()) {
                    answerWithEcho(buffer, key);
                }
                iter.remove();
            }
        }
    }
 
    private static void answerWithEcho(ByteBuffer buffer, SelectionKey key)
            throws IOException {
        SocketChannel client = (SocketChannel) key.channel();
        client.read(buffer);
        if (new String(buffer.array()).trim().equals(EXIT)) {
            client.close();
            System.out.println("closed client.");
        }
 
        buffer.flip();
        client.write(buffer);
        buffer.clear();
    }
 
    private static void register(Selector selector, ServerSocketChannel serverSocket)
            throws IOException {
        SocketChannel client = serverSocket.accept();
        client.configureBlocking(false);
        client.register(selector, SelectionKey.OP_READ);
        System.out.println("connected new client.");
    }
}
```

## Reactor

하나의 스레드에서 다중 접속을 처리하는 디자인 패턴

- Reactor
  - 이벤트 루프
  - 처리할 이벤트 발생시 handler 에게 dispatch
- Handler
  - 이벤트를 받아 필요한 비즈니스 로직 수행

```java
public class Reactor implements Runnable {
    final Selector selector;
    final ServerSocketChannel serverSocketChannel;

    Reactor(int port) throws IOException {
        selector = Selector.open();

        serverSocketChannel = ServerSocketChannel.open();
        serverSocketChannel.socket().bind(new InetSocketAddress(port));
        serverSocketChannel.configureBlocking(false);
        SelectionKey selectionKey = serverSocketChannel.register(selector, SelectionKey.OP_ACCEPT);

        // Attach a handler to handle when an event occurs in ServerSocketChannel.
        selectionKey.attach(new AcceptHandler(selector, serverSocketChannel));
    }

    public void run() {
        try {
            while (true) {
                selector.select();
                Set<SelectionKey> selected = selector.selectedKeys();
                for (SelectionKey selectionKey : selected) {
                    dispatch(selectionKey);
                }
                selected.clear();
            }
        } catch (IOException ex) {
            ex.printStackTrace();
        }
    }

    void dispatch(SelectionKey selectionKey) {
        Handler handler = (Handler) selectionKey.attachment();
        handler.handle();
    }
}
```

```java
public interface Handler {
    void handle();
}

class AcceptHandler implements Handler {
    final Selector selector;
    final ServerSocketChannel serverSocketChannel;

    AcceptHandler(Selector selector, ServerSocketChannel serverSocketChannel) {
        this.selector = selector;
        this.serverSocketChannel = serverSocketChannel;
    }

    @Override
    public void handle() {
        try {
            final SocketChannel socketChannel = serverSocketChannel.accept();
            if (socketChannel != null) {
                new EchoHandler(selector, socketChannel);
            }
        } catch (IOException ex) {
            ex.printStackTrace();
        }
    }
}

public class EchoHandler implements Handler {
    static final int READING = 0, SENDING = 1;

    final SocketChannel socketChannel;
    final SelectionKey selectionKey;
    final ByteBuffer buffer = ByteBuffer.allocate(256);
    int state = READING;

    EchoHandler(Selector selector, SocketChannel socketChannel) throws IOException {
        this.socketChannel = socketChannel;
        this.socketChannel.configureBlocking(false);
        // Attach a handler to handle when an event occurs in SocketChannel.
        selectionKey = this.socketChannel.register(selector, SelectionKey.OP_READ);
        selectionKey.attach(this);
        selector.wakeup();
    }

    @Override
    public void handle() {
        try {
            if (state == READING) {
                read();
            } else if (state == SENDING) {
                send();
            }
        } catch (IOException ex) {
            ex.printStackTrace();
        }
    }

    void read() throws IOException {
        int readCount = socketChannel.read(buffer);
        if (readCount > 0) {
            buffer.flip();
        }
        selectionKey.interestOps(SelectionKey.OP_WRITE);
        state = SENDING;
    }

    void send() throws IOException {
        socketChannel.write(buffer);
        buffer.clear();
        selectionKey.interestOps(SelectionKey.OP_READ);
        state = READING;
    }
}
```

### Event loop

- 이벤트가 발생하기를 대기
- 이벤트가 발생하면 처리할 수 있는 핸들러에 이벤트를 디스패치 합니다
- 핸들러에서 이벤트를 처리합니다
  - 이때 handler 는 이벤트 루프를 실행하고 있는 main-thread 이므로 block 이 발생하면 해제될 때까지 1-3단계를 반복할 수 없습니다
  - CPU intensive 작업이거나 blocking 이 꼭 필요하다면 -> 별도 thread 를 생성해서 비동기 처리 해야 합니다 
- 다시 1-3 단계를 반복 합니다

## 다른 구현체

- Netty
- Node.js
- Redis
