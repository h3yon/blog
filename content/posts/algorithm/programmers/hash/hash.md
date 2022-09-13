이제 다시 공부를 시작해야겠다 해서 해시 부분을 다시 풀어보았다.

# 1. 완주하지 못한 선수(Level 1)

링크: https://school.programmers.co.kr/learn/courses/30/lessons/42576

## 문제 설명

수많은 마라톤 선수들이 마라톤에 참여하였습니다. 단 한 명의 선수를 제외하고는 모든 선수가 마라톤을 완주하였습니다.  
마라톤에 참여한 선수들의 이름이 담긴 배열 participant와 완주한 선수들의 이름이 담긴 배열 completion이 주어질 때,  
완주하지 못한 선수의 이름을 return 하도록 solution 함수를 작성해주세요.  

## 제한사항
마라톤 경기에 참여한 선수의 수는 1명 이상 100,000명 이하입니다.  
completion의 길이는 participant의 길이보다 1 작습니다.  
참가자의 이름은 1개 이상 20개 이하의 알파벳 소문자로 이루어져 있습니다.  
참가자 중에는 동명이인이 있을 수 있습니다.  

## 입출력 예
|participant	|completion	| return|
|--|--|--|
|["leo", "kiki", "eden"]|["eden", "kiki"]|"leo"|
|["marina", "josipa", "nikola", "vinko", "filipa"]|["josipa", "filipa", "marina", "nikola"]|"vinko"|
|["mislav", "stanko", "mislav", "ana"]|["stanko", "ana", "mislav"]|"mislav"|

## 입출력 예 설명
### 예제 #1
"leo"는 참여자 명단에는 있지만, 완주자 명단에는 없기 때문에 완주하지 못했습니다.

### 예제 #2
"vinko"는 참여자 명단에는 있지만, 완주자 명단에는 없기 때문에 완주하지 못했습니다.

### 예제 #3
"mislav"는 참여자 명단에는 두 명이 있지만, 완주자 명단에는 한 명밖에 없기 때문에 한명은 완주하지 못했습니다.

## 풀어보기

```python
def solution(participant, completion):
    participant.sort()
    completion.sort()
    for i in range(len(completion)):
        if participant[i] != completion[i]:
            return participant[i]
    return participant[len(participant)-1]
```

# 2. 포켓몬 (Level 1)

링크: https://school.programmers.co.kr/learn/courses/30/lessons/1845  
해시에서 처음 본 문제인데 간단했다~

## 문제 설명

당신은 폰켓몬을 잡기 위한 오랜 여행 끝에, 홍 박사님의 연구실에 도착했습니다. 홍 박사님은 당신에게 자신의 연구실에 있는 총 N 마리의 폰켓몬 중에서 N/2마리를 가져가도 좋다고 했습니다.
홍 박사님 연구실의 폰켓몬은 종류에 따라 번호를 붙여 구분합니다. 따라서 같은 종류의 폰켓몬은 같은 번호를 가지고 있습니다. 예를 들어 연구실에 총 4마리의 폰켓몬이 있고, 각 폰켓몬의 종류 번호가 [3번, 1번, 2번, 3번]이라면 이는 3번 폰켓몬 두 마리, 1번 폰켓몬 한 마리, 2번 폰켓몬 한 마리가 있음을 나타냅니다. 이때, 4마리의 폰켓몬 중 2마리를 고르는 방법은 다음과 같이 6가지가 있습니다.

첫 번째(3번), 두 번째(1번) 폰켓몬을 선택
첫 번째(3번), 세 번째(2번) 폰켓몬을 선택
첫 번째(3번), 네 번째(3번) 폰켓몬을 선택
두 번째(1번), 세 번째(2번) 폰켓몬을 선택
두 번째(1번), 네 번째(3번) 폰켓몬을 선택
세 번째(2번), 네 번째(3번) 폰켓몬을 선택
이때, 첫 번째(3번) 폰켓몬과 네 번째(3번) 폰켓몬을 선택하는 방법은 한 종류(3번 폰켓몬 두 마리)의 폰켓몬만 가질 수 있지만, 다른 방법들은 모두 두 종류의 폰켓몬을 가질 수 있습니다. 따라서 위 예시에서 가질 수 있는 폰켓몬 종류 수의 최댓값은 2가 됩니다.
당신은 최대한 다양한 종류의 폰켓몬을 가지길 원하기 때문에, 최대한 많은 종류의 폰켓몬을 포함해서 N/2마리를 선택하려 합니다. N마리 폰켓몬의 종류 번호가 담긴 배열 nums가 매개변수로 주어질 때, N/2마리의 폰켓몬을 선택하는 방법 중, 가장 많은 종류의 폰켓몬을 선택하는 방법을 찾아, 그때의 폰켓몬 종류 번호의 개수를 return 하도록 solution 함수를 완성해주세요.

