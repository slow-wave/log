---
title: "[백준] DFS/BFS 1- 1260번 DFS와 BFS"
date: 2022-08-12T22:29:58+09:00
draft: false
category: ["algorithm"]
tags: ["백준", "DFS", "BFS", "python"]
categories: ["algo-problem"]
showToc: true
UseHugoToc: true
comments: true
---
## [백준] 1260번 DFS와 BFS
- ([문제 링크](https://www.acmicpc.net/problem/1260))

기본적인 그래프 탐색 문제 입니다. DFS는 stack을 활용해서 구현하고, BFS는 queue를 활용해 구현합니다. 

방문할 수 있는 정점이 여러 개인 경우 숫자가 적은 것을 먼저 방문하라는 조건을 고려해야 합니다!

### 풀이 방법

- Graph

```markdown
<input>
4 5 1
1 2
1 3
1 4
2 4
3 4
```

위의 testcase로 만들어진 그래프의 모양은 다음과 같습니다.  
{{< figure src="/images/bfs_dfs_1/1.png">}}

- DFS 방식으로 그래프 탐색

{{< figure src="/images/bfs_dfs_1/2.png">}}

stack 자료구조에서 pop을 하면 나중에 들어온 것이 먼저 나옵니다. 

인접한 node를 push 할 때 내림차순 정렬해서(3 → 2) 숫자가 작은 node가 먼저 pop 되게 합니다.  

- BFS 방식으로 그래프 탐색

{{< figure src="/images/bfs_dfs_1/3.png">}}

queue에서 pop을 하면 처음에 들어온 것이 먼저 나갑니다. 

인접한 node를 push 하기 전에 오름차순 정렬해서(2 → 3) 숫자가 작은 node가 먼저 pop 되게 합니다. 

### Code (python)

```python
from collections import deque
import sys

#DFS
def DFS(graph, root, visited =[]):
    stack = [root] #stack을 생성하고 root push

    while stack:
        n = stack.pop() 
        if n not in visited:
            visited.append(n) #visited에 표시
            if n in graph:
                temp = list(set(graph[n]) - set(visited)) 
                temp.sort(reverse = True) #내림차순으로 졍럴 - stack의 Top에 숫자가 작은 것이 위치하게된다. 
                stack += temp #stack에 push
    return " ".join(str(i) for i in visited)

#BFS
def BFS(graph, root, visited = []):
    queue = deque([root]) #queue를 생성하고 root push

    while queue:
        n = queue.popleft()

        if n not in visited:
            visited.append(n) #visited에 표시
            if n in graph:
                temp = list(set(graph[n]) - set(visited))
                temp.sort() #오름차순으로 졍럴 - queue의 Bottom에 숫자가 작은 것이 위치하게된다. 
                queue += temp #queue에 push
    return " ".join(str(i) for i in visited)

input = sys.stdin.readline
#N,M,V 입력 받기
N, M, V = map(int,input().split())

#그래프 만들기 - 각 노드마다 연결된 노드를 표시해준다. 
graph = {}
for i in range(M):
    n1, n2 = map(int,input().split())
    graph[n1] = graph.get(n1,[]) + [n2]
    graph[n2] = graph.get(n2,[]) + [n1]

print(DFS(graph, V))
print(BFS(graph, V))
```