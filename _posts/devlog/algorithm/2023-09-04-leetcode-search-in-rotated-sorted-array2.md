---
layout: post
title: "[LeetCode] 33. Search in Rotated Sorted Array"
subtitle: "algorithm"
categories: devlog
tags: algorithm binary-search
---

> LeetCode Top Interview 150의 33번 문제입니다.

<!--more-->

📚 목차
- [🌱 Search in Rotated Sorted Array](#-search-in-rotated-sorted-array)
  - [🟤 문제 정의 - Definition](#-문제-요약-definition)
  - [🟤 문제 풀이 전략 추상화 - Abstraction](#-문제-풀이-전략-추상화-abstraction)
  - [🟤 문제 풀이 - Algorithm](#-문제-풀이-algorithm)

----

## 🌱 [Search in Rotated Sorted Array](https://leetcode.com/problems/search-in-rotated-sorted-array/?envType=study-plan-v2&envId=top-interview-150)

### 🟤 문제 정의 (Definition)

- 파라미터로 1차원 배열 (nums)와 목표값(target)이 주어진다.
- 본래 오름차순으로 정렬되어 있는 배열을 인덱스 기준으로 회전시킨 배열이 nums이다.
- 회전이라 함은 [0, 1, 2, 3]이라는 배열이 있다면, 왼쪽으로 2 회전시켜 [2, 3, 0, 1]로 만든 배열이라는 의미이다.
- 위와 같은 특징을 가진 nums에서 target을 찾는데 target이 있다면 target의 인덱스 번호를 반환하고 없다면 -1을 반환하는 문제이다.


- 조건
  - 1 <= nums.length <= 5000
  - -10<sup>4</sup> <= nums[i] <= 10<sup>4</sup>
  - All values of nums are unique.
  - nums is an ascending array that is possibly rotated.
  - -10<sup>4</sup> <= target <= 10<sup>4</sup>

---

### 🟤 문제 풀이 전략 추상화 (Abstraction)

방법 1.
아주 간단한 방법으로 O(N)으로 순환하면서 target값이 있는지 확인할 수 있다.


방법 2.
하지만 이번 문제에서도 O(logN)의 시간복잡도로 문제를 풀도록 한다. 이번에는 역으로 사고해야한다.

이번 문제도 O(logN)의 시간복잡도를 사용해서 문제를 해결해야하기 때문에 이진 탐색을 사용한다. 이진탐색과 해당 문제를
해결하기 위한 문제 해결 방법을 적용하면 다음과 같다.

문제에서 주어지는 배열은 오름차순 1차원 배열이었지만, 어느 한 지점에서 회전하였기 때문에 완벽한 오름차순은 아니다. 하지만 한구간을 제외하고는
오름차순으로 정렬되어 있기 때문에 이 특징을 이용해야한다.

먼저 가운데 값을 비교하여 해당 값이면 그대로 가운데값의 인덱스를 반환하면 된다. 하지만 가운데값이 목표값이 아니라면 선택지가 2개로 나뉜다.
인덱스 mid를 기준으로 왼쪽과 오른쪽을 목표값과 비교하는 것이다. 배열 중 어느 한구간만 오름차순이 아니기 때문에 왼쪽 오른쪽 둘중 한 구간은 오름차순이다.
만일 목표값이 오름차순으로 되어 있는 구간 안에 포함되어 있는 값이라면 해당 구간에서 이진탐색하면 된다. 하지만, 목표값이 그 구간에 해당하지 않는다면 자연스럽게
나머지 구간 안에 목표값이 있다는 것이다.

예를 들어 [4, 5, 6, 7, 1] 중 목표값은 7이라고 한다면, 6을 기준으로 왼쪽과 오른쪽을 비교한다. 왼쪽은 4, 5로 정렬되어 있지만 7은 4, 5의 범위 안에 해당하지 않는다.
따러서 6의 오른쪽은 오름차순으로 정렬되어 있지는 않지만 목표값을 가지고 있는 밤위라는 의미가 된다. 그럼 오른쪽 범위를 기준으로 다시 이진탐색을 진행한다.

이 방법을 반복하면 값을 찾을 수 있으며, 해당값이 없는 경우 -1을 반환한다.

> 🥕 시간복잡도로는 O(logN)의 시간복잡도를 가진다.
>
> 🥕 공간복잡도는 재귀 Stack이 생성되기 때문에 O(logN)의 공간복잡도를 가진다.

---

### 🟤 문제 풀이 (Algorithm)

방법 1.
```java
class Solution {
    public int search(int[] nums, int target) {
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] == target) return i;
        }

        return -1;
    }
}
```

방법 2.

```java
class Solution {
    public int search(int[] nums, int target) {
        return binarySearch(nums, 0, nums.length - 1, target);
    }

    public int binarySearch(int[] list, int start, int end, int target) {
        if (start == end) {
            if (list[start] == target) return start;
            else return -1;
        }

        int mid = (start + end) / 2;

        if (list[mid] == target) return mid;


        if (list[mid] >= list[start]) {
            if (list[start] <= target && list[mid] > target) {
                return binarySearch(list, 0, mid - 1, target);
            } else {
                return binarySearch(list, mid + 1, end, target);
            }
        } else {
            if (list[mid] < target && list[end] >= target) {
                return binarySearch(list, mid + 1, end, target);
            } else {
                return binarySearch(list, 0, mid - 1, target);
            }
        }
    }
}
```

---
