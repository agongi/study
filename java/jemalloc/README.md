# Jemalloc

```
https://velog.io/@dbwogml15/%ED%86%A0%EC%8A%A4%E3%85%A3SLASH-22-Java-Native-Memory-Leak-%EC%9B%90%EC%9D%B8%EC%9D%84-%EC%B0%BE%EC%95%84%EC%84%9C
https://medium.com/daangn/memory-allocator-for-mongodb-1953f9cee06c
```

JVM 수준에서 관측할 수 없는 Native 메모리 사용량의 지속적인 증가 현상

## Native Memory
```
-Xms6g
-Xmx6g
-XX:MaxDirectMemorySize=3g
-XX:NativeMemoryTracking=summary
```

<img width="50%" alt="image" src="1.png">

- Heap JVM이 GC로 관리
- Native OS가 직접 관리

## JIT Compiler
```
nterpreter → C1(no profiling) → C1(with profiling) → C2
    0               1                    2-3            4
```
```
# C1 Compiler 사용선언 (C2 는 -XX:TieredStopAtLevel=4 사용)
 
-XX:TieredStopAtLevel=1
```

C2 (-server) 설정은 Native 영역에 HotSpot VM 기반 JIT Compiler 는 많이 실행된 method 가 CodeCache 에 올라가고 (== Native Memory)
그 영역이 지속적으로 누적되면서 (free 되지 않음) RSS 점유

> JIT 과 반대되는 개념은 GraalVM 의 AOT (Ahead Of Time) Compiler

## Jemalloc
glibc (표준 C/C++ 라이브러리) 를 사용하는 OS 의 메모리 할당자 (== malloc) 메모리 미회수로 인한 파편화 문제

- `-XX:TrimNativeHeapInterval` 옵션을 통해 주기적으로 Native 영역을 정리할 수 있습니다.
- 메모리 할당자 변경
  - malloc -> `jemalloc` (redis, mysql 등에서 사용)

```dockerfile
yum install -y jemalloc

# jemalloc 적용
ENV LD_PRELOAD=/usr/lib64/libjemalloc.so.2

# 적용 확인
$ cat /proc/{PID}/maps | grep jemalloc
```

## Malloc vs Jemalloc
https://sourceware.org/glibc/wiki/MallocInternals#Free_Algorithm 에 명세 된것처럼 malloc 은 회수로 마킹하지만 즉시 회수 하지 않음
(추후 재사용을 위함) 그로인해 회수되지 않은 메모리가 누적되어 사용중 메모리 RSS (Resident Set Size)가 지속 증가

```
The free() call marks a chunk of memory as "free to be reused" by the application,
but from the operating system's point of view, the memory still "belongs" to the application.
```

Native Memory 를 적극적으로 사용하는 Netty 및 JDK ByteBuffer.allocateDirect(); 등을 적극적으로 사용하면 누적되는 구조
그리고 Netty 의 Zero Copy (== DMA) 는 `Heap 복사없이 Native 를 직접 사용하므로 free() 시점을 보장하지 못함`

- Non-Zero Copy
  - 성능: Heap 복사비용 발생
  - 메모리: Native 는 JNI 의 임시 영역만 잠시 사용하고 즉시 해제
- Zero Copy
  - 성능: Heap 복사비용 미발생
  - 메모리: JVM 의 범위를 벗어나므로 OS 에서 해제하도록 위임
