---
layout: post
title: "[LeetCode] 74. Search a 2D Matrix"
subtitle: "algorithm"
categories: devlog
tags: algorithm binary-search
---

> LeetCode Top Interview 150의 74번 문제입니다.

<!--more-->

📚 목차
- [🌱 Search a 2D Matrix](#-search-a-2d-matrix)
  - [🟤 문제 정의 - Definition](#-문제-요약-definition)
  - [🟤 문제 풀이 전략 추상화 - Abstraction](#-문제-풀이-전략-추상화-abstraction)
  - [🟤 문제 풀이 - Algorithm](#-문제-풀이-algorithm)

----

## 🌱 [Search a 2D Matrix](https://leetcode.com/problems/search-a-2d-matrix/?envType=study-plan-v2&envId=top-interview-150)

### 🟤 문제 정의 (Definition)

- 파라미터로 2차원 배열과 목표값이 주어진다.
- 2차원 배열은 다음과 같은 특징을 가지고 있다.
  - 2차원 배열안에 있는 각각의 배열은 오름차순으로 정렬되어 있다.
  - 정렬된 배열 간의 규칙은 이전의 배열의 마지막 요소의 값이 이후의 배열 첫번째 요소보다 작다.

- 2차원 배열에 목표값이 있다면 true, 없다면 false를 반환하는 문제이다.

- O(log(m * n))의 시간복잡도로 문제를 풀어야한다.


- 조건
  - m == matrix.length
  - n == matrix[i].length
  - 1 <= m, n <= 100
  - -10<sup>4</sup> <= matrix[i][j], target <= 10<sup>4</sup>

---

### 🟤 문제 풀이 전략 추상화 (Abstraction)

이 문제는 이전에 풀었던 이진탐색 문제와 비슷하지만 약간 어렵다.

문제에서는 O(log(m * n))의 시간복잡도로 해결해야한다고 한다. 사실 이 시간복잡도가 가장 큰 힌트를 남겨주었다고 생각한다.

보통 정렬되어 있는 값에서 O(logN)으로 탐색을 하기 위해서는 이진 탐색이 사용된다. 여기에서 왜 log(m * n)처럼 n이 곱해질까? 
생각해보자.

여러 배열들이 하나의 배열에 저장되어 있고, 각각 배열마다 int 값들이 정렬되어 있다. 이 배열의 특징은 이전 배열의 값들과 이후 배열의 값들을 
배열의 순서대로도 오름차순으로 정렬할 수 있다는 것이다. 즉, 이진탐색으로 pivot 배열(가운데 배열)에 목표값이 없다면 2가지의 선택지를 가진다.

- 목표값이 pivot 배열의 첫번째 요소보다 작다면 목표값은 해당 배열의 왼쪽에 있다.
- 반대로 목표값이 pivot 배열의 마지막 요소보다 크다면 목표값은 해당 배열의 오른쪽 배열에 있다.
- (index 0 ~ index N 기준)

log(m)은 이진탐색 때 소요되는 시간이며, m에 n이 곱해진 것은 탐색 때마다 pivot 배열을 순환하며 목표값의 유무를 탐색해야하기 때문이다.

만일, 이렇게 다 비교했는데도 값이 없다면 모든 배열에 목표값이 없다는 것이므로 false를 반환한다.

> 🥕 시간복잡도는 O(log(m * n))이다.
> 
> 🥕 공간복잡도는 재귀로 호출하는 것 이외에 다른 추가적인 공간을 선언하여 할당하지 않았으므로 O(logN)이다.

---

### 🟤 문제 풀이 (Algorithm)

```java
class Solution {
    public boolean searchMatrix(int[][] matrix, int target) {
        
        return binarySearch(matrix, 0, matrix.length - 1, target);
    }

    public boolean binarySearch(int[][] list, int start, int end, int target) {

        if (start == end) {
            for (int i = 0; i < list[start].length; i++) {
                if (list[start][i] == target) return true;
            }

            return false;
        }

        int pivot = (start + end) / 2;

        boolean flag = false;
        for (int i = 0; i < list[pivot].length; i++) {
            if (list[pivot][i] == target) {
                return flag = true;
            }
        }

        if (target < list[pivot][0]) {
            flag = binarySearch(list, start, pivot, target);
        } else if (target > list[pivot][list[pivot].length - 1]) {
            flag = binarySearch(list, pivot + 1, end, target);
        }

        return flag;
    }
}
```
---
