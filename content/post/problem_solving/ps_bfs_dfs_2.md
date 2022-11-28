---
title: "[백준] DFS/BFS 3- 2667번 단지 번호 붙이기"
date: 2022-08-23T11:14:32+09:00
draft: false
category: ["algorithm"]
tags: ["백준", "DFS", "BFS", "python"]
categories: ["problem_solving"]
showToc: true
UseHugoToc: true
comments: true
---
## [백준] 2667번 단지 번호 붙이기
- ([문제 링크](https://www.acmicpc.net/problem/2667))

## 풀이 방법
graph에서 연결 요소(connected component)의 수를 찾고 연결 요소 안의 node 수를 카운트하는 문제입니다. deque로 BFS를 구현해서 해결했습니다. 

[0] graph와 (x,y) 좌표의 방문 여부를 표시하는 visited (list)를 생성합니다. 

[1] graph 전체를 순회하면서 graph(x,y) 값이 1인 경우에 bfs 함수를 실행합니다. 
- [1-1] (x,y)를 push한 queue를 생성합니다.
- [1-2] queue에서 원소를 pop 합니다.
- [1-3] pop한 원소를 기준값으로 해서 상하좌우를 살핍니다. 
만약 값이 1이고 아직 방문하지 않았다면 push 하고, 방문 표시합니다.
*이 과정은 빈 queue가 될 때까지 반복합니다.   

[2] bfs 함수가 실행된 횟수와 bfs 함수 내부에서 queue에 좌표를 push 할 때마다 count한 횟수를 출력합니다.

## Code (python)

```python
from collections import deque

def bfs(root):
    #단지 내의 apt 수 저장하는 변수
    cnt = 1
    queue = deque([root])
    #queue에 원소가 있는 동안
    while queue:
        #x,y pop
        x,y = queue.popleft()
        #(x,y)를 기준으로 상,하,좌,우 확인 
        for i in range(4):
            nx = x + dx[i]
            ny = y + dy[i]
            #만약 nx,ny가 범위값 밖에 있다면 continue
            if nx < 0 or ny < 0 or nx >= n or ny >= n:
                continue
            #graph(nx,ny)값이 1이고 아직 방문하지 않았다면
            if graph[nx][ny]==1 and not visited[nx][ny]:
                #방문 표시
                visited[nx][ny] = True
                #stack에 표시
                queue.append((nx,ny))
                #apt 수 증가
                cnt += 1

    return cnt

#입력
n = int(input())
graph,result = [],[]

for _ in range(n):
    line = input()
    graph.append([int(i) for i in line])

#방문 표시 리스트 생성
visited = [[False]*n for i in range(n)]

#상하좌우 이동을 위한 dx,dy 좌표
dx = [1, -1, 0, 0]
dy = [0, 0, -1, 1]

#graph의 모든 좌표에 대해서 확인
for x in range(n):
    for y in range(n):
        #만약 graph(x,y)값이 1이고 아직 방문하지 않았다면
        if graph[x][y] == 1 and not visited[x][y]:
            #방문 표시
            visited[x][y] = True
            #반환되는 단지 내의 apt 수를 저장
            apt_n = bfs((x,y))
            #result 리스트에 더함
            result.append(apt_n)

#오름차순으로 정렬
result.sort()
#총 apt 단지 수 출력
print(len(result))
#단지 내의 아파트 수 출력        
print(*result, sep='\n')
```