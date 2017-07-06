## Garbage Collection - Concept

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.02.13
ㅁ References:
 - http://javarevisited.blogspot.sg/2011/04/garbage-collection-in-java.html
 - http://javarevisited.blogspot.kr/2011/05/java-heap-space-memory-size-jvm.html
 - http://d2.naver.com/helloworld/1329
 - http://d2.naver.com/helloworld/329631

 - http://d2.naver.com/helloworld/1134732
 - http://d2.naver.com/helloworld/6043
 - http://d2.naver.com/helloworld/37111
 - http://d2.naver.com/helloworld/4717
 - http://d2.naver.com/helloworld/184615

 - https://prezi.com/bwba2m2xhive/java-garbage-collection_/
 - https://yckwon2nd.blogspot.kr/2014/04/garbage-collection.html
```

### [How does it work](http://d2.naver.com/helloworld/1329)
<img src="https://github.com/agongi/study/blob/master/java/garbage-collection-1-concept/images/image2014-4-3%2011-20-1.png" width="75%">

#### Young Generation
Eden, From (Survivor-0), To (Survivor-1) 영역으로 구분된다.<br>
새롭게 생성한 객체는 여기에 위치한다. 대부분의 객체가 금방 접근 불가능 상태가 되기 때문에 매우 많은 객체가 Young 영역에 생성되었다가 사라진다. 이 영역에서 객체가 사라질때 `Minor GC`가 발생한다고 말한다.

- 새로 생성한 객체는 Eden 영역에 위치한다
- Eden 영역이 가득 차면, Minor GC 가 발생한다
  - Minor GC 가 발생하면, New 영역 전체에 Mark-Sweep 이 이뤄진다
  - Reference 가 있는 객체는 현재 사용되는 Survivor 영역으로 이동한다
- 다시 Minor GC 가 발생하면
- 살아남은 객체는 다른 Survivor 영역으로 이동한다 - Aging
  - Eden 에서 Survivor 로 이동할 객체도, 이동할 Survivor 로 할당된다
- 이 과정을 반복
- Threshold 이상의 Age 객체는 Old 영역으로 이동하게 된다 - Promotion

> Survivor 영역 중 하나는 반드시 비어 있는 상태로 남아 있어야 한다

> 객체의 크기가 Eden 보다 크면 바로 Old 영역으로 할당된다

#### Old Generation
Young 영역에서 살아남은 객체가 여기로 복사된다. 대부분 Young 영역보다 크게 할당하며, 크기가 큰 만큼 Young 영역보다 GC는 적게 발생한다.

이 영역에서 객체가 사라질 때 `Full GC`가 발생한다고 말한다. 그리고 Full GC가 발생하면 통상적으로 말하는, `STW(stop-the-world)`가 발생하여 application thread가 hang 멈춘다. 대개의 경우 GC 튜닝이란 이 stop-the-world 시간을 줄이는 것이다.

#### Permanent Generation
Since it is a separated region, it is not considered as a part of the Java Heap space. Objects in this space are relatively permanent. Class definitions are stored here, as are `static instances`, `string pool` and `meta-data`. Rarely `Full GC` also comes over here to clean up this area.

### Changes in JDK 8
JDK 8 에서 변경된 점
- Perm 사라짐 (MetaSpace 영역으로 바뀜 - native memory)
- PermGen 영역이 삭제되어 heap 영역에서 사용할 수 있는 메모리가 늘어났다.
- PermGen 영역을 삭제하기 위해 존재했던 여러 복잡한 코드들이 삭제
- PermGen영역을 스캔 하기 위해 소모되었던 시간이 감소되어 GC 성능이 향상 되었다.

정리하면 JDK7 까지는
- new / survive / old / perm /  native  로 구분했다면

JDK8 에서는
- new / survive / old / metaSpace 로 아키텍쳐가 변경되었고

기존의 perm에 저장되어 문제를 유발하던 static Object 는 heap으로 옮겨서 GC 대상이 되도록함

### GC Types
#### Serial GC

#### Parallel GC
기본적으로 다른 CMS GC 보다 느리다.

> Parallel GC 방식에서는 Full GC가 수행될 때마다 Compaction 작업을 진행하기 때문에 시간이 많이 소요된다. 하지만, Full GC가 수행된 이후에는 메모리를 연속적으로 지정할 수 있어 메모리를 더 빠르게 할당할 수 있다.

#### Parallel Old GC

#### CMS
기본적으로 다른 Parallel GC 보다 빠르다.

> Full GC 발생시, Compaction을 을 진행하지 않으므로 일반적으로 속도가 빠르다. 다만 Concurrent mode failure 발생시 (메모리 단편화로 인한 큰 사이즈의 memory load fail) Compaction을 진행하는데, 시간이 다른 Parallel GC보다 더 오래 소요된다.

#### G1
지금까지 설명한 어떤 GC 방식보다도 빠르다. (Since JDK 1.7)

> 기존의 Young, Old 영역 방식이 아닌, 바둑판 형식을 이용하여 각 영역에 객체를 할당한다. 그러다가, 해당 영역이 꽉 차면 다른 영역에서 객체를 할당하고 GC를 실행한다. 즉, 지금까지 설명한 Young의 세가지 영역에서 데이터가 Old 영역으로 이동하는 단계가 사라진 GC 방식이라고 이해하면 된다
