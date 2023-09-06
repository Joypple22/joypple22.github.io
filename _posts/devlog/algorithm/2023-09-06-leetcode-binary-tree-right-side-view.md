---
layout: post
title: "[LeetCode] 199. Binary Tree Right Side View"
subtitle: "algorithm"
categories: devlog
tags: algorithm tree
---

> LeetCode Top Interview 150의 199번 문제입니다.

<!--more-->

📚 목차
- [🌱 Binary Tree Right Side View]()
  - [🟤 문제 정의 - Definition](#-문제-요약-definition)
  - [🟤 문제 풀이 전략 추상화 - Abstraction](#-문제-풀이-전략-추상화-abstraction)
  - [🟤 문제 풀이 - Algorithm](#-문제-풀이-algorithm)

----

## 🌱 [Binary Tree Right Side View](https://leetcode.com/problems/binary-tree-right-side-view/description/?envType=study-plan-v2&envId=top-interview-150)

### 🟤 문제 정의 (Definition)

- 파라미터로 트리 구조의 루트 노드가 주어진다.
- 해당 루트노드로부터 각 depth에 해당하는 노드들 중 제일 오른쪽에 있는 노드들을 리스트에 담아 반환하는 문제이다.


- 조건
  - The number of nodes in the tree is in the range [0, 100].
  - -100 <= Node.val <= 100

---

### 🟤 문제 풀이 전략 추상화 (Abstraction)

해당 문제는 정확하게 이해해야한다. 무조건 오른쪽 자식 노드를 각 depth에 맞게 배열에 담아 반환하는 문제가 아.니.다.

각 이진트리에서 depth마다 최대로 가질 수 있는 노드가 정해져있다. 루트 노드는 1개, 그 아래는 루트 노드로부터 left, right 총 2개, 그 아래 depth는 
위의 2개의 노드에서 각각 left, right가 생겨 총 4개의 노드가 생길 '수' 있다. 하지만 무조건 depth의 제곱만큼 노드가 생기는 것은 아니다. 이 부분을 잘 생각해보아야할 것이다.

만일 루트노드로부터 내림차순으로 작은 값만 추가한다면 왼쪽 노드만이 길게 이어진 트리 구조가 될것이다. 그럼 각 depth에서 제일 오른쪽에 있는 노드는 왼쪽 자식 노드일 것이다. 이런 경우가 
존재한다는 것을 생각해야한다.

그래서 각 자식 노드에 대한 정보를 리스트에 저장하기 위해 depth또한 함께 재귀 함수의 파라미터로 지정한다. depth는 1씩 일정하게 증가므로 각 depth의 제일 오른쪽에 있는 값을 왼쪽부터 차례대로 리스트에 
저장한다. 그리고 해당 depth에서 오른쪽 자식 노드에 접근할 때마다 해당 depth를 사용하여 배열 인덱스에 접근 후 갱신한다.

> 🥕 시간복잡도는 모든 노드를 탐색하여 제일 오른쪽 노드를 갱신하기 때문에 O(N)의 시간복잡도를 가진다.
> 
> 🥕 공간복잡도 또한 모든 노드를 탐색하는 재귀 함수를 사용하기 때문에 O(N)의 공간복잡도를 가진다.

---

### 🟤 문제 풀이 (Algorithm)

```java
class Solution {
    public List<Integer> rightSideView(TreeNode root) {
        List<Integer> list = new ArrayList<>();

        bfs(list, root, 0);

        return list;
    }

    public void bfs(List<Integer> list, TreeNode node, int depth) {
        if (node == null) return;

        if (list.size() <= depth) {
            list.add(node.val);
        } else {
            list.set(depth, node.val);
        }

        bfs(list, node.left, depth + 1);
        bfs(list, node.right, depth + 1);
    }
}
```

---
