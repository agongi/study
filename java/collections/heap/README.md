## Heap

```
@author: suktae.choi
- https://ratsgo.github.io/data%20structure&algorithm/2017/09/27/heapsort
```

힙은 크거나, 작은 값을 찾기 위해 만든 이진 트리입니다.

- 완전 이진트리 (b-tree) 의 형태를 띄어야 하고
- 부모는 자식보다 크거나, 작아야 한다 (insert/delete 시 sort 발생)
  - Max Heap
  - Min Heap

<img src="images/Screen Shot 2019-06-27 at 00.58.57.png">

> 위의 그림은 최대힙 (max-heap)

#### 우선순위 큐 (Priority Queue)

`부모/자식간 greater or less` 관계가 성립되므로, 힙으로 우선순위 큐를 구현 할 수 있다.

- enqueue

<img src="images/Screen Shot 2019-06-27 at 01.16.06.png" width="85%">

- dequeue

<img src="images/Screen Shot 2019-06-27 at 01.16.08.png" width="85%">

우선순위 큐는 FIFO 가 아닌, 우선순위에 따라 dequeue 되어야 한다.

heap 으로 값을 관리하고, (최소힙 기준) #get 을 호출하면 낮은 우선순위로 값이 나옴이 보장된다.

#### 이진트리 (binary tree)

자식노드가 최대 두 개인 노드들로 구성된 트리

#### 이진탐색트리 (binary search tree)

- left-child-node <= parent
- right-child-node >= parent
- 그리고 이진트리

[in-order (중위순회)](https://ratsgo.github.io/data%20structure&algorithm/2017/10/22/bst) 방식으로 탐색하는데, 그때의 효율성은 아래와 같다:

- search-key K 입력됨
- 루트노드와 K 비교
  - K 가 더 크다면 left-subtree 는 don't care (데이터의 절반을 보지 않아도 됨)
- right-node 와 K 비교
  - … 해당 과정 반복하며 효율적으로 탐색

insert/delete 가 자주 발생할시 트리의 balance 가 무너질수 있고, 그때는 효율적이지 않게됩니다.

<img src="images/Screen Shot 2019-06-27 at 01.31.30.png" width="50%">

#### [AVL 트리](https://ratsgo.github.io/data%20structure&algorithm/2017/10/27/avltree/)

AVL 트리란 서브트리의 높이를 적절하게 제어해 전체 트리가 어느 한쪽으로 늘어지지 않도록 한 이진탐색트리(Binary Search Tree)의 일종입니다.

#### [RB 트리](https://ratsgo.github.io/data%20structure&algorithm/2017/10/28/rbtree/)

RB 트리는 다음 다섯 가지 속성을 만족하는 이진탐색트리(Binary Search Tree)의 일종입니다.

1. 모든 노드는 빨간색, 검은색 둘 중 하나다.
2. 루트노드는 검은색이다.
3. 모든 잎새노드(NIL)는 검은색이다.
4. 어떤 노드가 빨간색이라면 두 개 자식노드는 모두 검은색이다. (따라서 빨간색 노드가 같은 경로상에 연이어 등장하지 않는다)
5. ‘각 노드~자손 잎새노드 사이의 모든 경로’에 대해 검은색 노드의 수가 같다.

#### [의사결정 트리](https://ratsgo.github.io/machine%20learning/2017/03/26/tree/)

#### [랜덤포레스트 (Random Forest)/로테이션포레스트 (Rotation Forest)](https://ratsgo.github.io/machine%20learning/2017/03/17/treeensemble/)