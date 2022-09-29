---
title: "[programmers] DFS/BFS 4- 순위"
date: 2022-09-06T14:23:31+09:00
draft: false
tags: ["programmers", "DFS", "python"]
categories: ["problem_solving"]
showToc: true
UseHugoToc: true
comments: true
---
## [programmers] 순위
- ([문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/49191))

이 문제는 그래프로 분류되어 있습니다. 어떻게 그래프로 접근해야하는지 아이디어가 생각나지 않아서 어려웠던 문제입니다. 구글링을 해봤을 때 플로이드 와샬(Floyd-Warshall) 알고리즘을 이용해서 구현을 하신 답안이 많았지만 DFS로 구현했습니다. 플로이드 와샬의 경우 각 정점에서 다른 모든 정점까지의 최단경로를 구할 수 있는 알고리즘인데 이보다는 DFS가 효율적이라고 생각했습니다. 실제로 플로이드 와샬의 시간 복잡도는 O(n^3)입니다. 

## 풀이 방법

```python
n = 5
results = [[4, 3], [4, 2], [3, 2], [1, 2], [2, 5]]
```

results의 정보를 가지고 확실한 순위를 알 수 있는 노드의 수를 찾아내는 문제입니다. 자기 자신을 제외하고 모든 노드에 대해서 승패 여부를 알 수 있을 때만 확실한 순위를 알 수 있습니다. 

만약 5번 노드가 1~4번 노드에 대해서 패배했다면 순위는 5위가 됩니다. 
만약 2번 노드가 1,3,4에게 패배하고 5에게 승리했다면 순위는 4위가 됩니다. 

따라서 주어진 정보를 가지고 노드 전체에 대한 승패를 표시하면 답을 구할 수 있습니다. 

자세한 풀이 과정은 다음과 같습니다. 
{{< figure src="/images/bfs_dfs_4/1.png">}}

최종 graph의 형태는 다음과 같습니다.
{{< figure src="/images/bfs_dfs_4/2.png">}}

확실한 순위를 알 수 있는 노드는 2,5번으로 총 2개 입니다. 
## Code (python)
```python
def dfs(graph):
    #1~N번 노드에 대해서
    for target in range(1,n+1):
        #target 노드가 이긴 노드들을 담음
        stack = [i for i, rst in enumerate(graph[target]) if rst == 1]
        
        while stack:
            lose = stack.pop()

            for i, rst in enumerate(graph[lose]):
                # a > b and b > c 면 a > c 임으로 이를 표시함.
                if not graph[target][i] and rst == 1:
                    graph[target][i], graph[i][target] = 1, -1
                    stack.append(i)
    return graph

def solution(n,results):
    answer = 0
    #승패를 기록하는 graph 생성
    graph = [[0]*(n+1) for _ in range(n+1)]
    
    #승패 기록
    for win, lose in results:
        graph[win][lose] = 1
        graph[lose][win] = -1
    
    graph = dfs(graph)

    for i in range(1,n+1):
        #자기 자신을 제외하고 모든 노드에 승패 표시가 되어있다면
        if graph[i][1:].count(0) == 1:
            answer += 1 
    return answer
```