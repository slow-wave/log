---
title: "[알고리즘] DFS vs. BFS"
date: 2022-08-10T21:50:38+09:00
draft: false
tags: ["BFS","DFS","Algorithm"]
showToc: true
UseHugoToc: true
comments: true
---

# Graph Search
graph search 방법에는 DFS, BFS 2가지 종류가 있다. 

## DFS(Depth-First-Search)

### 정의

root node 혹은 다른 임의의 node에서 시작해서 다음 분기(branch)로 넘어가기 전에 해당 분기를 완벽하게 탐색하는 방식이다. 

binary tree를 순회할 때 사용했던 다음의 순회 방법이 DFS에 속한다. 

- inorder
- preorder
- postorder

### 구현 방법

1) stack을 이용해서 구현

- 처음에는 스택에 노드가 없으니깐 시작할 노드를 넣는다.
- stack에서 노드를 하나 꺼내서 해당 node의 child node를 전부 스택에 넣고 꺼낸 노드는 출력한다.
- child node를 stack에 넣을 때 한번 stack에 넣었던 node는 다시 넣지 않는다.

2) recursion 이용한 순회 방법 (DFS의 경우 재귀 호출을 이용하면 코드가 간결해진다.)

- 노드에 방문하면 데이터를 출력하고 자식들을 순서대로 재귀호출한다.
- 자식들이 호출받으면 자기를 출력하고 자식들을 호출
    - 만약 재귀호출을 다녀와서 출력하면 자식들이 먼저 출력하니깐 거꾸로 올라가게됨.
- 자식이 1개 이상인 경우
    - stack은 쌓고 나서 호출 → 자식 중에서 나중에 들어간 node가 먼저 출력된다.
    - 재귀 호출은 정방향으로 출력하기 때문에 다르다.
        - 연결 관계를 입력할 때 어떤 노드와의 입력관계를 먼저 입력했느냐로 결정된다. (예시 - L 먼저 입력 됐다고 가정)

### 활용

- 모든 노드를 방문 할 때, 완전 탐색을 할 때 사용한다.

### 시간 복잡도

## BFS(Breadth-First-Search)

### 정의

Root node 혹은 다른 임의의 node에서 시작해서 인접한 node를 먼저 탐색하는 방법이다.

## 구현 방법

queue를 이용해서 구현한다. 

- 처음에는 queue에 node가 없으니깐 시작할 node를 넣는다.
- queue에서 node를 하나 꺼내서 해당 node의 child node를 전부 스택에 넣고 꺼낸 노드는 출력한다.

### 활용

- 최단 경로를 찾을 때 사용한다.

(가중치가 0,1인 경우에만 활용할 수 있다.)