## 제한사항

nums는 폰켓몬의 종류 번호가 담긴 1차원 배열입니다.
nums의 길이(N)는 1 이상 10,000 이하의 자연수이며, 항상 짝수로 주어집니다.
폰켓몬의 종류 번호는 1 이상 200,000 이하의 자연수로 나타냅니다.
가장 많은 종류의 폰켓몬을 선택하는 방법이 여러 가지인 경우에도, 선택할 수 있는 폰켓몬 종류 개수의 최댓값 하나만 return 하면 됩니다.

## 입출력 예
|nums|result|
|--|--|
|[3,1,2,3]|2|
|[3,3,3,2,2,4]|3|
|[3,3,3,2,2,2]|2|

## 풀어보기

```python
def solution(nums):
    available = len(nums) // 2
    set_nums = set(nums)
    return min(len(set_nums), available)
```

# 3. 전화번호 목록 (Level 2)

이 문제가 좀 헷갈렸다. 이중for문을 사용한다는 게 좀 헷갈렸던 부분이었던 것 같다.  
링크: https://school.programmers.co.kr/learn/courses/30/lessons/42577

## 문제 설명
전화번호부에 적힌 전화번호 중, 한 번호가 다른 번호의 접두어인 경우가 있는지 확인하려 합니다.
전화번호가 다음과 같을 경우, 구조대 전화번호는 영석이의 전화번호의 접두사입니다.
  
구조대 : 119  
박준영 : 97 674 223  
지영석 : 11 9552 4421  
전화번호부에 적힌 전화번호를 담은 배열 phone_book 이 solution 함수의 매개변수로 주어질 때, 어떤 번호가 다른 번호의 접두어인 경우가 있으면 false를 그렇지 않으면 true를 return 하도록 solution 함수를 작성해주세요.

### 제한 사항
phone_book의 길이는 1 이상 1,000,000 이하입니다.
각 전화번호의 길이는 1 이상 20 이하입니다.
같은 전화번호가 중복해서 들어있지 않습니다.

### 입출력 예제
|phone_book|return|
|--|--|
|["119", "97674223", "1195524421"]|false|
|["123","456","789"]|true|
|["12","123","1235","567","88"]|false|

## 풀어보기
```python
def solution(phone_book):
    answer = True
    for i in phone_book:
        for j in phone_book:
            if j.startswith(i):
                return False
    return answer
```

# 4. 위장 (수식을 생각하지 못했다. Level 2)

링크: https://school.programmers.co.kr/learn/courses/30/lessons/42578  

수식을 생각하지 못했다.  
경우의 수가 많아서 dp 문제가 아닐까 했는데 수식을 보고 머리를 한대 맞은 듯 했다!  
공부 좀 다시 해야겠다,,  

## 문제 설명
스파이들은 매일 다른 옷을 조합하여 입어 자신을 위장합니다.

예를 들어 스파이가 가진 옷이 아래와 같고 오늘 스파이가 동그란 안경, 긴 코트, 파란색 티셔츠를 입었다면 다음날은 청바지를 추가로 입거나 동그란 안경 대신 검정 선글라스를 착용하거나 해야 합니다.

|종류|이름|
|--|--|
|얼굴|동그란 안경, 검정 선글라스|
|상의|파란색 티셔츠|
|하의|청바지|
|겉옷|긴 코트|

스파이가 가진 의상들이 담긴 2차원 배열 clothes가 주어질 때 서로 다른 옷의 조합의 수를 return 하도록 solution 함수를 작성해주세요.

## 제한사항
clothes의 각 행은 \[의상의 이름, 의상의 종류]로 이루어져 있습니다.
스파이가 가진 의상의 수는 1개 이상 30개 이하입니다.
같은 이름을 가진 의상은 존재하지 않습니다.
clothes의 모든 원소는 문자열로 이루어져 있습니다.
모든 문자열의 길이는 1 이상 20 이하인 자연수이고 알파벳 소문자 또는 '_' 로만 이루어져 있습니다.
스파이는 하루에 최소 한 개의 의상은 입습니다.

## 입출력 예

|clothes|return|
|--|--|
|[["yellow_hat", "headgear"], ["blue_sunglasses", "eyewear"], ["green_turban", "headgear"]]|5|
|[["crow_mask", "face"], ["blue_sunglasses", "face"], ["smoky_makeup", "face"]]|3|

### 입출력 예 설명
#### 예제 #1
headgear에 해당하는 의상이 yellow_hat, green_turban이고 eyewear에 해당하는 의상이 blue_sunglasses이므로 아래와 같이 5개의 조합이 가능합니다.

1. yellow_hat
2. blue_sunglasses
3. green_turban
4. yellow_hat + blue_sunglasses
5. green_turban + blue_sunglasses

