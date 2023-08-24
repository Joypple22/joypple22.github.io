---
layout: post
title: "[LeetCode] 122. Best Time to Buy and Sell Stock II"
subtitle: "algorithm"
categories: devlog
tags: algorithm array string
---

> LeetCode Top Interview 150의 122번 문제입니다.

<!--more-->

📚 목차
- [🌱 Best Time to Buy and Sell Stock II](#-best-time-to-buy-and-sell-stock-ii)
  - [🟤 문제 정의 - Definition](#-문제-요약-definition)
  - [🟤 문제 풀이 전략 추상화 - Abstraction](#-문제-풀이-전략-추상화-abstraction)
  - [🟤 문제 풀이 - Algorithm](#-문제-풀이-algorithm)

- [🌱 풀이 개선](#-풀이-개선)

----

## 🌱 [Best Time to Buy and Sell Stock II](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/description/?envType=study-plan-v2&envId=top-interview-150)

### 🟤 문제 정의 (Definition)

- 파라미터로 1차원 배열 (prices)가 주어진다.
- prices 배열은 각 index가 날짜이고 그 날짜 index에 대한 값은 해당 날짜의 특정 주식에 대한 가격을 의미한다.

- 최대 1개의 주식을 보유할 수 있으며, 언제든지 매입과 매도를 할 수 있다.
- 매입과 매도를 적절하게 하여 주어진 날짜(prices.length)안에서 최대한 많은 이익을 내야한다.


- 조건
  - 1 <= prices.length <= 3 * 10<sup>4</sup>
  - 0 <= prices[i] <= 10<sup>4</sup>

---

### 🟤 문제 풀이 전략 추상화 (Abstraction)

방법 1. 다이나믹 프로그래밍의 DP 테이블을 활용한 방법

문제에서 요구하는 것은 언제든지 매도, 매입할 수 있는 단 한개의 주식을 주어진 날짜(nums 배열)와 날짜 해당하는 가격을 참고하여 
최대 이익을 반환하는 것이다.

먼저 주어진 prices 배열을 순환한다. 배열을 순환하면서 현재 날짜(index)에 매입을 하면 이후의 날짜에서 매도하게 되면 얼만큼의 이익이 발생하는지 
먼저 dp 테이블에 캐싱한다. 만일 수익이 마이너스(오늘 7원인데 내일 3원으로 하락해서 수익이 마이너스가 되는 경우)라면 어제까지 발생된 수익을 
넘겨받는다. (이후의 날짜에 매입 및 매도를 하지 않게 되면 그 전날까지 있었던 이익이 계속 이어지기 때문!)

또한 dp 테이블에 수익을 캐싱할 때 이미 수익이 잡혀있는 부분이 있을 것이다. 특정 시기에 매입해서 그날 매도하고 난 이익이다. 이 금액과 
현재 비교하고 있는 날짜와 특정 날짜와의 수익의 합을 비교하여 더 큰 값으로 갱신한다.

정리해보자면, 매입하는 현재 날짜 (index a)를 기준으로 매도할 a 이후의 날짜와 비교하면 2가지의 선택이 있다.

- a 이후의 날짜와 비교할 때 수익이 발생하게 되면 그 전날에 발생한 수익과 매입한 a 날짜(현재)와 매도한 날짜의 수익을 비교하여 더 큰 값을 캐싱한다.
- 만약 수익이 마이너스라면 전날에 발생한 수익과 현재 캐싱되어 있는 수익을 비교하여 더 큰 값으로 갱신한다.

위의 선택을 prices 배열을 순환하면서 처리하는 알고리즘으로 구현하였다.

> 🥕 시간복잡도는 prices의 배열을 모두 순환해야하기 때문에 O(N)이 기본으로 발생하고, 반복문 내에 다시 현재 날짜 이후의 모든 날짜와 
> 매도값을 조회하여 dp 테이블을 갱신하기 위해 O(N)의 시간복잡도가 필요하다. 그래서 총 O(N<sup>2</sup>)의 시간복잡도가 발생한다.
> 
> 🥕 공간복잡도는 가격을 갱신시킬 dp 테이블 배열이 하나 추가되어 O(N)의 공간복잡도를 가진다.

---

### 🟤 문제 풀이 (Algorithm)

```java
class Solution {
    public int maxProfit(int[] prices) {
        
		int[] amount = new int[prices.length];

		for (int i = 0; i < prices.length; i++) {
			int curPrice = prices[i];

			for (int k = i + 1; k < prices.length; k++) {
				int nextPrice = prices[k];

				int revenue = nextPrice - curPrice;

				if (revenue > 0) {
					amount[k] = Math.max(amount[k], amount[i] + revenue);
				} else {
					amount[k] = Math.max(amount[k - 1], amount[k]);
				}
			}
		}

        return amount[prices.length - 1];
    }
}
```

---

## 🌱 풀이 개선

더 간단한 풀이 방법이 존재한다. 해당 방법은 조금만 더 고민해보고 논리적인 사고를 통해 구현할 수 있는 방법이라고 생각한다.

```java
class Solution {
    public int maxProfit(int[] prices) {

        int result = 0;

        for (int i = 1; i < prices.length; i++) {
            if (prices[i] - prices[i - 1] > 0) {
                result += prices[i] - prices[i - 1];
            }
        }

        return result;
    }
}
```

코드만 보더라도 의도가 보인다. 어제 매입한 주식이 오늘 올랐으면 바로 매도하는 것이다. 또한, 내일 매도가가 오늘 매입가보다 비싸다면 
오늘 다시 매입하고 내일 다시 매도하는 방법이다. 이 방법이 어떻게 문제 풀이에 적용될 수 있을까 고민해보았다.

생각보다 단순했다. 오늘이 최저 매입가이고 내일이 최고 매도가라면 당연히 최대 수익은 내일 가격 - 오늘 가격이 된다. 이 방법으로만 생각하면 내일이 최고 매도가이든, 
내일 모래가 최고 매도가이든 최대 수익은 동일하다. 하지만, 매입, 매도를 여러번 할 수 있다면? 오늘 매입하고 내일 가격이 4가 올라 팔면 수익은 4가된다. 그런데 
내일 모래 가격이 다시 2로 하락하고 그 다음날 다시 매도가가 4로 올랐다면? 내일 모래 다시 매입하고 그 다음날 매도하여 2라는 수익을 추가로 낼 수 있다. 

핵심은 조그만 이익이 생기면 바로 매도하고 기존보다 가격이 하락하는 날에 다시 매입하여 다시 매도한다면 적어도 최소 매입가와 최대 매도가의 차이만큼은 확정적으로 
수익을 낼 수 있다.

> 🥕 이 풀이의 시간복잡도는 O(N)이며, 공간복잡도는 O(1)이다.
