---
layout: post
title: "[LeetCode] 125. Valid Palindrome"
subtitle: "algorithm"
categories: devlog
tags: algorithm array two-pointer
---

> LeetCode Top Interview 150의 125번 문제입니다.

<!--more-->

📚 목차
- [🌱 Valid Palindrome](#-valid-palindrome)
  - [🟤 문제 정의 - Definition](#-문제-요약-definition)
  - [🟤 문제 풀이 전략 추상화 - Abstraction](#-문제-풀이-전략-추상화-abstraction)
  - [🟤 문제 풀이 - Algorithm](#-문제-풀이-algorithm)

- [🌱 풀이 개선](#-풀이-개선)

----

## 🌱 [Valid Palindrome](https://leetcode.com/problems/valid-palindrome/?envType=study-plan-v2&envId=top-interview-150)

### 🟤 문제 정의 (Definition)

- 파라미터로 문자(String)가 주어진다.
- 이 문자는 대문자, 소문자, 영어, 특수 문자 등이 조합되어 있다.

- 요구사항은 주어진 문자에서 대문자는 소문자로 모두 바꾸고 영문자와 숫자를 제외한 모든 문자는 제거한다.
- 위의 처리가 완료된 문자가 회문이면 true를 아니면 false를 반환한다.


- 조건
  - 1 <= s.length <= 2 * 10<sup>5</sup>
  - s consists only of printable ASCII characters.

---

### 🟤 문제 풀이 전략 추상화 (Abstraction)

문제에서 하라는대로 순서에 맞춰 진행하면 된다.

먼저 모든 영문자를 소문자로 변환한다. Java에서는 API 중에 toLowerCase()라는 String 메소드가 있다. 이것을 통해 모든 영대문자를 소문자로 변환한다.

이후, 영문자와 숫자가 아닌 값은 모두 제거해야한다. 이것을 구현하기 위해 먼저 Deque라는 자료구조를 활용한다. 문자열을 순환하며 영문자와 숫자가 아닌 값은 queue에 add하지 않고,
조건에 맞게 영문자와 숫자만 queue에 add한다.

Deque의 특징은 일반적인 Queue처럼 FIFO에 더해 Stack의 pop처럼 마지막에 add된 값도 참조할 수 있는 api가 주어진다. 이런 Deque의 특징을 활용하여 deque의 first값과 last값을 pop하여 
비교한다. 모든 값을 비교하여 각 값이 일치하면 true를, 아니면 false를 반환한다.

> 🥕 위의 시간복잡도는 모든 값들을 순환하면서 deque 알맞은 값을 넣는 O(N)과 deque에서 값들을 꺼내어 비교하는 로직 O(1/2 N), 총 O(3/2 N)의 시간복잡도 => O(N)의 시간복잡도가 필요하다.
> 
> 🥕 공간복잡도는 문자열을 split으로 나눈 값 O(N)과 queue의 크기 O(N) 총 O(2N) => O(N)의 공간복잡도가 필요하다.

---

### 🟤 문제 풀이 (Algorithm)
```java
import java.util.*;

class Solution {
    public boolean isPalindrome(String s) {

        String[] list = s.toLowerCase().split("");

        Deque<String> queue = new LinkedList<>();

        for (int i = 0; i < list.length; i++) {
            char[] cList = list[i].toCharArray();

            if ((cList[0] >= 'a' && cList[0] <= 'z') || (cList[0] >= '0' && cList[0] <= '9')) {
                queue.addLast(list[i]);
            }
        }

        while (queue.size() > 1) {
            String first = queue.removeFirst();
            String last = queue.removeLast();

            if (!first.equals(last)) return false;
        }

        return true;
    }
}
```
---

## 🌱 풀이 개선

위의 방법대로 무탈하게 풀 수 있었지만 leetcode 결과에서 runtime 시간을 보여주어 조금 더 빠르게 개선할 수 있다고 리포트를 보여주었다.


어느 부분에서 더 빠르게 계산할 수 있을까? 고민해보니 굳이 deque 자료구조를 사용해야하나? 라는 생각이 들었다. deque에 조건에 맞는 값을 넣는 시간을 제외하면 
O(N)의 시간이 걸리는 로직 하나를 제거할 수 있다. 비록 O(N)이지만, O(N)도 N의 개수가 많아지만 시간이 더 소요되는 것은 마찬가지이기 때문에 자료구조를 사용하지 않기로 해본다.

deque 자료구조를 사용하지 않는다면 시간복잡도, 공간복잡도 모두 이득을 볼 수 있게 되어 바로 적용해본다.

```java
class Solution {
    public boolean isPalindrome(String s) {
        if (s.length() == 1) return true;

        char[] list = s.toLowerCase().toCharArray();
		int left = 0;
		int right = s.length() - 1;

		while (left <= right) {

			while (left < s.length() && !compareChar(list[left])) left += 1;
			while (right >= 0 && !compareChar(list[right])) right -= 1;

            if (left == s.length() && right == -1) return true;
			if (list[left++] != list[right--]) return false;
		}

		return true;
    }

	public boolean compareChar(char c) {
		return (c >= 'a' && c <= 'z') || (c >= '0' && c <= '9');
	}
}
```

추가된 코드는 `if (left == s.length() && right == -1) return true;` 이 부분인 것 같다. 이 부분은 생각치도 못한 케이스 ".,"를 위해 만든 코드이다.

특수문자를 제외한 문자열은 ""이므로 true를 반환해야한다. 하지만 현재 deque를 사용하지 않고 투포인터를 사용하여 주어진 문자열에서 조건에 맞지 않는 문자열은 포인터를 ++하거나 --한다. 
".,"를 위해 작성한 코드를 생각해보면 동시적으로 left값은 s.length() 값이 되고, right는 -1이 된다. 이런 경우는 영문자가 쓰이지 않은 특수문자만 쓰인 케이스에 적용할 수 있어 추가하게 되었다. 
