---
title: "[백준] Brute Force 1- 18111번 마인크래프트"
date: 2022-11-28T14:48:17+09:00
draft: false
tags: ["bruteforce", "python"]
categories: ["problem_solving"]
showToc: true
UseHugoToc: true
comments: true
---

- 문제([link](https://www.acmicpc.net/problem/18111))

브루트포스 알고리즘으로 해결하는 문제입니다. 땅의 높이는 256블록을 초과할 수 없습니다. 층을 기준으로해서모든 경우의 수를 계산합니다. 층(target)과 같은 높이만큼 블록을 제거하거나 추가해서 (작업 최소 시간, 높이)를 구할 수 있습니다. 

## 풀이 방법

[1] 0~256층까지 반복
    [1-1] graph 좌표에 저장되어 있는 블록이 층 수보다 크거나 같으면 → 블록 제거, 인벤토리에 추가
    [1-2] graph 좌표에 저장되어 있는 블록이 층 수보다 작으면 → 블록 추가, 인벤토리에서 제거
    [1-3] 인벤토리 블록의 범위 안에서 작업했다면
        [1-3-1] 최소 시간이라면 Update (작업 최소시간, 높이)

## Code (python)

```python
import sys

n, m, b = map(int, sys.stdin.readline().split())
graph = [list(map(int, sys.stdin.readline().split())) for _ in range(n)]
answer = sys.maxsize
idx = 0

# 0층부터 256층까지 반복
for target in range(257):
    plus, minus = 0, 0

    for i in range(n):
        for j in range(m):

            # 블록이 층 수보다 크거나 같으면 -> 인벤토리에 추가
            if graph[i][j] >= target:
                plus += graph[i][j] - target

            # 블록이 층 수보다 작으면 -> 인벤토리에서 제거
            else:
                minus += target - graph[i][j]
    #인벤토리 블록 수 이상으로 제거하지 않았다면
    if plus + b >= minus:
        # 최저 시간이면
        if minus + (plus * 2) <= answer:
            answer = minus + (plus * 2)
            idx = target

print(answer, idx)
```

## 참고자료

- [[blog] [baekjoon] 백준 18111번(파이썬): 마인크래프트](https://fre2-dom.tistory.com/457)
- [[blog] [Python 3] BOJ - 18111 "마인크래프트"](https://peisea0830.tistory.com/3)
