## Volatile vs Synchronized
Java conceptual comparison between volatile and synchronized keyword.

>###### Take a look at brief java-memory-model (JMM) and comparison between volatile and synchronized keyword.

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.02.05
ㅁ References:
 - http://thswave.github.io/java/2015/03/08/java-volatile.html
 - http://stackoverflow.com/questions/3519664/difference-between-volatile-and-synchronized-in-java
 - https://en.wikipedia.org/wiki/Java_memory_model
```

On multiprocessor architectures, individual processors may have their own local caches that are out of sync with main memory. It is generally undesirable to require threads to remain perfectly in sync with one another because this would be too costly from a performance point of view. This means that at any given time, different threads may see different values for the same shared data.

#### 1. Volatile

Forces all accesses (read or write) to the volatile variable to occur to main memory, effectively keeping the volatile variable out of processors caches.

> Volatile keyword is used in variable

#### 2. Synchronized
Prevents any other thread from obtaining the monitor (or lock) for the same object.

> Synchronized keyword is used in method
