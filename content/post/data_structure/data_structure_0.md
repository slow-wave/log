---
title: "[data structure] 자료구조 정리"
date: 2022-10-12T17:04:59+09:00
draft: false
tags: ["자료구조"]
categories: ["Data Structure"]
showToc: true
UseHugoToc: true
comments: true
---

## 0. Intro

Data Structure는 데이터를 저장하고 구성하는데 사용하는 저장소입니다. 데이터에 효율적으로 접근하고 업데이트 할 수 있도록 컴퓨터에 데이터를 정렬하는 방법입니다.

{{< figure src="/images/data_structure/data_structure_0/0.png">}}

Classification of Data Structure ([출처](https://www.geeksforgeeks.org/data-structures/))

Linear data structure란 데이터 요소가 순차적 또는 선형으로 배열되고 각 요소가 이전 및 다음 요소에 연결된 데이터 구조입니다. linear data structure에는 static과 dynamic 데이터 구조가 있습니다. static data structur의 경우 메모리 크기가 고정되어있고, dynamic data structure의 경우 메모리 크기가 고정되어있지 않습니다.

Non-linear data structure란 데이터 요소가 순차적 또는 선형으로 배치되지 않은 데이터 구조입니다. 이 구조에서는 단일 실행으로 모든 요소를 순회할 수 없습니다.

## 1. stack

## 2. queue

## 3. graph

graph는 Nodes/Vertices(정점)와 Edges(간선)로 구성된 비선형 데이터 구조입니다. 실생활에서는 네트워크를 나타내는데 사용됩니다.

### 0) graph 관련 용어

- adjacent vertex(인접 정점) : 간선에 의 해 직접 연결된 정점
- degree(정점의 차수) : 무방향 그래프에서 하나의 정점에 인접한 정점의 수
  무방향 그래프에 존재하는 정점의 모든 차수의 합 = 그래프의 간선 수의 2배
- in-degree : 방향 그래프에서 외부에서 오는 간선의 수 (내차수 라고도 부름)
- out-degree : 방향 그래픙에서 외부로 향하는 간선의 수 (외차수 라고도 부름)
  방향 그래프에 있는 정점의 진입 차수 또는 진출 차수의 합 = 방향 그래프의 간선의 수(내차수 + 외차수)
- path length(경로 길이) : 경로를 구성하는 데 사용된 간선의 수
- simple path (단순 경로) : 경로 중에서 반복되는 정점이 없는 경우
- cycle : 단순 경로의 시작 정점과 종료 정점이 동일한 경우

### 1) graph의 구성 요소

- Nodes/Vertices(V)

  - 그래프의 기본 단위입니다.

- Edges(E)
  - edge를 그리거나 그래프의 두 노드를 연결하는데 사용합니다.

### 2) graph 구현

- Adjacency Matrix

  - V x V 크기의 2D 배열임.
  - 공간을 많이 차지한다는 단점이 있음. O(V^2)
  - 예시 그림
    {{< figure src="/images/data_structure/data_structure_0/8.png">}}

  - code
    ```python
    if __name__ == '__main__':
        #  n is the number of vertices
        #  m is the number of edges
        n, m = map(int, input().split())
        adjMat = [[0 for i in range(n)]for j in range(n)]
        for i in range(n):
            u, v = map(int, input().split())
            adjMat[u][v] = 1
            adjMat[v][u] = 1
    ```

- Adjacency List

  - 배열이 사용되며, 배열의 크기는 node의 수와 같음.
  - 예시 그림
    {{< figure src="/images/data_structure/data_structure_0/9.png">}}
  - code

    ```python
    class AdjNode:
        def __init__(self, data):
            self.vertex = data
            self.next = None


    # A class to represent a graph. A graph
    # is the list of the adjacency lists.
    # Size of the array will be the no. of the
    # vertices "V"
    class Graph:
        def __init__(self, vertices):
            self.V = vertices
            self.graph = [None] * self.V

        # Function to add an edge in an undirected graph
        def add_edge(self, src, dest):
            # Adding the node to the source node
            node = AdjNode(dest)
            node.next = self.graph[src]
            self.graph[src] = node

            # Adding the source node to the destination as
            # it is the undirected graph
            node = AdjNode(src)
            node.next = self.graph[dest]
            self.graph[dest] = node

        # Function to print the graph
        def print_graph(self):
            for i in range(self.V):
                print("Adjacency list of vertex {}\n head".format(i), end="")
                temp = self.graph[i]
                while temp:
                    print(" -> {}".format(temp.vertex), end="")
                    temp = temp.next
                print(" \n")


    # Driver program to the above graph class
    if __name__ == "__main__":
        V = 5
        graph = Graph(V)
        graph.add_edge(0, 1)
        graph.add_edge(0, 4)
        graph.add_edge(1, 2)
        graph.add_edge(1, 3)
        graph.add_edge(1, 4)
        graph.add_edge(2, 3)
        graph.add_edge(3, 4)

        graph.print_graph()

    # This code is contributed by Kanav Malhotra
    ```

### 3) graph의 종류

{{< figure src="/images/data_structure/data_structure_0/1.png">}}

- Null Graph : edge가 없는 경우
- Trivial Graph : 1개의 vertex만 있고 edge가 없는 경우

{{< figure src="/images/data_structure/data_structure_0/2.png">}}

- Undirected Graph : 무방향 그래프
- Directed Graph : 방향 그래프

{{< figure src="/images/data_structure/data_structure_0/3.png">}}

- Connected Graph : 모든 node가 연결되어 있음.
- Disconnected Graph : 1개의 node라도 끊어진 부분이 있음.

{{< figure src="/images/data_structure/data_structure_0/4.png">}}

- Regular Graph : 모든 노드의 degree가 그래프의 다른 노드와 동일한 그래프
- Complete Graph : 각 노드끼리 연결이 되어있는 그래프

{{< figure src="/images/data_structure/data_structure_0/5.png">}}

- Cycle Graph : 각 vertex의 degree가 2인 경우임. 그래프 전체가 순환됨.
- Cyclic Graph : 1개의 cycle이라도 존재하는 경우를 뜻함.

{{< figure src="/images/data_structure/data_structure_0/6.png">}}

- Directed Acyclic Graph : cycle이 없는 방향 그래프
- Bipartite Graph : 각 집합의 꼭짓점에 두 집합 사이의 간선이 포함되지 않도록 꼭짓점을 두 집합으로 나눌 수 있는 그래프

{{< figure src="/images/data_structure/data_structure_0/7.png">}}

- Weighted Graph : 가중치가 존재하는 그래프. 무방향/방향 가중치 그래프가 존재함.
  - 방향을 가진 그래프를 network라고 함.

### 4) graph의 활용

- 컴퓨터의 제어 흐름을 나타내는데 사용
- OS에서는 resource allocation graph로 사용
- 경로 최적화, 최단 경로를 찾는데 활용됨
- P2P(Peer to Peer) application용 컴퓨터 네트워크에서 사용
- DAG(Directed Acyclic Graph)는 가상화폐 기술에도 적용

### 5) graph의 장단점

- 장점
  - 최단경로, 노드의 이웃을 쉽게 찾을 수 있음
  - DFS, BFS, MST와 같은 알고리즘을 구현하는데 사용
  - 비선형 구조로 인해 복잡한 문제와 시각화를 이해하는데 도움이 됨.
- 단점
  - 메모리 복잡도가 클 수 있음.
  - 그래프의 곱셈 어려움.

## 4. Tree

## 0) 정의

트리는 그래프의 한 형태다. 아래는 그래프와 트리를 비교한 표이다.

|                | Graph                                                                                   | Tree                                                            |
| -------------- | --------------------------------------------------------------------------------------- | --------------------------------------------------------------- |
| Structure      | vertices/nodes and edges의 집합임.                                                      | nodes and edges의 집합임.                                       |
| Edges          | 각 노드는 몇 개의 엣지들을 가질 수 있다.                                                | 만약 n개의 노드가 있다면 (n-1)개의 edge를 갖는다.               |
| Types of Edges | 방향성 or 무방향성을 가짐.                                                              | 항상 방향성을 가짐.                                             |
| Root node      | root 존재하지 않음.                                                                     | root(parent)가 존재함.                                          |
| Loop Formation | 싸이클이 있을 수 있음.                                                                  | 싸이클 없음.                                                    |
| Traversal      | 그래프 탐색 방법에는 Breadth-First Search (BFS), and Depth-First Search (DFS)가 존재함. | 트리 탐색 방법에는 in-order, pre-order, or post-order가 존재함. |

## 1) 트리 탐색 방법

{{< figure src="/images/data_structure/data_structure_0/10.png">}}

- in-order : D → B → A → E → C → F → G
- pre-order : A → B → D → C → E → F → G
- post-order : D → B → E → G → F → C → A

### 이진 트리 탐색 코드 ([problem](https://www.acmicpc.net/problem/1991))

```python
## 백준 1991번
import sys

def preorderTraversal(root):
    if root != '.':
        sys.stdout.write(str(root))
        preorderTraversal(tree[root][0])
        preorderTraversal(tree[root][1])

def inorderTraversal(root):
    if root != '.':
        inorderTraversal(tree[root][0])
        sys.stdout.write(str(root))
        inorderTraversal(tree[root][1])

def postorderTraversal(root):
    if root != '.':
        postorderTraversal(tree[root][0])
        postorderTraversal(tree[root][1])
        sys.stdout.write(str(root))

if __name__ == "__main__":
    N = int(sys.stdin.readline().strip())
    tree = {}

    for n in range(N):
        root, left, right = sys.stdin.readline().strip().split()
        tree[root] = [left, right]

    preorderTraversal('A')
    sys.stdout.write("\n")
    inorderTraversal('A')
    sys.stdout.write("\n")
    postorderTraversal('A')

'''
<input>
7
A B C
B D .
C E F
E . .
F . G
D . .
G . .
'''
```

## 참고자료

[intro]

- [[geeksforgeeks] **Data Structures**](https://www.geeksforgeeks.org/data-structures/)

[graph]

- [[geeksforgeeks] **Graph Data Structure And Algorithms**](https://www.geeksforgeeks.org/graph-data-structure-and-algorithms/)
- [[geeksforgeeks] Applications, Advantages and Disadvantages of Graph](https://www.geeksforgeeks.org/applications-advantages-and-disadvantages-of-graph/)
- [[wiki] 가중그래프](https://ko.wikipedia.org/wiki/%EA%B0%80%EC%A4%91_%EA%B7%B8%EB%9E%98%ED%94%84)
- [[blog] [자료구조] 그래프(Graph)란](https://gmlwjd9405.github.io/2018/08/13/data-structure-graph.html)

[tree]

- [[geeksforgeeks] **Difference between graph and tree**](https://www.geeksforgeeks.org/difference-between-graph-and-tree/)
- [[blog] **파이썬으로 구현한 자료구조 - 트리**](https://blex.me/@baealex/%ED%8C%8C%EC%9D%B4%EC%8D%AC%EC%9C%BC%EB%A1%9C-%EA%B5%AC%ED%98%84%ED%95%9C-%EC%9E%90%EB%A3%8C%EA%B5%AC%EC%A1%B0-%ED%8A%B8%EB%A6%AC#%EC%A0%84%EC%B2%B4-%EC%86%8C%EC%8A%A4%EC%BD%94%EB%93%9C)
