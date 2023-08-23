---
layout: post
title: "[LeetCode] 189. Rotate Array"
subtitle: "algorithm"
categories: devlog
tags: algorithm array string
---

> LeetCode Top Interview 150의 189번 문제입니다.

<!--more-->

📚 목차
- [🌱 Rotate Array](#-rotate-array)
  - [🟤 문제 요약 - Definition](#-문제-요약-definition)
  - [🟤 문제 풀이 전략 추상화 - Abstraction](#-문제-풀이-전략-추상화-abstraction)
  - [🟤 문제 풀이 - Algorithm](#-문제-풀이-algorithm)

- [🌱 풀이 개선](#-풀이-개선)

----

## 🌱 [Rotate Array](https://leetcode.com/problems/rotate-array/description/?envType=study-plan-v2&envId=top-interview-150)

### 🟤 문제 정의 (Definition)

- 파라미터로 1차원 배열(nums)와 array의 요소들을 오른쪽으로 옮기는 횟수 k가 주어진다.

- 오른쪽으로 k번 옮겼을 때의 nums를 만드는 것이 이번 문제의 목표이다.


- 조건
  - 1 <= nums.length <= 10<sup>5</sup>
  - -2<sup>31</sup> <= nums[i] <= 2<sup>31</sup> - 1
  - 0 <= k <= 10<sup>5</sup>

- 추가조건
  - 공간복잡도 O(1)을 지키며 풀어보기

---

### 🟤 문제 풀이 전략 추상화 (Abstraction)

방법 1. 추가조건을 만족하지 않은 방법으로 문제를 풀어보기

자료구조 Queue를 활용하여 문제를 풀어보았다.

먼저 k번만큼 각 nums의 배열을 오른쪽으로 이동시키는 것을 생각해보아야 한다. [1, 2, 3, 4, 5, 6, 7]이 있는 상황에서 오른쪽으로 3번 이동시키면 
[5, 6, 7, 1, 2, 3, 4]가 될 것이다. 오른쪽으로 옮기면서 nums.length를 넘게되는 값들은 다시 왼쪽 index 0부터 시작하게 된다.

이 점에 착안하여 2개의 queue를 만들어보기로 했다. k번 오른쪽으로 옮기게 된다는 것은 배열의 index 마지막으로부터 k번째까지의 값이 왼쪽으로 옮겨진다는 의미이다. 
그렇기 때문에 첫번째 queue에는 nums.length - k ~ nums.length 까지의 값들을 queue에 넣는다. 두번째 queue에는 index 0부터 nums.length - k까지 두번째 queue에 
넣는다.

이를 통해 nums의 모든 값들은 2개의 queue안에 모두 저장한다. 마지막으로 첫번째 queue부터 nums 배열의 index 0부터 차례대로 remove 함수를 호출한다.(FIFO) 
첫번째 queue가 자신이 가지고 있는 값들을 모두 remove했다면, 이어서 두번째 queue를 nums에 아직 삽입되지 않은 index부터 차례대로 저장한다.


> 🥕 시간복잡도는 nums의 값을 두개의 queue에 차례대로 add하는 과정에서 O(N)이 발생한다. 또한, 다시 한번 nums를 순환하면서 queue에 저장했던 값들을 다시 
> 저장하는 과정에서 O(N), 총 O(2N) => O(N)의 시간복잡도가 발생한다.
> 
> 🥕 공간복잡도는 Queue라는 가변 공간이 2개 생성되었기 때문에 O(2N)의 공간복잡도가 발생했다고 볼 수 있다.

...

방법 2. 방법 1의 Queue 2개를 1개로 줄이기

공간복잡도를 조금이나마 줄여보고자 고민하게 된 방법이다. 방법 1에서 조금만 더 생각해보면 굳이 queue를 2개 만들어 k값을 기준으로 나누어 값들을 관리하지 않더라도 
index nums.length - k ~ nums.length 만큼의 값들을 먼저 queue에 넣고 이후에 index 0부터 k까지의 값들을 queue에 넣어 nums를 순환할 때 
한번에 pop을 하는 방법도 떠올랐다.

> 🥕 시간복잡도는 마찬가지로 O(2N)이 발생하게 된다.
>
> 🥕 공간복잡도는 Queue가 하나 줄어 O(N)이다.

---

### 🟤 문제 풀이 (Algorithm)
```java
import java.util.*;

class Solution {
    public void rotate(int[] nums, int k) {
		// index 0 ~ K까지
		Queue<Integer> queue1 = new LinkedList<>();

		// index K ~ nums.length 까지
		Queue<Integer> queue2 = new LinkedList<>();

		int extraK = nums.length - (k % nums.length);

		for (int i = 0; i < nums.length; i++) {
			if (i < extraK) queue1.add(nums[i]);
			else queue2.add(nums[i]);
		}

		int index = 0;
		for (int i = 0; i < nums.length; i++) {
			if (index < nums.length - extraK) nums[index++] = queue2.remove();
			else nums[index++] = queue1.remove();
		}
    }
}
```

...

```java
import java.util.*;

class Solution {
    public void rotate(int[] nums, int k) {
		// index 0 ~ K까지
		Queue<Integer> queue = new LinkedList<>();

		int extraK = nums.length - (k % nums.length);

        for (int i = extraK; i < nums.length; i++) {
            queue.add(nums[i]);
        }

        for (int i = 0; i < extraK; i++) {
            queue.add(nums[i]);
        }

		for (int i = 0; i < nums.length; i++) {
            nums[i] = queue.remove();
		}
    }
}
```
---

## 🌱 풀이 개선

문제는 해결했지만.. 아직 한개의 조건을 충족시키지 못했다. 바로 O(1)의 방법으로 푸는 방식이다. 아쉽게도 runtime 성능 또한 더 좋게 구현할 수 있다는 것을 
leetcode에서 알려준다. (위의 구현 방식은 전체 5ms가 소요되고, 제일 효율적인 방식은 0ms가 소요된다고 한다. 0ms..?)

어떻게 하면 성능을 더 개선할 수 있을지 고민하다가 도저히 떠오르지 않아 사람들은 어떻게 구현했는지 참고해보았다.

```java
class Solution {
    public void rotate(int[] nums, int k) {

        int redefineK = k % nums.length;

        reverse(nums, 0, nums.length - 1);
        reverse(nums, 0, redefineK - 1);
        reverse(nums, redefineK, nums.length - 1);
    }

    public void reverse(int[] list, int start, int end) {
        while (start <= end) {
            int temp = list[start];
            list[start++] = list[end];
            list[end--] = temp;
        }
    }
}
```

어떻게 이런 생각이 떠오를 수 있는지 궁금했다..ㅎㅎ 먼저 모든 값의 순서를 반전시킨다. 그러면 [1, 2, 3, 4, 5, 6, 7]에서 [7, 6, 5, 4, 3, 2, 1]의 순으로 
nums의 배열 내부 값이 바뀌게 된다. 그럼 여기서 7, 6, 5는 3번 오른쪽으로 갔을 경우 index 0부터 5, 6, 7 순으로 저장되어 있어야한다.

그래서 index 0부터 k번까지 다시 값을 반전시키고, k + 1번부터 nums.length까지 다시 값을 반전시키면..? [5, 6, 7, 1, 2, 3, 4]가 된다.

이 방법은 시간복잡도가 기존보다 약 2배정도 빠르다. 그 이유는 reverse 함수에서 index start와 end 값을 교체한 후 각각 start++, end--하기 때문이다. start와 end가 각각 증가, 감소하여 
만나게 되는 지점(index)은 (start + end) / 2 지점이다. nums의 전체를 순환했던 것과 달리(O(N)) reverse 함수는 O(1/2N)의 시간복잡도를 가진다.

공간복잡도 또한 추가적으로 생성하는 가변 공간 등이 없기 때문에 O(1)을 지키며 알고리즘을 구현할 수 있다.
