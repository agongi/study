## Index

```
ㅁ Author: suktae.choi
ㅁ Date: 2017.03.11
ㅁ References:
 - https://dev.mysql.com/doc/refman/5.7/en/mysql-indexes.html
 - http://choko11.tistory.com/entry/%EC%9D%B8%EB%8D%B1%EC%8A%A4-1-%EA%B0%9C%EB%85%90%EC%A2%85%EB%A5%98%EC%A3%BC%EC%9D%98%EC%82%AC%ED%95%AD
 - http://www.dba-oracle.com/t_difference_between_btree_and_bitmap_index.htm
 - http://mikyung.net/451
```

### Concept
Index is used to find a specific row more faster, There are two different type of index:
- [B-Tree](#b-tree)
- [Bitmap](https://github.com/agongi/study/tree/master/rdbms/index/#bitmap)

#### B-Tree
<img src="https://github.com/agongi/study/blob/master/rdbms/index/images/SQL_245.jpg" width="75%">

- Data Structure: b-tree
- 범위 검색: 첫번째 범위의 left node 로 간 후, 리프노드만을 따라가며 검색
- 데이터블록: 노드에서 저장할수 있는 인덱스 크기
  - 추가: overflow 발생시, 새로운 블록 생성 및 깊이 증가. 한쪽으로 불균형 대칭이 되면 인덱스 재빌딩 (Full GC)
  - 삭제: 해당 인덱스에 flag 설정, 일정 이상되면 기존 블록과 병합 진행. 불균형 되면 인덱스 재빌딩

#### Bitmap
<img src="https://github.com/agongi/study/blob/master/rdbms/index/images/oracle_bitmap_index_structure.jpg" width="75%">

- Data Structure: 2-dimensional array
- 검색: 해당 배열을 쭉 읽는다 (e.g. array[1]\[\])

#### Why B-Tree is commonly used?
- 트리가 균형이 잡혔을다고 가정하면, 거의 대부분의 select 에서 동일한 응답 속도를 보장해 준다. (같은 Depth)
  - B-Tree 의 시간복잡도는 트리의 깊이(Depth) 에 결정됨, 균형잡힌 트리는 동일한 depth 로 구성되어 있음
  - 트리의 균형이 깨지는 경우 (특정 Leaf node 의 깊이가 다르다, 삽입/삭제가 많이 발생한 경우) 망가짐, 재구성 필요
- 우리가 인덱스 (pk) 로 주로 사용하는 키는 auto-increment 의 속성을 가진 (혹은 고루 넓은 범위의 숫자) id 인 경우가 대부분이므로 트리가 균형잡히게 구성된다. 그리고 변경이 거의 일어나지 않는다. 그래서 btree 가 대부분의 상황에서 적절
  - 최초 트리 구성시 균형 잡힘. 삽입/삭제가 거의 발생하지 않아 불균형하게 될 가능성 적음
