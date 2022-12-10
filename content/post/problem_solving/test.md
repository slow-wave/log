---
title: "[백준] DP 1 - 2579번 계단 오르기"
date: 2022-12-10T16:54:24+09:00
draft: false
tags: ["DP", "python"]
categories: ["problem_solving"]
showToc: true
UseHugoToc: true
comments: true
---

- 문제([link](https://www.acmicpc.net/problem/2579))

DP로 해결하는 문제입니다. 

## 풀이 방법

- 기본 조건 : 계단의 개수는 300이하의 자연수

```python
arr = [0 for i in range(301)]
dp = [0 for i in range(301)]
```

- 계단 오르기 규칙

```markdown
1)1칸 or 2칸 오르기 가능

2)연속 3칸 밟기 불가능

3)마지막 칸 반드시 밟아야 함.
```

→ DP는 큰 문제를 작은 문제로 나누어 푸는 문제임. 

어떤 큰 문제가 있을 때 그것의 가장 작은 문제부터 생각해야함. 

→ 마지막 칸은 반드시 밟아야 하므로 다음과 같은 두 개의 경우의 수 존재함. 

```markdown
| END-3 | END-2 | END-1 | END |

- [case 1] (END-3) ➡️  (END-1) ➡️ (END)
- [case 2] (END-2) ➡️ (END)
```

→ case1 or case2 중 max값 찾기. 

```python
 dp[i] = max(dp[i-3] + arr[i-1] + arr[i], dp[i-2] + arr[i])

```

---

## Code (python)

```python
import sys

n = int(sys.stdin.readline())

arr = [0 for i in range(301)]
dp = [0 for i in range(301)]

for i in range(n):
    arr[i] = int(sys.stdin.readline())

# 0~2까지 초기값 설정
dp[0] = arr[0]
dp[1] = max(arr[0] + arr[1], arr[1])
dp[2] = max(arr[0] + arr[2], arr[1] + arr[2])

#각 step까지의 최댓값 구하기
for i in range(3, n):
    dp[i] = max(dp[i-3] + arr[i-1] + arr[i], dp[i-2] + arr[i])

#마지막 계단의 최댓값 출력
print(dp[n-1])
```

## 출처

- [blog] [[백준] 2579번(python 파이썬)](https://pacific-ocean.tistory.com/149)
- [blog] [[백준] 2579번 계단오르기 파이썬 해설](https://sungmin-joo.tistory.com/18)