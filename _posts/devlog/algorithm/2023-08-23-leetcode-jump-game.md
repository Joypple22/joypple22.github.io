---
layout: post
title: "[LeetCode] 55. Jump Game"
subtitle: "algorithm"
categories: devlog
tags: algorithm array string
---

> LeetCode Top Interview 150의 55번 문제입니다.

<!--more-->

📚 목차
- [🌱 Jump Game](#-jump-game)
  - [🟤 문제 정의 - Definition](#-문제-요약-definition)
  - [🟤 문제 풀이 전략 추상화 - Abstraction](#-문제-풀이-전략-추상화-abstraction)
  - [🟤 문제 풀이 - Algorithm](#-문제-풀이-algorithm)

- [🌱 풀이 개선](#-풀이-개선)

----

## 🌱 [Jump Game](https://leetcode.com/problems/jump-game/description/?envType=study-plan-v2&envId=top-interview-150)

### 🟤 문제 정의 (Definition)

- 파라미터로 1차원 배열 (nums)이 주어진다.
- nums 배열에는 각 index에서 점프할 수 있는(다음 index로 넘어갈 수 있는) 최대 거리 수가 저장되어 있다.
- index 0부터 시작해서 각 index의 value에 맞추어 점프하여 last index에 도달할 수 있는지의 여부를 파악한다.
- 가능하면 true, 아니면 false를 반환한다.


- 조건
  - 1 <= nums.length <= 10<sup>4</sup>
  - 0 <= nums[i] <= 10<sup>5</sup>

---

### 🟤 문제 풀이 전략 추상화 (Abstraction)

방법 1. 재귀를 통한 다이나믹 프로그래밍(topDown)으로 풀어보기

재귀는 Stack과 비슷한 구조를 가지고 있다. 함수가 재귀적으로 호출되면 stack에 쌓여 stack 메모리를 모두 사용하게 되면 StackOverflow가 발생하는 것처럼 
index마다 점프하면서 하나의 루트를 종료까지 도달하면 다른 루트를 탐색하도록 한다. 이 때, 이미 다녀온 index는 다시 반복하여 불필요한 처리를 막기 위해 
dp 테이블을 생성한다. 각 인덱스에 방문하게 되면 dp 테이블에 true로 설정하여 이후 재귀 호출 시 먼저 dp 테이블에 해당 인덱스로 재귀 호출이 가능한지의 여부를 파악한다.


재귀함수에는 현재 위치 인덱스, 이전에 접근한 적이 있는지를 트래킹하는 dp 테이블, 앞으로 갈 수 있는 최대 거리만큼 다시 재귀적으로 호출하는 반복문이 있다.

```
public boolean recur(int[] list, boolean[] dp, int index) {
    if (index == list.length - 1) return true;
    else if (index > list.length) return false;

    boolean flag = false;

    if (!dp[index]) {
        for (int i = 1; i <= list[index]; i++) {
            if (recur(list, dp,index + i)) {
                flag = true;
                break;
            }
        }

        dp[index] = true;
    }

    return flag;
}
```

재귀에 꼭 필요한 요소는 재귀 함수를 종료할 수 있는 종료조건을 꼭 마련해야한다. 여기에서는 index가 last index에 접근하면 true를 반환하여 종료하거나 
index가 last index를 넘어가버리면 false를 반환하도록 했다.


> 🥕 시간복잡도는 각 재귀는 N번인 모든 index에 접근하여 처리할 수 있기 때문에 O(N)이며, 각 재귀함수에서 for문을 통한 반복문을 사용하기 때문에 한개의 재귀함수에서 O(N)의 
> 시간복잡도가 소요된다. 결과적으로 모든 index마다 재귀 * 하나의 재귀에서 처리되는 반복문 = O(N<sup>2</sup>)의 시간복잡도를 가진다.
> 
> 🥕 공간복잡도는 dp 테이블을 생성하는데 O(N)과 재귀함수를 호출하여 stack 영역에 생성되는 데이터 메모리 O(N)이 있기에 총 O(2N) -> O(N)이 된다.

...

방법 2. 반복문을 통한 다이나믹 프로그래밍(Bottom-Up)으로 풀어보기

컴퓨터가 처리하는 연산의 방식으로 인해 재귀 함수를 사용하여 처리하는 top-down 방식보다는 bottom-up 방식의 처리가 더 빠르다. 그래서 bottom-up 방식으로 
수정해서 구현해보았다.

```
public boolean canJump(int[] nums) {
    if (nums.length == 1) return true;
    
    boolean[] can = new boolean[nums.length];
    can[0] = true;

    for (int i = 0; i < nums.length; i++) {
        if (!can[i]) continue;

        // 현 위치에서 점프 최대 값
        int jump = nums[i];

        // 현재 위치에서 jump를 했을 때 nums.length를 넘기면 true
        if (i + jump >= nums.length - 1) return true;

        // 아니라면 점프 가능한 위치 캐싱
        for (int k = i; k <= i + jump; k++) {
            can[k] = true;
        }
    }

    return false;
}
```

방법 1과 궁극적인 방식은 유사하다. 단지, 반복문을 사용하여 처리하는 것이 크게 다르다.

먼저 현재 위치한 인덱스가 점프를 할 수 있는 인덱스인지의 여부를 조회한다. 방법 1에서는 DFS같이 먼저 하나의 루트를 조사하고 다른 루트를 조사하기 때문에 
방문했던 위치를 캐싱해야했다면, 방법 2에서는 BFS와 유사하게 현재 자신이 갈 수 있는 인덱스를 캐싱하고 갈 수 있는 영역만 로직 처리를 진행하도록 했다.

이후 점프할 수 있는 최대 거리와 현재 위치를 더한 값이 last index보다 크거나 같다면 true를 반환한다. 아니라면 현재 위치에서 접근할 수 있는 이후의 인덱스를 캐싱하여 
다음에 유효한 처리를 할 수 있도록 한다.

> 🥕 시간복잡도는 방법 1과 마찬가지라고 생각한다. 2개의 for문에서 하나는 모든 nums의 배열을 순환하는 O(N)이며, 다른 하나는 jump할 수 있는 최대 거리까지 
> 반복하기 때문에 최대 O(N)의 시간복잡도를 가진다. 2중 for 문이므로 O(N<sup>2</sup>) 시간복잡도를 가진다.
> 
> 🥕 공간복잡도로는 재귀 stack이 사라지고 dp 테이블만 사용하기 때문에 O(N)의 시간복잡도를 가진다.


> ❗️방법 1과 방법 2는 시간복잡도가 유사하지만 leetcode에서 걸리는 runtime를 살펴보면 첫번째 풀이는 약 330ms 정도 소요되고, 방법 2는 
> 약 50ms를 소요한다. 재귀를 통한 실질적 runtime 시간이 생각보다 차이를 일으키는 것을 확인할 수 있었다.
> 
> <br />
> 재귀함수를 사용하면 JVM에서 내부적으로 처리하는 과정이 더해진다. JVM의 Runtime Data Area에는 JVM Stack 영역이 있다. 이 영역은 함수를 처리하면서 
> 현재 함수의 메타데이터(지역 변수, 파라미터 등)을 저장하는 목적으로 사용되어진다. 함수가 재귀적으로 호출되면 이 JVM Stack 영역에 쌓이게 되는 함수 데이터들이 
> 늘어나게 된다. 즉, 함수를 재귀적으로 호출하여 새로운 메타데이터를 가진 함수를 Stack 영역에 저장하고 이후에 처리가 완료된 재귀함수는 제거해야하는 등 보이지 않는 
> 처리들을 수행하기 때문에 Bottom-up 방식의 dp 알고리즘보다 Top-Down 방식의 dp 알고리즘이 조금 더 runtime 시간이 소요된다.

---

### 🟤 문제 풀이 (Algorithm)

```java
class Solution {
    public boolean canJump(int[] nums) {
        
        boolean[] isVisit = new boolean[nums.length];
        return topDown(nums, isVisit, 0);
    }

	public boolean topDown(int[] list, boolean[] dp, int index) {
		if (index == list.length - 1) return true;
		else if (index > list.length) return false;

		boolean flag = false;

		if (!dp[index]) {
			for (int i = 1; i <= list[index]; i++) {
				if (topDown(list, dp,index + i)) {
					flag = true;
					break;
				}
			}

			dp[index] = true;
		}

		return flag;
	}
}
```

...

```java
class Solution {
    public boolean canJump(int[] nums) {
        if (nums.length == 1) return true;
        
        boolean[] can = new boolean[nums.length];
        can[0] = true;

        for (int i = 0; i < nums.length; i++) {
            if (!can[i]) continue;
			
            int jump = nums[i];
			
            if (i + jump >= nums.length - 1) return true;
			
            for (int k = i; k <= i + jump; k++) {
                can[k] = true;
            }
        }

        return false;
    }
}
```

---

## 🌱 풀이 개선

위의 방법보다 더욱 빠르게 개선할 수 있는 방법이 있다. 어떤 알고리즘도 사용하지 않고 논리적 사고로 풀 수 있는..!

```java
class Solution {
    public boolean canJump(int[] nums) {
        int reachable = 0;

        for (int i = 0; i < nums.length; i++) {
            if (reachable < i) return false;
            else reachable = Math.max(reachable, i + nums[i]);
        }

        return true;
    }
}
```

nums 배열을 순환하면서 현재 자신이 갈 수 있는 최대 index 거리를 갱신하면서 비교하면 아주 쉽고! 빠르고! 효율적으로 풀 수 있다.

생각해보면 이 문제는 오직 last index에 접근할 수 있는지, 없는지의 여부만 판단하면 되기 때문이다. 현재 자신이 갈 수 있는 거리보다 현재 
조회해야하는 인덱스의 값이 더 작다면 현재 조회해야하는 인덱스에 접근할 수 없다는 의미가 된다. 이런 논리적 근거를 바탕으로 시간복잡도 O(N), 
공간복잡도 O(1)로 문제를 해결할 수 있다.