#### 예제 #2
face에 해당하는 의상이 crow_mask, blue_sunglasses, smoky_makeup이므로 아래와 같이 3개의 조합이 가능합니다.

1. crow_mask
2. blue_sunglasses
3. smoky_makeup

```python
def solution(clothes):
    my_dict = {}
    for i in range(len(clothes)):
        cloth, cloth_type = clothes[i]
        if cloth_type not in my_dict:
            my_dict[cloth_type] = 1
        else:
            my_dict[cloth_type] += 1
            
    answer = 1
    for dict_key in my_dict.keys():
        # 안입거나 입거나
        answer *= my_dict[dict_key]+1
        
    return answer-1
```
경우의 수는 `(A+1)*(B+1)*...` 와 같다.    
A가 마스크 종류 중 하나라면 그 중에서 aC1 조합으로 하는데 안입는 경우의 수가 있으니 (A+1) * 로 하면 된다.  
모두 안 입는 경우의 수 하나를 빼주면 된다.  

# 베스트 앨범(Level 3)

링크: https://school.programmers.co.kr/learn/courses/30/lessons/42579  

위에 레벨2문제에서 틀려서 조금 슬펐는데,  
이번 문제는 맞춰서 안도했다!  
근데 다시 보니 코드가 너무 복잡해서 다른 방식을 생각해봐야겠다.

## 문제 설명
스트리밍 사이트에서 장르 별로 가장 많이 재생된 노래를 두 개씩 모아 베스트 앨범을 출시하려 합니다. 노래는 고유 번호로 구분하며, 노래를 수록하는 기준은 다음과 같습니다.

속한 노래가 많이 재생된 장르를 먼저 수록합니다.  
장르 내에서 많이 재생된 노래를 먼저 수록합니다.  
장르 내에서 재생 횟수가 같은 노래 중에서는 고유 번호가 낮은 노래를 먼저 수록합니다.  
노래의 장르를 나타내는 문자열 배열 genres와 노래별 재생 횟수를 나타내는 정수 배열 plays가 주어질 때, 베스트 앨범에 들어갈 노래의 고유 번호를 순서대로 return 하도록 solution 함수를 완성하세요.

## 제한사항

- genres[i]는 고유번호가 i인 노래의 장르입니다.  
- plays[i]는 고유번호가 i인 노래가 재생된 횟수입니다.  
- genres와 plays의 길이는 같으며, 이는 1 이상 10,000 이하입니다.  
- 장르 종류는 100개 미만입니다.  
- 장르에 속한 곡이 하나라면, 하나의 곡만 선택합니다.  
- 모든 장르는 재생된 횟수가 다릅니다.  

## 입출력 예
|genres|plays|return|
|--|--|--|
|["classic", "pop", "classic", "classic", "pop"]|[500, 600, 150, 800, 2500]|[4, 1, 3, 0]|

## 입출력 예 설명
classic 장르는 1,450회 재생되었으며, classic 노래는 다음과 같습니다.

```
고유 번호 3: 800회 재생
고유 번호 0: 500회 재생
고유 번호 2: 150회 재생
```
pop 장르는 3,100회 재생되었으며, pop 노래는 다음과 같습니다.

```
고유 번호 4: 2,500회 재생
고유 번호 1: 600회 재생
```
따라서 pop 장르의 [4, 1]번 노래를 먼저, classic 장르의 [3, 0]번 노래를 그다음에 수록합니다.

장르 별로 가장 많이 재생된 노래를 최대 두 개까지 모아 베스트 앨범을 출시하므로 2번 노래는 수록되지 않습니다.

## 풀어보기

```python
def solution(genres, plays):
    _dict = dict()
    arr = []
    i = 0
    
    # {"genre":plays} 맵을 구해서 장르별 play 수를 구한다.
    # arr에는 (순서, 장르명, 플레이)를 담아준다.
    for genre,play in zip(genres, plays):
        arr.append((i, genre, play))
        if genre not in _dict.keys():
            _dict[genre] = play
        else:
            _dict[genre] += play
        i += 1
    
    # play 순으로 장르를 정렬한다.
    sorted_dict = sorted(_dict.items(), key=lambda x: x[1], reverse=True)
    
    # _arr 에는 arr을 플레이 순으로 정렬해준 리스트를 할당한다.
    _arr = sorted(arr, key=lambda x: x[2], reverse=True)    
    
    # 장르별로 arr에 있던 순서를 candidate에 담아준다. (플레이순)
    candidate = []
    for genre, _ in sorted_dict:
        candidate.append([i[0] for i in _arr if i[1] == genre])
    
    # 장르별로 2개까지라고 했으니까 2개씩만 answer 리스트에 담아준다.
    answer = []
    for i in candidate:
        answer.extend(i[:2])

    return answer
```
