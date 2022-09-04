---
title: "3. Time Complixity - TapeEquilibrium"
date: 2022-09-04T14:47:34+09:00
draft: false
categories:
  - algorithm
tags:
  - algorithm
---

# TapeEquilibrium

Link: https://app.codility.com/programmers/lessons/3-time_complexity/tape_equilibrium/

A non-empty array A consisting of N integers is given. Array A represents numbers on a tape.

Any integer P, such that 0 < P < N, splits this tape into two non-empty parts: A[0], A[1], ..., A[P − 1] and A[P], A[P + 1], ..., A[N − 1].

The difference between the two parts is the value of: |(A[0] + A[1] + ... + A[P − 1]) − (A[P] + A[P + 1] + ... + A[N − 1])|

In other words, it is the absolute difference between the sum of the first part and the sum of the second part.

For example, consider array A such that:

  A[0] = 3
  A[1] = 1
  A[2] = 2
  A[3] = 4
  A[4] = 3
We can split this tape in four places:

P = 1, difference = |3 − 10| = 7.  
P = 2, difference = |4 − 9| = 5.  
P = 3, difference = |6 − 7| = 1.  
P = 4, difference = |10 − 3| = 7  

Write an efficient algorithm for the following assumptions:

N is an integer within the range [2..100,000];
each element of array A is an integer within the range [−1,000..1,000].

# Tried

```python
def solution(A):
    candidate = []
    for i in range(len(A)-1):
        P = i+1
        diff = abs(sum(A[:P])-sum(A[P:]))
        candidate.append(diff)
    return min(candidate)
```

다 맞췄지만 성능면에서 다 틀려서 53%만 맞췄다..  
시간 복잡도는 O(N * N)이다.

```python
def solution(A):
    first = A[0]
    second = sum(A[1:])
    diff = abs(first - second)
    for i in range(1, len(A)-1):
        first += A[i]
        second -= A[i]
        diff = min(diff, abs(first-second))
    return diff
```

하나씩 반영해나가는 게 O(N)이 되고,  
100%로 맞췄음을 알 수 있다.

