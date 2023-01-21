---
title: "[Leetcode] Linked List 1 - 21. Merge Two Sorted Lists"
date: 2022-09-13T00:15:16+09:00
draft: false
tags: ["leetcode", "linked-list", "python"]
categories: ["Problem Solving"]
showToc: true
UseHugoToc: true
comments: true
---

## [Leetcode] **21. Merge Two Sorted Lists**

[문제 링크](https://leetcode.com/problems/merge-two-sorted-lists/)

이 문제는 제목 그대로 2개의 리스트를 정렬해서 결합하는 문제입니다. 구현되어있는 ListNode class를 이용해서 mergeTwoLists method를 완성하면 됩니다.

## 풀이 과정

다음의 조건을 갖고 있다고 가정하고 실행 과정을 정리해보겠습니다.

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next

a = [1,2,4]
b = [1,3,4]

dummy = cur = ListNode(0)
```

{{< figure src="/images/problem_solving/ps_linkedlist_1/1.png">}}
{{< figure src="/images/problem_solving/ps_linkedlist_1/2.png">}}
{{< figure src="/images/problem_solving/ps_linkedlist_1/3.png">}}

## Code (python)

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next

class Solution:
    def mergeTwoLists(self, list1: Optional[ListNode], list2: Optional[ListNode]) -> Optional[ListNode]:
        dummy = cur = ListNode(0)
        while list1 and list2:
            if list1.val < list2.val:
                cur.next = list1
                list1 = list1.next
            else:
                cur.next = list2
                list2 = list2.next
            cur = cur.next
        cur.next = list1 or list2
        return dummy.next
```

## 자료

[1] [python 변수 할당의 개념](https://rroundtable.github.io/FastPages/python/pointer/2019/08/03/Python-%EB%B3%80%EC%88%98%ED%95%A0%EB%8B%B9%EC%9D%98-%EA%B0%9C%EB%85%90.html)

[2] [Leetcode solution 1](https://leetcode.com/problems/merge-two-sorted-lists/discuss/1826693/Python3-MERGING-Explained)
