---
layout: post
title: "[LeetCode] 148. Sort List"
subtitle: "algorithm"
categories: devlog
tags: algorithm sort
---

> LeetCode Top Interview 150의 148번 문제입니다.

<!--more-->

📚 목차
- [🌱 Sort List](#-sort-list)
  - [🟤 문제 정의 - Definition](#-문제-요약-definition)
  - [🟤 문제 풀이 전략 추상화 - Abstraction](#-문제-풀이-전략-추상화-abstraction)
  - [🟤 문제 풀이 - Algorithm](#-문제-풀이-algorithm)

- [🌱 풀이 개선](#-풀이-개선)

----

## 🌱 [Sort List](https://leetcode.com/problems/sort-list/?envType=study-plan-v2&envId=top-interview-150)

### 🟤 문제 정의 (Definition)

- 파라미터로 링크드리스트 노드 하나가 주어진다.
- 이 링크드리스트는 숫자값이 val 필드에 무작위로 저장되어 있다.

- 해당 링크드리스트를 오름차순으로 정렬하고, 해당 노드를 반환하는 묹제이다.


- 조건
  - The number of nodes in the list is in the range [0, 5 * 10<sup>4</sup>].
  - -10<sup>5</sup> <= Node.val <= 10<sup>5</sup>

---

### 🟤 문제 풀이 전략 추상화 (Abstraction)

방법 1.
링크드리스트는 배열이 아니라 하나의 노드가 다른 노드로 연결되어 있는 구조이기 때문에 단순히 배열을 오름차순으로 정렬하는 API를 사용하여 정렬할 수 없다. 
해당 문제를 단순하게 접근하면 링크드리스트를 순환하며 접근할 수 있는 숫자 값을 배열에 모두 저장한다. 숫자값이 저장된 배열을 오름차순으로 정렬한다. 이후에 
정렬된 배열의 숫자를 기준으로 다시 링크드리스트 노드를 만들어 해당 head 노드를 반환하는 방법으로 문제를 풀이했다.

> 🥕 시간복잡도는 먼저 링크드리스트 노드를 순환하며 배열에 숫자값을 저장하는 코드로 O(N)의 시간복잡도를 가졌다. 또한, 배열을 오름차순으로 정렬하는 API는 보통 퀵정렬을 
> 사용하기 때문에 O(logN)의 시간복잡도를 가지며, 오름차순으로 정렬된 배열을 순환하며 링크드리스트 노드를 만들기 때문에 O(N)의 시간복잡도를 가진다. 총 O(2N + logN) => O(N)의 
> 사간복잡도를 가진다.
> 
> 🥕 공간복잡도는 먼저 배열을 저장하는 공간 O(N)과 새롭게 노드를 만드는 공간 O(N)의 공간이 필요하여 총 O(2N) => O(N)의 공간복잡도가 필요하다.

---

### 🟤 문제 풀이 (Algorithm)

```java
class Solution {
    public ListNode sortList(ListNode head) {
        List<Integer> list = new ArrayList<>();

        while (head != null) {
            list.add(head.val);

            head = head.next;
        }

        List<Integer> answer = list.stream().sorted().collect(Collectors.toList());

        ListNode answerNode = new ListNode(0);
        ListNode newNode = answerNode;
        for (int i = 0; i < answer.size(); i++) {
            ListNode node = new ListNode(answer.get(i));
            newNode.next = node;
            newNode = node;
        }

        return answerNode.next;
    }
}
```

---

## 🌱 풀이 개선

해당 문제는 중간에 배열로 값을 옮겨와 다시 링크드리스트 노드로 재구성해야하는 과정이 포함되어 있다. 이 과정이 제거된다면 비록 O(N)의 시간복잡도라 하더라도 
시간적, 공간적으로 더 효율적으로 리소스를 사용하여 문제를 해결할 수 있다.

다양한 정렬 방법 중에 Merge Sort를 사용하는 방법이 있다. Merge Sort는 퀵 정렬과 비슷하게 O(logN)의 시간복잡도를 가지고 정렬하는 알고리즘이다. 
링크드리스트는 인덱스로 접근하여 연결되어 있는 값들 중 반절 값을 O(1)로 조회할 수 없다. 때문에 빠르게 링크드리스트의 중간 연결값을 파악하는게 첫번째 관문이다. 

slow와 fast 2개의 노드를 먼저 마련하고 slow는 slow.next로 재할당, fast는 fast.next.next로 재할당한다. slow는 1칸씩, fast는 2칸씩 노드를 이동하는 것이다. 
fast가 먼저 노드의 끝값이 null에 도달하면 slow는 fast의 절반까지 도달했을 것이다. 이것을 활용하여 O(1/2 N)의 시간복잡도로 slow 노드를 통해 링크드리스트의 가운데 노드를 
파악할 수 있다.

반절로 나눈 링크드리스트의 next가 null이 되도록, 즉, 링크드리스트에 연결되어 있는 노드가 1개가 될때까지 재귀를 호출하여 분할한다. 재귀로 호출하여 노드를 잘게 분할하여 나온 값을 
합쳐 정렬하는 방법이다.

```java
class Solution {
    public ListNode sortList(ListNode head) {
        
        if (head == null || head.next == null) return head;

        ListNode prev = null;
        ListNode slow = head;
        ListNode fast = head;

        while (fast != null && fast.next != null) {
            prev = slow;
            slow = slow.next;
            fast = fast.next.next;
        }

        prev.next = null;

        ListNode left = sortList(head);
        ListNode right = sortList(slow);

        return merge(left, right);
    }

    public ListNode merge(ListNode left, ListNode right) {

        ListNode node = new ListNode(0);
        ListNode temp = node;

        while (left != null && right != null) {
            if (left.val < right.val) {
                temp.next = left;
                left = left.next;
            } else {
                temp.next = right;
                right = right.next;
            }

            temp = temp.next;
        }

        if (left != null) temp.next = left;

        if (right != null) temp.next = right;

        return node.next;
    }
}
```

> 🥕 해당 방법으로 개선할 시 시간복잡도는 sortList에서 O(1/2 * N)만큼의 시간복잡도, merge가 불려지는 횟수인 LogN에서 merge에서 N만큼 시간복잡도를 가지기 때문에 NlogN의 시간복잡도를 가진다. 
> 총 상수까지 더해 O(N/2 + NlogN)의 시간복잡도이지만 상수를 제거하여 O(NlogN)의 시간복잡도를 가진다.
> 
> 🥕 공간복잡도는 해당 노드로 수행되기 때문에 추가적인 노드를 저장하는 공간은 필요없지만 logN만큼의 stack이 쌓이기 때문에 이에 대한 공간복잡도 O(logN)이 필요하다.
