---
title: "3. Time Complexity - PermMissingElem"
date: 2022-09-03T16:47:34+09:00
draft: false
categories:
  - algorithm
tags:
  - algorithm
---

# PermMissingElem

Link: https://app.codility.com/programmers/lessons/3-time_complexity/perm_missing_elem/

An array A consisting of N different integers is given. The array contains integers in the range [1..(N + 1)], which means that exactly one element is missing.

Your goal is to find that missing element.

Write a function:

class Solution { public int solution(int[] A); }  

that, given an array A, returns the value of the missing element.  

For example, given array A such that:  

  A[0] = 2  
  A[1] = 3  
  A[2] = 1  
  A[3] = 5  
the function should return 4, as it is the missing element.  

Write an efficient algorithm for the following assumptions:

N is an integer within the range [0..100,000];  
the elements of A are all distinct;  
each element of array A is an integer within the range [1..(N + 1)].  

# Tried

```python
from collections import Counter

def solution(A):
    try:
        count_A = Counter(A)
        for i in range(1, max(A)):
            if i not in count_A.keys():
                return i
        raise Exception("not found")
    except:
        if len(A) > 0:
            return max(A)+1
        else:
            return 1
```

1,2,3 일 때는 그럼 어떡하지 했는데, 4인 걸 제출하고나서야 알았다.  
그래서 위처럼 한번 수정했더니 100% 만족함을 알았다.  
