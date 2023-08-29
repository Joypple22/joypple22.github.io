---
layout: post
title: "[LeetCode] 21. Merge Two Sorted Lists"
subtitle: "algorithm"
categories: devlog
tags: algorithm array linked-list
---

> LeetCode Top Interview 150의 21번 문제입니다.

<!--more-->

📚 목차
- [🌱 Merge Two Sorted Lists](#-merge-two-sorted-lists)
  - [🟤 문제 정의 - Definition](#-문제-요약-definition)
  - [🟤 문제 풀이 전략 추상화 - Abstraction](#-문제-풀이-전략-추상화-abstraction)
  - [🟤 문제 풀이 - Algorithm](#-문제-풀이-algorithm)

----

## 🌱 [Merge Two Sorted Lists](https://leetcode.com/problems/merge-two-sorted-lists/?envType=study-plan-v2&envId=top-interview-150)

### 🟤 문제 정의 (Definition)

- 파리미터로 링크드리스트 자료구조를 가진 노드 2개가 주어진다.
- 해당 노드는 int 필드를 가지고 있다.

- 파라미터로 주어진 2개의 링크드리스트는 head의 역할을 하며 child 노드를 가지고 있다.
- 각자 연결되어 있는 노드의 필드값을 오름차순으로 장렬하여 하나의 링크드리스트 자료구조 형태로 재구성하는 문제이다.


- 조건
  - The number of nodes in both lists is in the range [0, 50].
  - -100 <= Node.val <= 100
  - Both list1 and list2 are sorted in non-decreasing order.

---

### 🟤 문제 풀이 전략 추상화 (Abstraction)

이 문제의 경우 매우 간단하게 풀 수 있다. 배열이 오름차순으로 정렬되어 있는 것처럼 링크드리스트가 오름차순 필드를 가지는 순서로 
자식 노드에 할당되어 있다는 것을 파악하면 된다.

먼저, head가 될 노드를 하나 생성한다. 이 헤드의 노드로부터 자식노드가 될 노드를 찾아야하는데, 2개의 링크드리스트의 현재 val 필드값을 
비교하여 작은 값을 가진 노드를 헤드의 next 필드에 초기화한다. 그리고 초기화한 링크드리스트의 변수를 자식 노드로 초기화한다.

이런 방식으로 구현하는 것은 마치 배열의 인덱스가 비교 후 1씩 증가하는 것과 비슷하게 동작한다. 현재의 노드를 비교 완료하여 할당하면 
해당 노드에서 다음 노드로 갱신해야 또다시 비교할 수 있는 원리이다.

> 🥕 시간복잡도로는 2개의 노드를 투포인터 방식으로 순서대로 순환하기 때문에 O(2N) => O(N)의 시간복잡도를 가진다.
> 
> 🥕 공간복잡도는 링크드리스트 노드의 특성에 따라 별도로 추가된 공간은 존재하지 않는다. 따라서 O(1)의 공간복잡도를 가진다.


> 해당 문제는 왜 공간복잡도 O(1)을 가질까?
> 
> 배열과 햇갈리는 부분이다. 배열은 선언하게 되면 연속적으로 메모리 공간을 할당한다. 할당된 메모리 공간 안에서 각 값들이 참조되는 방식이기 때문에 
> N만큼의 길이를 가지는 배열은 N만큼의 공간을 새로 만든다.
> 
> 반면에 링크드 리스트는 참조 타입 데이터의 연속이다. 참조 타입 데이터는 Heap이라는 메모리 공간에 할당되는데, 이런 참조 타입 데이터의 
> 특징은 deep copy를 하지 않으면 해당 참조 데이터를 다른 변수가 참조할 수 있다.
> 
> 예를 들어, <br />
> int a = 10; <br />
> b = a; <br />
> 같이 기본 타입 데이터는 a를 20으로 재할당해도 b는 10을 가지고 있다. 즉, 기본 타입 데이터는 할당할 때 해당 값으로 참조된다. (정확하게는 
> JVM Stack 메모리에 직접 저장되고, 인스턴스의 필드로써 저장될 때는 Heap 영역에 직접 저장된다. 기본타입 데이터를 할당하거나 비교할 때는 값으로 
> 판단한다.)
> 
> 참조 타입 데이터는 조금 다르게 동작한다. <br />
> Node a = new Node(1); <br />
> Node b = a; <br />
> 
> a.val = 3; <br />
> b.val == 3 => true <br />
> 
> 참조 타입 데이터는 변수에 할당될 때 Heap이라는 데이터 주소를 할당받는다. b는 a의 노드를 할당받았는데, a와 b는 동일한 인스턴스를 
> 할당받았다는 의미이다. 따라서 동일한 주소값을 가지고 있기 때문에 a의 노드 필드를 변경하면 b에도 영향이 끼친다.

---

### 🟤 문제 풀이 (Algorithm)

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode mergeTwoLists(ListNode list1, ListNode list2) {
        ListNode answer = new ListNode(0);

        ListNode l1 = list1;
        ListNode l2 = list2;

        ListNode temp = answer;

        while (l1 != null && l2 != null) {
            int l1Val = l1.val;
            int l2Val = l2.val;

            if (l1Val < l2Val) {
                temp.next = l1;
                temp = l1;
                l1 = l1.next;
            } else {
                temp.next = l2;
                temp = l2;
                l2 = l2.next;
            }
        }

        if (l1 != null && l2 == null) {
            while (l1 != null) {
                temp.next = l1;
                temp = l1;
                l1 = l1.next;
            }
        } else if (l1 == null && l2 != null) {
            while (l2 != null) {
                temp.next = l2;
                temp = l2;
                l2 = l2.next;
            }
        }

        answer = answer.next;

        return answer;
    }
}
```

---
