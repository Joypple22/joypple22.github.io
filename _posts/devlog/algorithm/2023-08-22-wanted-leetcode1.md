---
layout: post
title: "[LeetCode] Array/String"
subtitle: "algorithm"
categories: devlog
tags: algorithm array string
---

> LeetCode Top Interview 150 문제 1-3번 문제입니다.

<!--more-->

- 목차
  - [🌱 1. Merge Sorted Array](#-1-merge-sorted-array)
    - [🟤 1. 문제 요약](#-1-문제-요약)
    - [🟤 1. 문제 풀이](#-1-문제-풀이)
    - [🟤 1. 풀이 한계 및 개선](#-1-문제-한계-및-개선)

  - [🌱 2. Remove Element](#-remove-element)
    - [🟤 2. 문제 요약](#-2-문제-요약)
    - [🟤 2. 문제 풀이](#-2-문제-풀이)
    - [🟤 2. 풀이 한계 및 개선](#-2-문제-한계-및-개선)
  
  - [🌱 3. Remove Duplicates from Sorted Array](#-3-remove-duplicates-from-sorted-array)
    - [🟤 3. 문제 요약](#-3-문제-요약)
    - [🟤 3. 문제 풀이](#-3-문제-풀이)

- [🌱 마무리](#-마무리)
----

## 🌱 1. Merge Sorted Array
[문제 링크](https://leetcode.com/problems/merge-sorted-array/?envType=study-plan-v2&envId=top-interview-150)

### 🟤 문제 요약
- Parameter
  - nums1, nums2은 배열
  - m, n은 각각 nums1, nums2의 element 중 유효한 값 (index 0 ~ m, 0 ~ n)
  - nums1은 0 ~ m 이외의 값들은 0으로 정렬해야하는 요소에서 제외된 값들이다. (nums1.length >= m)


- 요구 사항
  - nums1과 nums2의 유효한 값들을 하나의 배열에 합쳐 오름차순으로 정렬하기
  - return할 필요는 없으며 nums1의 배열에 위의 요구 사항이 반영되어야 한다.


### 🟤 1. 문제 풀이

방법 1.
```java
import java.util.*;

class Solution {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        for (int i = 0; i < n; i++) {
            nums1[i + m] = nums2[i];
        }

        Arrays.sort(nums1);
    }
}
```

간단하지만 시간복잡도 측면에서는 약간 더 소요되는 방법으로 구현해보았다.

1. nums1의 유효한 범위(index m 이상) 값에 nums2의 요소들을 추가한다.
2. 모두 추가한 이후, nums1를 통으로 Arrays.sort를 사용해서 정렬을 한다.

시간복잡도 측면에서는 처음 nums1에 nums2의 element를 insert할 때는 O(N) 의 사간복잡도를 가진다. 또한, Arrays.sort는 sort 메소드의 파라미터에 어떤 값을 
넣는지에 따라 다르지만 array 배열 하나를 넣었을 경우 quick sort 알고리즘을 사용하여 O(N logN)의 시간복잡도를 가진다.

그렇기 때문에 해당 솔루션으로 풀이했을 경우 O(N logN)의 시간복잡도를 가지게 된다.

공간복잡도를 고려해보자면, quick sort는 in-place 정렬 알고리즘이기 때문에 해당 배열을 제외한 추가적인 공간이 불필요하다. 즉, 주어진 배열 안에서 모든 정렬을 수행한다.
다만, quick sort는 재귀호출로 인해 logN만큼의 재귀호출을 하기 때문에 이를 위한 공간이 필요하다. 따라서 Arrays.sort에 필요한 공간복잡도는 O(log N)이다. 

### 🟤 1, 풀이 한계 및 개선
Arrays.sort API를 사용하면 매우 쉽게 해당 문제를 풀 수 있지만, 정렬할 때의 시간복잡도 적용되기 때문에 O(N)의 시간복잡도로 푸는 것이 더욱 효율적이다.

```java
import java.util.*;

class Solution {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        // Definition
        // 이 문제에서 주어지는 요구 사항은 다음과 같다.
        // 1. nums1 배열과 nums2 배열은 모두 오름차순으로 정렬되어 있다.
        // 2. nums1의 element들과 nums2의 element들을 하나의 배열에 오름차순으로 정렬해야한다.
        // 3. nums1에 2 배열의 오름차순 정렬된 element들이 있어야하며, 초기 nums1의 length는 반환해야하는 두 배열의 요소들의 합과 같다.
        
        // Abstraction
        // 이 문제를 풀기 위해서는 nums1에 두 배열의 모든 요소들을 오름차순으로 정렬해해서 반환해야한다.
        // 1. 먼저, nums1의 배열과 nums2의 배열을 오름차순으로 정렬할 수 있도록 값을 임시로 저장할 수 있는 array를 생성한다.
        // 2. 생성된 array에 nums1과 nums2의 element를 비교하며 조건에 만족하는 값을 insert한다.
        // 3. 생성된 array에 모든 요소들을 정렬한 상태로 insert하면 nums1에 값을 대치한다.

        // Algorithm
        List<Integer> list = new ArrayList<>();

        int oneIndex = 0;
        int twoIndex = 0;

        while (oneIndex != m && twoIndex != n) {
            if (nums1[oneIndex] < nums2[twoIndex]) {
                list.add(nums1[oneIndex]);
                oneIndex++;
            } else {
                list.add(nums2[twoIndex]);
                twoIndex++;
            }
        }

        while (oneIndex != m) list.add(nums1[oneIndex++]);
        while (twoIndex != n) list.add(nums2[twoIndex++]);

        for (int i = 0; i < list.size(); i++) {
            nums1[i] = list.get(i);
        }
    }
}
```

이 문제는 위의 Arrays.sort의 알고리즘을 사용하지 않고 O(N)의 시간복잡도를 가지는 풀이법이다.

1. 먼저, 빈 ArrayList 컬렉션을 만든다.
2. nums1과 nums2를 각각 비교하면서 값이 적은 순서대로 빈 리스트 컬렉션에 add한다.
3. nums1과 nums2의 유효한 값(index m, n)값을 모두 리스트 컬렉션에 넣었다면, nums1을 순환하며 리스트 컬렉션의 index 0부터 순서대로 교체한다.

> 🥕 시간복잡도 <br />
> 해당 풀이법에서는 각 배열을 순환하는 로직만 있을 뿐, 2차원 배열 등의 복잡한 시간복잡도는 존재하지 않는다.
> 따라서, nums1의 모든 값들 + nums2의 모든 값들을 조회하기 위해 O(m + n) => O(N)의 시간복잡도를 가진다.
> 
> 🥕 공간복잡도 <br />
> 해당 풀이의 공간복잡도는 list 컬렉션에 nums1과 nums2의 element들을 모두 add 해야하는 공간이 가변공간으로써 필요하다.
> 그렇기 때문에 해당 풀이의 공간복잡도는 O(m + n)이다.

----

## 🌱 2. Remove Element
[문제 링크](https://leetcode.com/problems/remove-element/description/?envType=study-plan-v2&envId=top-interview-150)

### 🟤 2. 문제 요약
- Parameter
  - 1차원 배열(nums)와 정수(int)를 파라미터로 받는다.

- 요구사항
  - 배열에 있는 element 중에서 정수 val와 동일한 값을 가진 element를 제거한다.
  - 제거한 이후 배열에 남아있는 element의 개수와 nums 배열에서 제거된 element를 제외한 나머지 값들을 index 0부터 insert해야한다.
  - ex) [3, 2, 2, 3] => [2, 2, _, _]
  - _ 값에는 어떤 값이 와도 무방하다.

### 🟤 2. 문제 풀이
이 문제를 풀 때도 간단하게 풀 수 있는 방법부터 먼저 고민하고 풀어보았다.

```java
import java.util.*;

class Solution {
    public int removeElement(int[] nums, int val) {

        int result = nums.length;

        for (int i = 0; i < nums.length; i++) {
            if (nums[i] == val) {
                nums[i] = 51;
                result--;
            }
        }

        Arrays.sort(nums);

        return result;
    }
}
```

1번 문제와 같이 sort API를 사용해서 문제를 풀어보았다.

문제의 전제 조건 중 `0 <= nums[i] <= 50`의 조건이 있었다. 즉, nums의 element는 모두 50을 넘지 않기 때문에 val 값에 해당하는 nums의 element는 모두 51로 바꿔주었다.
이후에 sort을 하면 51값으로 대체된 값은 제일 큰 값을 가지게 되었기 때문에 모두 배열의 맨 뒤로 정렬될 것을 생각하여 구현했다.

이 문제 풀이는 1번과 마찬가지로 Arrays.sort API를 사용하기 때문에 O(N logN)의 시간복잡도를 가진다. 즉, O(N)의 시간복잡도로 풀 수 있다면 더 효율적인 코드가 될 것이다.

### 🟤 2. 풀이 한계 및 개선
1번과 마찬가지로 더 빠른 시간복잡도 O(N)로 풀 수 있다면 더 빠르게 처리할 수 있다.

```java
class Solution {
	// Definition
    // 주어진 배열(nums)과 정수(val)가 있다.
    // nums에 val와 같은 값을 가지는 element는 모두 제거한다.
    // 제거된 element를 제외한 나머지의 개수를 반환하고 배열 또한 값을 제거한 나머지로 구성되도록 구현하기
  
    // Abstration
    // 1. nums 배열을 순환한다.
    // 2. nums 배열을 순환하면서 val과 같은 값이 있다면 continue, 값이 다르다면 변수(result)에 1씩 더한다.
    // 또한, index 변수를 만들어서 값이 중복되지 않은 경우에는 nums[index]에 해당 값을 insert하고 index를 1 더한다.
  
	// Algorithm
    public int removeElement(int[] nums, int val) {

        int result = 0;
        int idx = 0;

        for (int i = 0; i < nums.length; i++) {
            if (nums[i] != val) {
                nums[idx++] = nums[i];
                result++;
            }
        }
		
		int[] list = new int[100];

        return result;
    }
}
```

이 방법 또한 추가적인 로직 없이 단순히 nums를 순환하는 O(N) 시간복잡도를 통해서 해결한 풀이법이다.

시간복잡도는 O(N)이며, 공간복잡도는 nums 배열(크기가 고정된 array) 이외 다른 가변 공간을 필요로 하지 않기 때문에 O(1)의 공간복잡도를 가진다.

---

## 🌱 Remove Duplicates from Sorted Array
[문제 링크](https://leetcode.com/problems/remove-duplicates-from-sorted-array/?envType=study-plan-v2&envId=top-interview-150)

### 🟤 3. 문제 요약
- 조건
  - 1 <= nums.length <= 3 * 10^4
  - -100 <= nums[i] <= 100
  - num is sorted in non-decreasing order (오름차순)

- Parameter
  - nums 1차원 배열

- 요구조건
  - nums는 오름차순 정렬되어 있는 1차원 배열이다.
  - nums의 element 중 중복되는 값은 1개를 제외하고 모두 제거해야한다.
  - 제거된 element의 개수와 제거된 값이 nums 배열에 index 0부터 정렬되어 있어야한다.
  - ex) [1, 1, 2] => [1, 2, _]

### 🟤 문제 풀이
```java
class Solution {
    public int removeDuplicates(int[] nums) {
        // Definition
        // 1. 오름차순으로 정렬되어 있는 배열이 주어진다.
        // => 배열 안의 중복된 데이터를 제거하고 element는 유니크값으로 가질 수 있도록 한다.
        // 2. 삭제된 데이터를 제외한 나머지(유니크 값)의 합을 반환하는 문제

        // Abstraction
        // 1. 이미 배열은 오름차순으로 정렬된 상태이다.
        // 2. 수정할 index를 변수로 놓고, 각 값이 달라질 때마다 배열의 index에 해당 값을 insert한다.

        // Algorithm
        int index = 1;
        int result = 1;

        for (int i = 1; i < nums.length; i++) {
            if (nums[i - 1] != nums[i]) {
                result++;
                nums[index++] = nums[i];
            }
        }

        return result;
    }
}
```

앞의 1, 2번과 마찬가지로 O(N)의 시간복잡도로 해결할 수 있는 방법을 고민해보았다.

이미 nums는 오름차순으로 정렬되어 있었기 때문에 수정할 index를 가리키는 변수와 중복되지 않는 값을 더하는 result 변수를 두고 nums를 순환하면서 값을 구한다.

시간복잡도는 nums를 순환하는 로직 하나뿐이기에 O(N)를 가지고, 공간복잡도는 가변공간이 없고 주어진 nums 배열을 재구성하기 때문에 O(1)의 공간복잡도를 가진다.

---

### 🌱 마무리
사실상 공간복잡도 표기법만으로는 O(logN)보다 O(N)이 더 큰 공간을 차지할 수 있다고 생각했다. 하지만 LeetCode의 Memory 결과를 보고 그것이 완전하지 않다는 것을 알게 되었다.

공간복잡도는 상수값을 무시?하고 각 객체의 메모리와 구현 세부사항에 따라 다른 결과를 초래할 수 있기 때문에 적은 메모리에 하나하나 신경쓰는 것보다 코드의 가독성 및 유지보수에 조금 더
초점을 맞추는 것이 더 나은 방법이지 않을까 생각해본다.
