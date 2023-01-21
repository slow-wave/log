---
title: "[algorithm] 소수 판별"
date: 2022-11-24T01:14:27+09:00
draft: false
categories: ["Algorithm"]
showToc: true
UseHugoToc: true
comments: true
---

## 소수 판별

### Intro

코딩테스트 문제를 풀다보면 가끔 소수 판별하는 문제가 나옵니다. 소수는 간단하게 정의하자면 1보다 큰 자연수 중 1과 자기 자신만을 약수로 가지는 수입니다. 수학 시간에 소수를 많이 다뤘다보니 수를 보면 이 수는 소수인지 아닌지는 작은 수면 바로 판별이 가능하지만 이를 알고리즘으로 바꾸는 것은 좀 어려웠습니다.

다행히 소수 판별 문제를 해결하기 위한 대표적인 방법이 의미 정의 되어있습니다. 에라토스테네스의 체 알고리즘을 활용하는 것입니다. 이는 다수의 자연수에 대하여 소수 여부를 판별할 때 사용하는 대표적인 알고리즘이며 N보다 작거나 같은 모두 소수를 찾을 때 사용할 수 있습니다.

### 1) 과정

에라토스테네스의 체 알고리즘의 **구체적인 동작 과정**은 다음과 같습니다.
{{< figure src="/images/algorithm/algo_prime/Sieve_of_Eratosthenes_animation.gif">}}

```markdown
1. 2부터 𝑁까지의 모든 자연수를 나열한다
2. 남은 수 중에서 아직 처리하지 않은 가장 작은 수 𝑖를 찾는다
3. 남은 수 중에서 i의 배수를 모두 제거한다(𝑖는 제거하지 않는다)
4. 더 이상 반복할 수 없을 때까지 2번과 3번의 과정을 반복한다
```

### 2) code

위의 동작 과정을 코드로 구현하면 다음과 같습니다.

```python
def prime(n):
    array = [True for i in range(n + 1)] # 처음엔 모든 수가 소수(True)인 것으로 초기화

    # 에라토스테네스의 체 알고리즘
    for i in range(2, int(n ** 0.5 + 1)): # 2부터 n의 제곱근까지의 모든 수를 확인하며
        if array[i] == True: # i가 소수인 경우 (남은 수인 경우)
            # i를 제외한 i의 모든 배수를 지우기
            j = 2
            while i * j <= n:
                array[i * j] = False
                j += 1
    return array
```

### 3) ‘소수 판별’ 관련 문제

- [백준 1920번](https://www.acmicpc.net/problem/1920)
- [백준 1987번](https://www.acmicpc.net/problem/1978)

## 참고 자료

- [blog] **[[이것이 코딩 테스트다 with Python] 38강 에라토스테네스의 체](https://freedeveloper.tistory.com/392)**
- [wiki] [소수](<https://ko.wikipedia.org/wiki/%EC%86%8C%EC%88%98_(%EC%88%98%EB%A1%A0)>)
- [wiki] [에라토스테네스의 체](https://ko.wikipedia.org/wiki/%EC%97%90%EB%9D%BC%ED%86%A0%EC%8A%A4%ED%85%8C%EB%84%A4%EC%8A%A4%EC%9D%98_%EC%B2%B4)
