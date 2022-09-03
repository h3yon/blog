---
title: "2. Array - CyclicRotation"
date: 2022-09-03T16:47:34+09:00
draft: false
categories:
  - algorithm
tags:
  - algorithm
---

# 주제: Arrays

## CyclicRotation

Link: https://app.codility.com/programmers/lessons/2-arrays/cyclic_rotation/

An array A consisting of N integers is given. Rotation of the array means that each element is shifted right by one index, and the last element of the array is moved to the first place. For example, the rotation of array A = [3, 8, 9, 7, 6] is [6, 3, 8, 9, 7] (elements are shifted right by one index and 6 is moved to the first place).

The goal is to rotate array A K times; that is, each element of A will be shifted to the right K times.

Write a function:

class Solution { public int[] solution(int[] A, int K); }

that, given an array A consisting of N integers and an integer K, returns the array A rotated K times.

For example, given

    A = [3, 8, 9, 7, 6]
    K = 3
the function should return [9, 7, 6, 3, 8]. Three rotations were made:

    [3, 8, 9, 7, 6] -> [6, 3, 8, 9, 7]
    [6, 3, 8, 9, 7] -> [7, 6, 3, 8, 9]
    [7, 6, 3, 8, 9] -> [9, 7, 6, 3, 8]
For another example, given

    A = [0, 0, 0]
    K = 1
the function should return [0, 0, 0]

Given

    A = [1, 2, 3, 4]
    K = 4
the function should return [1, 2, 3, 4]

Assume that:

N and K are integers within the range [0..100];
each element of array A is an integer within the range [−1,000..1,000].
In your solution, focus on correctness. The performance of your solution will not be the focus of the assessment.

# Tried

문제를 보았을 때 2가지 풀이방식이 생각났었다.  

첫번째 방법은 아래와 같다.  
일단 for문을 돌면서 맨끝껄 빼고  
뺀 부분을 맨 앞에, 남아 있는 부분은 뒤에 두는 방법을 생각했다.

```python
from collections import deque

def solution(A, K):
    tmp = deque()
    for i in range(K):
        tmp.appendleft(A.pop(-1))
    return list(tmp) + A
```

근데 50%만 맞추어서 2번째 방법을 생각했다.

```python
from collections import deque

def solution(A, K):
    dq = deque(A)
    dq.rotate(K)
    return list(dq)
```

라이브러리만 사용하는 꼴이어서 괜찮을까 하고 일단 풀어봤는데,  
100%로 맞추었다.

세번째 방법으로는 A\[K-1:\] 로 인덱싱하는 방법도 있었다.  
그런데 그 부분에서 생각을 좀 해야될 것으로 보여서 패스했다!  

## 회고

50% 맞추었던 부분을 다시 보았다.  
배열이 비어있는데 K만큼 pop(-1)하면 당연히 에러가 날 것이다.  
이 부분을 수정해주자.  

```python
from collections import deque

def solution(A, K):
    #A가 없을 경우 
    if len(A) < K:
        return A
    tmp = deque()
    for i in range(K):
        tmp.appendleft(A.pop(-1))
    return list(tmp) + A
```

여기에서 50점 -> 75점이 되었다.  

상황을 정리해보자.  
- K >= N, got \[1, 1, 2, 3, 5\] expected \[3, 5, 1, 1, 2\]
- maximal N and K, WRONG ANSWER, got \[710, 807, 568, 560, ..\] expected \[155, 710, 807, 568,..\]

일단 K>=N인 경우를 내가 잘 고려하지 않았음을 알았다.  

```python
from collections import deque

def solution(A, K):
    # A배열이 비어있는데 K만큼 pop하면 exception 떨어지니까 예외처리
    try:
        # K>=N인 경우 나머지로 바꿔준다.
        if K > len(A):
            K %= len(A)
        tmp = deque()
        for i in range(K):
            tmp.appendleft(A.pop(-1))
        return list(tmp) + A
    except:
        return A
```

이렇게 100점을 만들었다.  
다양한 경우를 고려해야 함을 느꼈다.
