---
layout: post
title: "[LeetCode] 373. Find K Pairs with Smallest Sums"
subtitle: "algorithm"
categories: devlog
tags: algorithm heap
---

> LeetCode Top Interview 150의 373번 문제입니다.

<!--more-->

📚 목차
- [🌱 Find K Pairs with Smallest Sums](#-find-k-pairs-with-smallest-sums)
  - [🟤 문제 정의 - Definition](#-문제-요약-definition)
  - [🟤 문제 풀이 전략 추상화 - Abstraction](#-문제-풀이-전략-추상화-abstraction)
  - [🟤 문제 풀이 - Algorithm](#-문제-풀이-algorithm)

----

## 🌱 [Find K Pairs with Smallest Sums](https://leetcode.com/problems/find-k-pairs-with-smallest-sums/description/?envType=study-plan-v2&envId=top-interview-150)

### 🟤 문제 정의 (Definition)

- 파라미터로 1차원 배열 2개(nums1, nums2)와 정수형 데이터 int (k)가 주어진다.
- 2개의 1차원배열의 요소들을 각각 1개씩 조합하여 만든 리스트의 합이 가장 작은 수의 조합부터 차례대로 K만큼 저장된 리스트를 반환하는 문제이다.


- 조건
  - 1 <= nums1.length, nums2.length <= 105
  - -10<sup>9</sup> <= nums1[i], nums2[i] <= 10<sup>9</sup>
  - nums1 and nums2 both are sorted in non-decreasing order.
  - 1 <= k <= 10<sup>4</sup>

---

### 🟤 문제 풀이 전략 추상화 (Abstraction)

이 문제를 풀이할 때 처음에 세운 전략은 이러했다. 먼저 두 배열을 정렬한다. 오름차순으로 정렬한 두 배열의 인덱스 0부터 조회하면서 각 배열의 인덱스가 
1씩 늘어난 경우 2가지를 비교하여 작은 합을 가지고 있는 배열을 가리키는 포인터를 1씩 증가시킨다. 이러한 방법을 사용하여 k만큼 값을 추가하는 방법을 고려했었다.

하지만 이 문제에서 heap을 사용하게 된다면 굳이 배열을 재정렬하고 각 값의 요소들을 순환하면서 복잡하게 처리할 것 없이 조금 더 쉽게 처리할 수 있다.

먼저 하나의 배열을 정하고 해당 배열은 인덱스 0을 유지하면서 다른 배열의 인덱스를 증가시키며 두 배열의 각 인덱스의 값들과 인덱스를 조합한 값들을 heap에 저장한다. 
이 때 heap은 2개의 값이 저장되어 있는 리스트를 오름차순으로 정렬하면서 저장하는 최소힙을 사용한다.

이후 heap에 저장된 값을 pop하면서 답으로 반환할 리스트에 저장하는데.. 이때! 주의사항이 있다. 바로 pop한 이후 각 배열로부터 값을 얻어온 2개의 값의 합과 
아직 가지고 있는 요소들 전체를 비교하지 못한 배열의 인덱스를 증가시켜서 발생하게 된 수의 조합을 다시 heap에 저장한다.

heap은 최소힙으로 설정했기 때문에 이 때 새롭게 각 값들의 요소들의 조합을 heap에 저장해도 정렬이 된다. 이 방법을 통해 k번 값을 추가하면 정답을 얻을 수 있다.

> 🥕 시간복잡도는 먼저 num1를 모두 순환하면서 heap에 값을 저장하기 때문에 O(NlogN)의 시간복잡도가 소요된다. 이후, while문으로 k번 값을 제거하면서 answer에 
> 저장하기 때문에 O(klogN)의 시간복잡도가 소요된다. 따라서 총 시간복잡도는 O(NlogN + klogN)이 된다.
> 
> 🥕 공간복잡도는 nums1의 값을 모두 heap에 저장하기 때문에 O(N)의 공간복잡도가 소요된다. 또한 answer로 반환할 list도 새롭게 할당할 공간이 
> 필요하기 때문에 총 공간복잡도는 O(N + k)가 된다.

---

### 🟤 문제 풀이 (Algorithm)

```java
class Solution {
    public List<List<Integer>> kSmallestPairs(int[] nums1, int[] nums2, int k) {
        PriorityQueue<int[]> minHeap = new PriorityQueue<>((a, b) -> (a[0] + a[1]) - (b[0] + b[1]));

		for (int i : nums1) {
            minHeap.add(new int[] {i, nums2[0], 0});	
        }
        
        List<List<Integer>> answer = new ArrayList<>();
        
		while (k > 0 && !minHeap.isEmpty()) {
            int[] ints = minHeap.poll();
            answer.add(List.of(ints[0], ints[1]));
            
            int nums2Index = ints[2];
            
            if (nums2Index < nums2.length - 1) {
                minHeap.add(new int[]{ints[0], nums2[nums2Index + 1], nums2Index + 1});  
            }
            
            k--;
        }

        return answer;
    }
}
```

---
