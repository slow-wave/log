---
title: "[백준] DFS/BFS 2- 2178번 미로탐색"
date: 2022-08-19T17:11:21+09:00
draft: false
category: ["algorithm"]
tags: ["백준", "graph", "python"]
categories: ["Problem Solving"]
showToc: true
UseHugoToc: true
comments: true
---

## [백준] 2178번 미로탐색

- ([문제 링크](https://www.acmicpc.net/problem/2178))

## 풀이 방법

(1,1) ~ (N,M) 까지의 최단 경로를 구하는 문제이므로 BFS를 활용해서 구현합니다.
이 문제에서 BFS를 활용하여 구현하는 이유는 다음과 같습니다.

## Code (python)

```python

from collections import deque

def bfs(root):
    queue = deque([root]) #큐를 생성해서 root push

    while queue:
        x,y = queue.popleft() #pop - 기본 좌표가 나옴

        #상하좌우 이동
        for i in range(4):
            nx = x + dx[i]
            ny = y + dy[i]
            #좌표 밖을 벗어나면 넘어감
            if nx < 0 or ny < 0 or nx >= N or ny >= M:
                continue

            if graph[nx][ny] == 1: #만약 이동한 좌표의 값이 1이라면
                graph[nx][ny] = graph[x][y] + 1 #이동한 좌표의 값에 기본 좌표 값에 1을 더함
                queue.append([nx,ny]) #좌표를 queue에 push

    return graph[N-1][M-1] #도착 지점 좌표의 값을 리턴함

graph,root = [],[0,0]
N,M = map(int,input().split())

#graph 만들기
for i in range(N):
    line = input()
    graph.append([int(i) for i in line])

#좌표 상화좌우 이동을 위한 dx,dy
dx = [-1,1,0,0]
dy = [0,0,-1,1]

print(bfs(root))
```
