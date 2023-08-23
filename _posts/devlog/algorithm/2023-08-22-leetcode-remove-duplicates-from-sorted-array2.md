---
layout: post
title: "[LeetCode] 80. Remove Duplicates from Sorted Array II"
subtitle: "algorithm"
categories: devlog
tags: algorithm array string
---

> LeetCode Top Interview 150의 80번 문제입니다.

<!--more-->

📚 목차
- [🌱 Reove Duplicates from Sorted Array 2](#-remove-duplicates-from-sorted-array-2)
  - [🟤 문제 요약 - Definition](#-문제-요약-definition)
  - [🟤 문제 풀이 전략 추상화 - Abstraction](#-문제-풀이-전략-추상화-abstraction)
  - [🟤 문제 풀이 - Algorithm](#-문제-풀이-algorithm)

----

## 🌱 [Remove Duplicates from Sorted Array 2](https://leetcode.com/problems/remove-duplicates-from-sorted-array-ii/description/?envType=study-plan-v2&envId=top-interview-150)

### 🟤 문제 정의 (Definition)

- 오름차순의 배열 nums를 파라미터로 주어지고 이 배열을 사용해야한다.
- nums 배열에는 중복된 element가 존재한다. (안할 수도 있다.)
- element는 오름차순을 유지하되, 중복된 element가 최대 2개가 존재하도록 nums를 재구성해야한다.
- nums를 재구성하고, 유효한 값을 모두 더한 값 K를 반환한다.


- 단! 주어진 array 안에서 재구성해야한다. 즉, 추가적인 배열 등의 공간을 생성하면 안된다.


- 조건
  - 1 <= nums.length <= 3 * 10^4
  - -10^4 <= nums[i] <= 10^4
  - nums is sorted in non-decreasing order.

---

### 🟤 문제 풀이 전략 추상화 (Abstraction)
해당 문제에서 제일 주의해야할 점은 `in-place`. 즉, 주어진 배열 내에서 element 스위칭 등 처리를 해야한다. 풀이에 힌트가 되는 점은 nums가 미리 오름차순으로 정렬되어 있다는 것이다.

- 오름차순으로 정렬된 nums 배열에서 순차적으로 저장해야하는 index 순서를 가리키는 지역변수 (index)
- 배열을 순환하면서 현재 어떤 값을 카운팅하는지를 저장하는 임시 값 저장 지역변수 (tempValue)
- 현재 tempValue가 얼마나 값을 가지고 있는지 카운팅하는 지역변수 (count)
- 위의 조건대로 중복된 값이 최대 2개까지만 남겨놓은 재구성된 nums의 유효한 값 - 반환값 (result)

총 4개의 지역변수를 사용하여 `in-place` 요구 조건을 충족시킨다.

...
<br />
<br />
해당 문제를 제일 빠르게 풀기 위해서는 O(N)의 시간복잡도를 가진다. nums의 모든 element 값을 조회하여 값이 유효한지의 여부를 
파악해야하기 때문이다.

이미 오름차순으로 정렬되어 있기 때문에 nums의 값을 순차적으로 조회하면서 카운팅한다. 만약 카운팅 값이 2를 넘지 않은 경우에는 
유효한 값이므로 삽입해야하는 nums의 index에 현재 값을 삽입한다. 또한, 유효한 값이므로 result에 1을 더한다.

하지만 count가 2를 넘어갔을 경우에는 더이상 해당 element는 유효하지 않기 때문에 카운팅만 해준다.

이후에 배열을 순환하면서 현재 트래킹하고 있는 값과 순환하는 현재의 값이 달라지게 되면 count를 0으로 초기화시키고 tempValue를 현재 값으로 
초기화시켜준다.

> 🥕 시간복잡도는 nums를 순환하는 시간인 O(N)가 쇼요된다.
> 
> 🥕 공간복잡도는 주어진 nums 배열 이외에 새로 추가한 가변 공간이 존재하지 않기 때문에 O(1)의 공간복잡도를 가진다. 

---

### 🟤 문제 풀이 (Algorithm)

```java
class Solution {
  public int removeDuplicates(int[] nums) {

    int index = 0;
	
    int tempValue = Integer.MIN_VALUE;
    int count = 0;

    int result = 0;

    for (int i = 0; i < nums.length; i++) {
      if (nums[i] == tempValue) {
        count++;
      } else {
        tempValue = nums[i];
        count = 0;
      }

      if (count < 2) {
        nums[index++] = nums[i];
        result++;
      }
    }

    return result;
  }
}
```

---
