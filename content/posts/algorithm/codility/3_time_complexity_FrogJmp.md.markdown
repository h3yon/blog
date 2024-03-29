---
title: "3. Time Complexity - FrogJmp"
date: 2022-09-03T18:27:34+09:00
draft: false
categories:
  - algorithm
tags:
  - algorithm
---

# FrogJmp

Link: https://app.codility.com/programmers/lessons/3-time_complexity/frog_jmp/

A small frog wants to get to the other side of the road. The frog is currently located at position X and wants to get to a position greater than or equal to Y. The small frog always jumps a fixed distance, D.

Count the minimal number of jumps that the small frog must perform to reach its target.

Write a function:

def solution(X, Y, D)

that, given three integers X, Y and D, returns the minimal number of jumps from position X to a position equal to or greater than Y.

For example, given:

  X = 10
  Y = 85
  D = 30
the function should return 3, because the frog will be positioned as follows:

after the first jump, at position 10 + 30 = 40
after the second jump, at position 10 + 30 + 30 = 70
after the third jump, at position 10 + 30 + 30 + 30 = 100
Write an efficient algorithm for the following assumptions:

X, Y and D are integers within the range [1..1,000,000,000];
X ≤ Y.

# Tried

```python
def solution(X, Y, D):
    i = (Y-X) // D
    X += i*D
    while X < Y:
        X += D
        i += 1
    return i
    
# def solution(X, Y, D):
#     i = 0
#     while X < Y:
#         X += D
#         i += 1
#     return i
```

아래처럼 풀었다가 성능 측정에서 0점이 나왔다.  
그래서 위처럼 다시 수정해서 100%로 해주었다!
