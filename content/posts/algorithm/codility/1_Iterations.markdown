---
title: "Week Preparation Kit"
date: 2022-08-28T23:47:34+09:00
draft: false
categories:
  - algorithm
tags:
  - algorithm
---

# 주제: 1. Iterations

## BinaryGap

A binary gap within a positive integer N is any maximal sequence of consecutive zeros that is surrounded by ones at both ends in the binary representation of N.

For example, number 9 has binary representation 1001 and contains a binary gap of length 2. The number 529 has binary representation 1000010001 and contains two binary gaps: one of length 4 and one of length 3. The number 20 has binary representation 10100 and contains one binary gap of length 1. The number 15 has binary representation 1111 and has no binary gaps. The number 32 has binary representation 100000 and has no binary gaps.

Write a function:

class Solution { public int solution(int N); }

that, given a positive integer N, returns the length of its longest binary gap. The function should return 0 if N doesn't contain a binary gap.

For example, given N = 1041 the function should return 5, because N has binary representation 10000010001 and so its longest binary gap is of length 5. Given N = 32 the function should return 0, because N has binary representation '100000' and thus no binary gaps.

Write an efficient algorithm for the following assumptions:

N is an integer within the range [1..2,147,483,647].

## try

```python
def solution(N):
    binary_value = str(bin(N))[2:]
    arr = binary_value.strip('0').strip('1').split('1')
    answer = 0
    for value in arr:
        if len(value) > 0:
            answer = max(len(value), answer)
    return answer

```

## 후기

큰일났다. 점수가 너무 낮다,,
60점,,-> strip을 해주니 100점 strip이 필수!
