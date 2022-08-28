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
