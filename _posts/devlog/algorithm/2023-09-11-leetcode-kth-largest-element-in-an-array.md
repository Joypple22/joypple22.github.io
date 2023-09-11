---
layout: post
title: "[LeetCode] 215. Kth Largest Element in an Array"
subtitle: "algorithm"
categories: devlog
tags: algorithm heap
---

> LeetCode Top Interview 150의 215번 문제입니다.

<!--more-->

📚 목차
- [🌱 215. Kth Largest Element in an Array]()
  - [🟤 문제 정의 - Definition](#-문제-요약-definition)
  - [🟤 문제 풀이 전략 추상화 - Abstraction](#-문제-풀이-전략-추상화-abstraction)
  - [🟤 문제 풀이 - Algorithm](#-문제-풀이-algorithm)

----

## 🌱 [Kth Largest Element in an Array](https://leetcode.com/problems/kth-largest-element-in-an-array/?envType=study-plan-v2&envId=top-interview-150)

### 🟤 문제 정의 (Definition)

- 파라미터로 1차원 배열(nums)와 정수형 데이터 (k)가 주어진다.
- nums 배열 안에 있는 요소들 중, k번째로 큰 값을 찾는 문제이다.
- 단, 정렬을 사용하지 않아야한다.


- 조건
  - 1 <= k <= nums.length <= 10<sup>5</sup>
  - -10<sup>4</sup> <= nums[i] <= 10<sup>4</sup>

---

### 🟤 문제 풀이 전략 추상화 (Abstraction)

방법 1.

사실 이 문제는 정렬을 사용하면 쉽게 해결할 수 있는 문제이다. 내림차순으로 정렬하고 K - 1번째 인덱스의 값을 반환하면 해결할 수 있는 문제이다.

방법 2.
하지만 문제의 조건은 정렬을 사용하지 않고 해결할 수 있는지의 여부를 묻고 있다. 그렇다면 값의 순서를 보장하도록 정렬 API를 직접 사용하는 것이 아니라 
배열의 값을 재구성하면서 정렬이 될 수 있도록 하는 방법을 사용하면 될 것이다. 바로 최대 Heap을 사용하는 것이다.

최대 Heap은 보통 완전이진트리 방식으로 구현된다. 처음에 저장하면 해당 값은 루트 노드가 되지만, 값을 저장할 때마다 부모 노드와 저장하는 값의 크기를 비교하여 
값이 작다면 그대로 저장하고 값이 크다면 부모노드와 저장할 자식 노드의 위치를 바꾸는 방법이다. 이 방법을 사용하면 쉽게 정렬하면서 값을 재구성할 수 있다.

따라서 주어진 1차원 배열의 값들을 힙을 사용한 우선순위 큐에 저장하고 우선순위 큐에서 k만큼 pop하고난 마지막 값을 반환하는 것으로 해당 문제를 풀기로 하였다.

> 🥕 시간복잡도는 O(NlogN)이다. 먼저, 1차원 배열에 있는 값들을 순환하면서 heap에 데이터를 저장한다. 이때 heap을 최대힙으로 사용한다고 가정한다면 
> 최대 힙에 값을 저장할 때는 최악의 경우 하나의 요소를 저장할 때마다 logN의 시간복잡도가 소요된다. 저장할 값이 제일 작으면 O(1)이겠지만, 저장할 값이 제일 큰 값이라면 트리의 노드를 역으로 
> 거슬러 올라가면서 부모노드와 자식노드의 순서를 바꾸어야하기 때문이다. 마찬가지로 값을 삭제할 때도 삭제할 노드 아래 자식노드가 있다면 노드를 재정렬해주어야 한다. 그렇기 때문에 
> 삭제할 값이 k번 있다면 klogN의 시간복잡도가 소요된다. 따라서 최종적으로 O(NlogN + klogN)의 시간복잡도가 소요된다.
> 
> 🥕 공간 복잡도는 1차원 배열의 요소만큼 heap을 사용하기 때문에 O(N)의 공간복잡도가 소요된다. 


---

### 🟤 문제 풀이 (Algorithm)

방법 1.

```java
class Solution {
	
	public int findKthLargest(int[] nums, int k) {
		Arrays.sort(nums);
		
		return nums[nums.length - k]; 
    }
}
```

...

방법 2.

```java
class Solution {
  public int findKthLargest(int[] nums, int k) {
    Queue<Integer> queue = new PriorityQueue<>(Collections.reverseOrder());

	  for (int num : nums) {
		  queue.add(num);
	  }

    for (int i = 1; i < k; i++) {
      queue.remove();
    }

    return queue.remove();
  }
}
```

---
