---
layout: post
title: "[LeetCode] 3. Longest Substring Without Repeating Characters"
subtitle: "algorithm"
categories: devlog
tags: algorithm array sliding-window
---

> LeetCode Top Interview 150의 3번 문제입니다.

<!--more-->

📚 목차
- [🌱 Longest Substring Without Repeating Characters](#-longest-substring-without-repeating-characters)
  - [🟤 문제 정의 - Definition](#-문제-요약-definition)
  - [🟤 문제 풀이 전략 추상화 - Abstraction](#-문제-풀이-전략-추상화-abstraction)
  - [🟤 문제 풀이 - Algorithm](#-문제-풀이-algorithm)

----

## 🌱 [Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/?envType=study-plan-v2&envId=top-interview-150)

### 🟤 문제 정의 (Definition)

- 파라미터로 문자열 하나가 주어진다.

- 이 문자열에서 인접한 문자들이 반복적으로 드러나지 않도록 한 제일 긴 문자를 반환하는 문제이다.


- 조건
  - 0 <= s.length <= 5 * 10<sup>4</sup>
  - s consists of English letters, digits, symbols and spaces.

---

### 🟤 문제 풀이 전략 추상화 (Abstraction)

문제에서 요구하는 것은 매우 간단하다. 하나의 문자열이 주어지고 그 문자열 안에서 파생될 수 있는 문자 중 가장 긴 문자의 길이를 반환하는 것이다. 이 때, 
파생될 수 있는 문자는 각 문자 안에서 중복되는 값이 없어야한다.

예를 들어, abcabcbb는 abc, bca, cab 등이 중복되지 않은 문자들을 담은 가장 긴 문자열이라고 볼 수 있다. 또한, bbbbb는 b가 중복되기 때문에 b가 가장 긴 문자열이 
되고, pwwkew는 wke가 가장 긴 문자열이 될 것이다.

...

문제는 간단해보이지만, 막상 코드로 구현해볼 때 막힐 수 있다. 이 문제는 sliding - window 와 Set 자료구조로 해결할 수 있는 문제이다.

먼저, 문제에서 요구하는 것은 각 제일 긴 문자열을 모두 반환하는 것이 아닌, 제일 긴 문자열의 길이를 반환하는 문제이다. 따라서 abcabcbb같이 제일 긴 문자열이 3개정도 있더라도 
모두 찾을 필요없이 하나만 찾으면 이후 문제를 해결할 수 있다. (물론 sliding-window를 사용하면서 같은 길이의 값들을 찾을 수 있다.)

문제에서의 핵심은 이어지는 문자열이 내부에서 중복되는 값이 있는지를 체크하는 것이다. abc가 있다면 ab, bc를 체크할 필요없이 a, b, c가 각각 있는지 체크하면 당연히 ab, bc 같은 
문자열도 허용되지 않는다. 그렇다면 슬라이딩 윈도우를 사용하여 점차 윈도우 크기를 늘려가며 검사하되 늘릴려는 인덱스에 접근했을 때 중복되는 값이 존재하면 반대로 윈도우를 줄이며 새로운 문자열을 
만들 수 있도록 조절하는 방법을 사용해야한다.

투포인터 방식으로 각 인덱스를 가리킬 포인터를 2개(a, b) 생성한다. b를 1씩 증가시키며 b 인덱스의 값이 Set 자료구조에 없는 값이라면 다시 1증가시키고 maxLength를 비교하여 긴 값으로 갱신시킨다. 
만약, b를 1증가시켰는데 해당 인덱스의 값이 현재 윈도우 안의 문자와 중복된다면 a를 1씩 증가시켜 중복되지 않을때따지 윈도우 크기를 감소시킨다.

이러한 매커니즘을 사용하여 해당 문제를 해결할 수 있다.

> 🥕 문제의 시간복잡도는 2개의 포인터가 N만큼 순환하며 비교하기 때문에 O(2N) => O(N)의 시간복잡도가 소요된다고 볼 수 있다.
> 
> 🥕 공간복잡도로는 Set이라는 가변공간을 사용하기 때문에 O(N)의 공간복잡도가 필요하다.

---

### 🟤 문제 풀이 (Algorithm)

```java
import java.util.*;

class Solution {
    public int lengthOfLongestSubstring(String s) {

        int left = 0;

        Set<Character> set = new HashSet<>();
        int maxLength = 0;

        for (int right = 0; right < s.length(); right++) {

            if (!set.contains(s.charAt(right))) {
                set.add(s.charAt(right));
                maxLength = Math.max(maxLength, right - left + 1);
            } else {
                while (set.contains(s.charAt(right))) {
                    set.remove(s.charAt(left++));
                }
                set.add(s.charAt(right));
            }
        }

        return maxLength;
    }
}
```
