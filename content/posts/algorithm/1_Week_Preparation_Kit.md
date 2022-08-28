---
title: "Week Preparation Kit"
date: 2022-08-28T23:47:34+09:00
draft: true
categories:
  - algorithm
tags:
  - algorithm
---


# 1 Week Preparation Kit

## Day1

### 1번

<img width="576" alt="image" src="https://user-images.githubusercontent.com/46602874/187058276-8f4d6060-5eec-4480-99e1-945f061a7568.png">

```python
def plusMinus(arr):
    counts = [0,0,0] # count in order to plus, minus, 0 numbers
    for num in arr:
        if num > 0:
            counts[0] += 1
        elif num < 0:
            counts[1] += 1
        elif num == 0:
            counts[2] += 1
        else:
            #for logging
            print(num)
    for count in counts:
        ans = count/len(arr)
        print("{:.6f}".format(ans))
```

### 2번 Mini-Max Sum

<img width="576" alt="image" src="https://user-images.githubusercontent.com/46602874/187058649-86d45e23-c0c4-40bb-9403-d38589d70fe7.png">

```python
def miniMaxSum(arr):
    arr.sort()
    print(sum(arr[0:4]), end=' ')
    print(max(sum(arr[-4:]), arr[-1]))
```

### 3번 Time Conversion

<img width="576" alt="image" src="https://user-images.githubusercontent.com/46602874/187059186-273c486e-1198-4451-8515-2ddc564bcfab.png">


```python
def timeConversion(s):
    times = s.split(':')
    if 'PM' in times[2]:
        times[2] = times[2].replace('PM', '')
        if times[0] != '12':
            times[0] = str(int(times[0]) + 12)
    if 'AM' in times[2]:
        times[2] = times[2].replace('AM', '')
        if times[0] == '12':
            times[0] = str(int(times[0]) - 12).zfill(2)
    return ':'.join(times)
```

## Day2

a = \[1,2,3,4,3,2,1\] 일 때 한번만 나오는 숫자는 4이다.
이렇게 한번만 나오는 숫자를 리턴하자.
답: 4

### 1번 Lonely Integer

```python
def lonelyinteger(a):
    arr = [a[i] for i in range(len(a)) if a.count(a[i]) == 1]
    return arr[0]
```

### 2번

<img width="576" alt="image" src="https://user-images.githubusercontent.com/46602874/187059349-9a4a2647-62a6-4373-9d01-81e41e963514.png">

```python
def diagonalDifference(arr):
    # stop 3 3
    arr_len = len(arr)
    first = [arr[i][i] for i in range(arr_len)]
    second = [arr[i][abs(i-arr_len+1)] for i in range(arr_len)]
    return abs(sum(first)-sum(second))
```
