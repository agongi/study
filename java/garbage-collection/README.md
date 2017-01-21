## Garbage Collection
Java fundamental of Garbage Collection(GC).

>###### GC finds garbage in heap and removes it for returning memory into heap again.

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

 - http://yckwon2nd.blogspot.kr/2014/04/garbage-collection.html?m=1
```

#### [1. How Garbage Collection works](http://d2.naver.com/helloworld/1329)
<img src="https://github.com/agongi/study/blob/master/java/garbage-collection/images/Screen%20Shot%202016-02-28%20at%2016.51.10.png" width="50%">

###### 1.1. Young Generation
Eden, From (Survivor-0), To (Survivor-1) 영역으로 구분된다.<br>
새롭게 생성한 객체는 여기에 위치한다. 대부분의 객체가 금방 접근 불가능 상태가 되기 때문에 매우 많은 객체가 Young 영역에 생성되었다가 사라진다. 이 영역에서 객체가 사라질때 `Minor GC`가 발생한다고 말한다.

 - 새로 생성한 대부분의 객체는 Eden 영역에 위치한다.
 - Eden 영역에서 GC가 한 번 발생한 후 살아남은 객체는 Survivor 영역 중 하나로 이동된다.
 - Eden 영역에서 GC가 발생하면 이미 살아남은 객체가 존재하는 Survivor 영역으로 객체가 계속 쌓인다.
 - 하나의 Survivor 영역이 가득 차게 되면 그 중에서 살아남은 객체를 다른 Survivor 영역으로 이동한다. 그리고 가득 찬 Survivor 영역은 아무 데이터도 없는 상태로 된다.
 - 이 과정을 반복하다가 계속해서 살아남아 있는 객체는 Old 영역으로 이동하게 된다.

> `Survivor 영역 중 하나는 반드시 비어 있는 상태로 남아 있어야 한다`. 만약 두 Survivor 영역에 모두 데이터가 존재하거나, 두 영역 모두 사용량이 0이라면 여러분의 시스템은 정상적인 상황이 아니라고 생각하면 된다.

###### 1.2. Old Generation
Young 영역에서 살아남은 객체가 여기로 복사된다. 대부분 Young 영역보다 크게 할당하며, 크기가 큰 만큼 Young 영역보다 GC는 적게 발생한다. (-XX:NewRatio=2 or 3 or 4)<br>
이 영역에서 객체가 사라질 때 `Full GC`가 발생한다고 말한다. 그리고 Full GC가 발생하면 통상적으로 말하는, `STW(stop-the-world)`가 발생하여 application thread가 hang 멈춘다. 대개의 경우 GC 튜닝이란 이 stop-the-world 시간을 줄이는 것이다.

<img src="https://github.com/agongi/study/blob/master/java/garbage-collection/images/Screen%20Shot%202016-02-28%20at%2019.30.03.png" width="50%">

###### 1.3.Permanent Generation
Since it is a separated region, it is not considered as a part of the Java Heap space. Objects in this space are relatively permanent. Class definitions are stored here, as are `static instances`, `string pool` and `meta-data`. Rarely `Full GC` also comes over here to clean up this area.

#### 2. Garbage Collection tuning
<img src="https://github.com/agongi/study/blob/master/java/garbage-collection/images/endOfThreadDump.JPG" width="75%">

 - New Generation (young generation)

 : eden<br>
 : from<br>
 : to<br>

 - Tenured Generation (old generation)
 - perm gen

###### [2.1. GC 옵션](http://d2.naver.com/helloworld/37111)
<img src="https://github.com/agongi/study/blob/master/java/garbage-collection/images/Screen%20Shot%202016-02-28%20at%2018.39.24.png" width="75%">

 - -server : 기본적으로 설정  
 - -Xms, -Xmx : 동일하게 설정 (Memory size가 dynamic하게 조정되는 overhead 없애려는 목적)
 - -XX:NewRatio : 2 ~ 4 정도의 값 (head에서 young/old의 비율을 설정)
 - -XX:PermSize, -XX:MaxPermSize : OutOfMemoryError가 발생하고, 그 문제의 원인이 Perm 영역의 크기 때문일 때에만 설정
 - GC 로그설정 : 최대한 상세하게 (큰 성능상 이슈 없음)

###### [2.2. GC 방식](http://d2.naver.com/helloworld/1329)
 - CMS (Concurrent Mark Sweep) GC

 : 기본적으로 다른 Parallel GC 보다 빠르다.

 > Full GC 발생시, Compaction을 을 진행하지 않으므로 일반적으로 속도가 빠르다. 다만 Concurrent mode failure 발생시 (메모리 단편화로 인한 큰 사이즈의 memory load fail) Compaction을 진행하는데, 시간이 다른 Parallel GC보다 더 오래 소요된다.

 - Parallel GC

 : 기본적으로 다른 CMS GC 보다 느리다.

 > Parallel GC 방식에서는 Full GC가 수행될 때마다 Compaction 작업을 진행하기 때문에 시간이 많이 소요된다. 하지만, Full GC가 수행된 이후에는 메모리를 연속적으로 지정할 수 있어 메모리를 더 빠르게 할당할 수 있다.

 - G1 (Garbage First) GC

 : 지금까지 설명한 어떤 GC 방식보다도 빠르다. (Since JDK 1.7)

 > 기존의 Young, Old 영역 방식이 아닌, 바둑판 형식을 이용하여 각 영역에 객체를 할당한다. 그러다가, 해당 영역이 꽉 차면 다른 영역에서 객체를 할당하고 GC를 실행한다. 즉, 지금까지 설명한 Young의 세가지 영역에서 데이터가 Old 영역으로 이동하는 단계가 사라진 GC 방식이라고 이해하면 된다

<img src="https://github.com/agongi/study/blob/master/java/garbage-collection/images/Screen%20Shot%202016-02-28%20at%2019.19.13.png" width="50%">

결론적으로, 운영 중인 시스템 특성에 따라 적합한 GC 방식이 다르므로 해당 시스템에 가장 적합한 방식을 찾아야 한다. `-verbosegc` 옵션 추가 후 결과를 [분석하는 방법](https://blogs.oracle.com/poonam/entry/understanding_cms_gc_logs)을 추천한다.

평균적으로 다음의 성능이 나올경우 추가적인 GC튜닝이 필요없다.
 - Minor GC performance (less than 50ms)
 - Minor GC frequency (once in 10s)
 - Full GC performance (less than 1s)
 - Full GC frequency (once in 10m)

#### [3. Monitoring](http://d2.naver.com/helloworld/6043)
Stress Test
 - Apache JMeter
 - nGrinder

APM (Application Performance Manager)
 - New Relic
 - Jennifer
 - [Pinpoint](https://github.com/naver/pinpoint)
 - [Netdata](https://github.com/firehol/netdata)
