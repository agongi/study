## Garbage Collection - Concept

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.02.13
ㅁ References:
 - http://www.oracle.com/technetwork/tutorials/tutorials-1876574.html
 - http://javarevisited.blogspot.sg/2011/04/garbage-collection-in-java.html
 - http://javarevisited.blogspot.kr/2011/05/java-heap-space-memory-size-jvm.html

 - http://d2.naver.com/helloworld/1329
 - http://d2.naver.com/helloworld/329631
 - http://d2.naver.com/helloworld/1134732
 - http://d2.naver.com/helloworld/6043
 - http://d2.naver.com/helloworld/37111
 - http://d2.naver.com/helloworld/4717
 - http://d2.naver.com/helloworld/184615

 - https://prezi.com/bwba2m2xhive/java-garbage-collection
 - https://yckwon2nd.blogspot.kr/2014/04/garbage-collection.html
 - http://imp51.tistory.com/entry/G1-GC-Garbage-First-Garbage-Collector-Tuning
 - http://initproc.tistory.com/entry/G1-Garbage-Collection
```

### CMS (Serial, Parallel) GC
<img src="images/image2014-4-3%2011-20-1.png" width="75%">

#### Young Generation
Eden, From (Survivor-0), To (Survivor-1) 영역으로 구분된다.<br>
새롭게 생성한 객체는 여기에 위치한다. 대부분의 객체가 금방 접근 불가능 상태가 되기 때문에 매우 많은 객체가 Young 영역에 생성되었다가 사라진다.

- 새로 생성한 객체는 Eden 영역에 위치한다
- Eden 영역이 가득 차면 `Minor GC` 가 발생한다
  - Minor GC 가 발생하면 New 영역 전체에 Mark-Sweep 이 이뤄진다
  - Reference 가 있는 객체는 현재 사용되는 Survivor 영역으로 이동한다
- 다시 Minor GC 가 발생하면
- 살아남은 객체는 다른 Survivor 영역으로 이동한다 - `Aging`
  - Eden 에서 Survivor 로 이동할 객체도, 이동할 Survivor 로 할당된다
- 이 과정을 반복
- Threshold 이상의 Age 객체는 Old 영역으로 이동하게 된다 - `Promotion`

> Survivor 영역 중 하나는 반드시 비어 있는 상태로 남아 있어야 한다

> 객체의 크기가 Eden 보다 크면 바로 Old 영역으로 할당된다

#### Old Generation
Young 영역에서 살아남은 객체가 여기로 복사된다. 대부분 Young 영역보다 크게 할당하며, 크기가 큰 만큼 Young 영역보다 GC는 적게 발생한다.

Old 영역이 가득 차면 `Full GC`가 발생한다. 통상적으로 말하는 `STW(stop-the-world)`가 발생하여 application thread가 hang 멈춘다.

> GC 튜닝이란 stop-the-world 시간을 줄이는 것이다

#### Permanent Generation
Since it is a separated region, it is not considered as a part of the Java Heap space. Objects in this space are relatively permanent.

Class definitions are stored here, as are `static instances`, `string pool` and `meta-data`. Rarely `Full GC` also comes over here to clean up this area.

### G1 GC
<img src="images/Screen%20Shot%202017-08-15%20at%2001.02.19.gif" width="75%">

G1에는 전통적인 type(Eden, Survivor 및 Old Generation) 외 Humongous Region, Available / Unused Region 존재
- 전체 heap을 Region이라는 영역으로 분활하여 관리하며, Region이 곧 Eden, Survivor 또는 Old일 수 있다
- Region의 목표 수치는 2048으로 분활된다. 즉, 8G의 Heap이라면 하나의 Region의 크기는 4MB (8192MB/2048 = 4MB)
- Young generation memory는 non-contiguous region으로 구성되며, 이는 필요 시 크기 조정을 용이하게 함

> 객체 크기가 Region의 1/2보다 큰 경우, humongous 영역으로 관리

#### Young Generation
<img src="images/Screen%20Shot%202017-08-15%20at%2002.02.19.gif" width="75%">

- Evacuation Pauses
  - 이 단계가 `Minor GC` 이다
    - 첫 Minor GC 는 Heap 이 일정 용량 이상으로 점유시 수행된다
    - 그 후에는 사용자가 지정한 pause duration과 per-byte copying Cost에 의해 결정
  - 전체 Eden, Survive regions 에서 대상을 선택한다
    - Region 선택 기준은 사용자가 옵션으로 지정한 pause time과 G1 GC 내부에서 사용하는 휴리스틱 알고리즘
    - Region 은 Live Data Counting 정보를 가지고 있다. 이 정보를 바탕으로 region 영역이 먼저 Garbage 되어야 하는지 결정된다 (그래서 이름이 Garbage First)
  - Reference 가 있는 객체는 (Live object) 다음 Phase 로 이동한다
    - `STW(stop-the-world)` 구간이며 병렬로 처리한다
    - Eden > Survivor
    - Survivor from <> to  - `Aging`
    - Survivor > Old - `Promotion`

#### Old Generation
`-XX:InitiatingHeapOccupancyPercent` (IHOP) 에서 정한 수치가 넘어가면 동작한다. 모든 phase 가 병렬로 처리된다

- Concurrent Marking Cycle Phases
  - initial mark
    - `Minor GC` 때 수행, `STW(stop-the-world)` 구간
    - Initial marking of live object along with Minor GC
  - Concurrent marking
    - Mark empty region
    - 이 때부터 Evacuation Pauses와 동시에 실행 가능
  - Remark
    - `STW(stop-the-world)` 구간
    - Empty regions are removed and reclaimed. Region liveness is now calculated for all regions
  - Cleanup/Copying
    - `STW(stop-the-world)` 구간
    - G1 selects the regions with the lowest "liveness", those regions which can be collected the fastest

> Minor GC 가 발생할때 병렬적으로 Old region 에 대해 미리 mark 해놓고, Next GC에 liveness (빨리 처리가능한) 한 region 이 같이 정리되는 구조

> 조금씩 조금씩 Minor GC 때 Old region 이 같이 정리되는 개념이다

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
