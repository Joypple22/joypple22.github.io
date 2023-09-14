---
layout: post
title: "[LeetCode] 133. Clone Graph"
subtitle: "algorithm"
categories: devlog
tags: algorithm graph
---

> LeetCode Top Interview 150의 133번 문제입니다.

<!--more-->

📚 목차
- [🌱 Clone Graph](#-clone-graph)
  - [🟤 문제 정의 - Definition](#-문제-요약-definition)
  - [🟤 문제 풀이 전략 추상화 - Abstraction](#-문제-풀이-전략-추상화-abstraction)
  - [🟤 문제 풀이 - Algorithm](#-문제-풀이-algorithm)

----

## 🌱 [Clone Graph](https://leetcode.com/problems/clone-graph/?envType=study-plan-v2&envId=top-interview-150)

### 🟤 문제 정의 (Definition)

- 파라미터로 Graph 자료구조 특징을 가진 노드가 주어진다.
- 간단하게 문제를 요약하면, 주어진 graph 자료구조 노드를 deep copy하여 반환하는 것이다.


- 조건
  - The number of nodes in the graph is in the range [0, 100].
  - 1 <= Node.val <= 100
  - Node.val is unique for each node.
  - There are no repeated edges and no self-loops in the graph.
  - The Graph is connected and all nodes can be visited starting from the given node.

---

### 🟤 문제 풀이 전략 추상화 (Abstraction)

이 문제를 해결하기 전에 deep copy가 무엇인지 알아야한다.

deep copy는 데이터의 무결성을 위해 자주 사용되는 개념이다. 일반적으로 객체 인스턴스같은 참조 자료형 데이터는 Heap 메모리에 저장되어 변수에 할당된다.
 
Java는 기본적으로 call by value 방식으로 기본형 데이터와 참조형 데이터를 저장한다. 하지만 참조형 데이터를 다른 변수에 별도의 처리없이 할당될 경우에는 Heap 메모리에 저장된 
참조형 데이터의 주소를 다른 변수에 할당하기 때문에 call by reference같은 현상이 일어난다. 즉, 다른 두개의 변수가 하나의 데이터 주소를 바라보고 있는 것처럼 
처리되기 때문에 하나의 변수에 할당된 참조형 데이터를 수정하면 해당 데이터를 할당받고 있는 다른 변수에도 영향을 받는다. 이 개념이 shallow copy(얕은 복사)이다.
 
얕은 복사는 데이터를 변수에 할당하기에 아주 간단하지만, 데이터의 무결성을 지키기 어려워 에러를 유발하는 원인이 되기도 한다. 이를 방지하고자 deep copy라는 
개념을 활용해야한다. deep copy는 참조형 데이터의 논리적 값은 일치하지만 물리적으로 저장된 메모리 주소의 위치는 다르게함을 의미한다.

deep copy 개념을 사용하여 참조형 데이터를 다른 변수에 할당하면 두 인스턴스는 별개의 주소에 저장되기 때문에 서로에게 영향을 주지 않는다. 이를 통해 데이터의 무결성을 
지킬 수 있다.

> ❗️단, 여기서 주의해야할 점이 있다. 바로 참조형 데이터 안에 또 다른 참조형 데이터가 멤버 변수, 필드로 있는 경우이다. 이 경우에도 참조형 데이터 안에 있는 또다른 
참조형 변수를 deep copy해주어야 deep copy라는 개념이 온전히 성럽될 수 있다.

...

문제에서 요구하는 것은 파라미터로 주어진 graph 자료구조의 객체 인스턴스를 deep copy하여 반환하는 것이다. graph 구조 데이터의 특징은 하나의 참조형 데이터 안에 
또 다른 참조형 데이터가 있고, 이러한 형태가 반복적으로 이루어져있다.

그렇기 때문에 2가지가 필요하다. 바로 참조형 데이터가 어디까지 연속적으로 하위 멤버 변수로 할당되어 있는지 탐색하고 각 노드를 deep copy하여 반환할 노드의 child노드로 
추가시키는 작업! 이 작업을 위해 dfs라는 방법을 사용한다. 또한, 조건으로 주어진 각 노드의 val값은 유니크하기 때문에 val값을 캐싱하여 해당 val과 연결되어 있는 
또다른 노드와 연결시키기 위해 각 val들을 담은 deep copy된 노드들을 캐싱할 배열 자료구조가 필요하다.

> 🥕 해당 문제의 시간복잡도는 O(N)이다. dfs를 사용해 모든 노드들을 방문하며 deep copy할 새로운 노드에 추가시켜야하기 때문이다.
> 
> 🥕 공간복잡도로는 O(N + 100) => O(N)이 된다. O(100)은 조건으로 주어진 `0 <= val <= 100`을 통해 노드를 캐싱할 배열이다. 또한, DFS는 
> 재귀함수를 사용하여 마치 stack과 같은 방법으로 탐색하기 때문에 주어진 모든 노드가 N일 경우 O(N)의 공간이 필요하다.


---

### 🟤 문제 풀이 (Algorithm)

```java
class Solution {
	public Node cloneGraph(Node node) {
		if (node == null) return node;

		Node[] isVisit = new Node[101];
		Node answer = new Node(node.val);

		dfs(node, answer, isVisit);

		return answer;
	}

	public void dfs(Node node, Node answer, Node[] isVisit) {
        isVisit[answer.val] = answer;
        
        for(int i = 0; i < node.neighbors.size(); i++) {
            Node n = node.neighbors.get(i);

            if(isVisit[n.val] == null) {
                Node newNode = new Node(n.val);

                answer.neighbors.add(newNode);

                dfs(n , newNode , isVisit);
            } else {
                answer.neighbors.add(isVisit[n.val]);
            }
        }
	}
}
```

---
