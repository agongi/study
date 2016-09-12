## HashMap
Java fundamental of HashMap.

>###### Simply describe how HashMap works internally.

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.02.14
ㅁ References:
 - https://en.wikipedia.org/wiki/Hash_table
 - http://d2.naver.com/helloworld/831311
 - http://bcho.tistory.com/1072
```

**Definition of HashMap**

The data structure of `{key, value}` pair array using `hashCode based on key` to insert and/or select values.<br>
HashCode is generated from `hashCode() method` that is defined in Object class, and returns int (32-bit type).

```java
@Override
public int hashCode() {
  final int prime = 31;
  int result = 1;
  // generate object hash based on member variables
  result = prime * result + ((addr == null) ? 0 : addr.hashCode());
  result = prime * result + age;
  result = prime * result + ((name == null) ? 0 : name.hashCode());
  result = prime * result + zipCode;
  // end of generation

  return result;
}
```

<img src="https://github.com/agongi/study/blob/master/java/hashmap/images/Screen%20Shot%202016-02-26%20at%2022.07.22.png" width="50%">

HashCode collision could happen because a number of object can be defined is larger than 2^32 and the same hash can be generated in each different key.<br>
Take a look at this formulation :

```
index = hash_function(KEY) % SIZE_OF_ARRAY;
```

If size is 10 and keys are 1 or 11 or 21, index (hash) is equal to 1.

Here is the way how to avoid collision.

#### 1. Separate chaining
LinkedList<br>
Additional heap is required in each insert<br>
Dynamic array

<img src="https://github.com/agongi/study/blob/master/java/hashmap/images/Screen%20Shot%202016-02-26%20at%2022.07.30.png" width="50%">

 - insert : index[x] is conflicted, easily add next node using LinkedList
 - select : get index from hash of key, directly access index[x] and linearly search list until key is equal to
 - delete : easily delete link of nodes

> About JDK 1.8 uses LinkedList when the number of nodes is less than 8, otherwise Tree (red-black) for performance.

#### 2. Open addressing
###### 2.1. Linear probing
Use empty space<br>
No more additional heap is required<br>
Fixed array<br>
Refreshing is required - to clear dummy value<br>
Resizing is required - to create new array when origin is about to be full

 - insert : 11을 키로 하는 데이타를 그림과 같이 넣으면 1이 키인 데이타와 충돌이 발생한다. (이미 index가 1인 버킷에는 데이타가 들어가 있다.) Linear Probing에서는 아래 그림과 같이 충돌이 발생한 index (1) 뒤의 버킷에 빈 버킷이 있는지를 검색한다. 2번 버킷은 이미 index가 2인 값이 들어가 있고, 3번 버킷이 비어있기 3번에 값을 넣으면 된다.

<img src="https://github.com/agongi/study/blob/master/java/hashmap/images/Screen%20Shot%202016-02-26%20at%2022.07.45.png" width="50%">

 - select : key 11에 대해서 검색을 하면, index가 1이기 때문에, array[1]에서 검색을 하는데, key가 일치하지 않기 때문에 뒤의 index를 검색해서 같은 키가 나오거나 또는 Key가 없을때 까지 검색을 진행한다.

 - delete : 삭제를 했을 경우 충돌에 의해서 뒤로 저장된 데이타는 검색이 안될 수 있다. 아래에서 좌측 그림을 보자,  2번 index를 삭제했을때, key 11에 대해서 검색하면, index가 1이기 때문에 1부터 검색을 시작하지만 앞에서 2번 index가 삭제되었기 때문에, 2번 index까지만 검색이 진행되고 정작 데이타가 들어 있는 3번 index까지 검색이 진행되지 않는다.<br>
 그래서 이런 문제를 방지하기 위해서 우측과 같이 데이타를 삭제한 후에, Dummy node를 삽입한다. 이 Dummy node는 실제 값을 가지지 않지만, 검색할때 다음 Index까지 검색을 연결해주는 역할을 한다.

<img src="https://github.com/agongi/study/blob/master/java/hashmap/images/Screen%20Shot%202016-02-26%20at%2023.41.26.png" width="50%">

**Performance**

<img src="https://github.com/agongi/study/blob/master/java/hashmap/images/Screen.Shot.2016-02-26.at.22.08.56.png" width="50%">
