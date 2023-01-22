---
title:  "[Python3] 코딩테스트에 자주 쓰이는 Code Snippets"
date:   2021-07-02 11:11:51 +0900
categories: algorithm
---



파이썬으로 코딩테스트를 준비하면서 필요한 구문들을 적어두고 있습니다. 공부하면서 계속 업데이트할 예정입니다

## 1. 입출력

### 1.1. 입력

#### `sys.stdin.readline()` 의 활용

- 한 개의 정수를 입력받을 때

```python
import sys
a = int(sys.stdin.readline())
```

- 정해진 개수의 정수를 한 줄에 입력받을 때

```python
import sys
a,b,c = map(int,sys.stdin.readline().split())
```

	`map()` 은 반복 가능한 객체(리스트 등)에 대해 각각의 요소들을 지정된 함수로 처리해주는 함수.

- 임의의 개수의 정수를 한 줄에 입력받아 리스트에 저장할 때

```python
import sys
data = list(map(int,sys.stdin.readline().split()))
```

	`map()`은 맵 객체를 만드므로, 리스트형으로 바꿔주기 위해서 `list()`로 처리

- 임의의 개수의 정수를 n줄 입력받아 2차원 리스트에 저장할 때

```python
import sys
data = []
n = int(sys.stdin.readline())
for i in range(n):
    data.append(list(map(int,sys.stdin.readline().split())))
```

이렇게 한다면 각 요소의 길이가 동일한 2차원 리스트도 만들 수 있고, 각각 길이가 다른 2차원 리스트도 입력 받을 수 있다.

- 문자열 n줄을 입력받아 리스트에 저장할 때

```python
import sys
n = int(sys.stdin.readline())
data = [sys.stdin.readline().strip() for i in range(n)]
```



### 1.2. 출력

#### 리스트에 있는 문자열을 하나의 문자열로 변환 :  `'구분자'.join(리스트)`

```python
li = ['a', 'b', 'c']
print("".join(li))		# 'abc' 출력
print("_".join(li))		# 'a_b_c' 출력
```

같은 원리로, 리스트에 있는 숫자를 이어 출력하고자 할 때에는 문자열로 변환하여 동일하게 할 수 있다. 

```python
li = [1,2,3,4]
str = " ".join(list(map(str,li)))
print(str)  # 1 2 3 4 출력
```



## 2. 자주 사용하는 모듈

- ### `itertools`

  - **순열(Permutations)**

  ```python
  import itertools
  
  l1 = ["a","b","c"]
  p = itertools.permutations(l1)
  print(list(p))   # [('a', 'b', 'c'), ('a', 'c', 'b'), ('b', 'a', 'c'), ('b', 'c', 'a'), ('c', 'a', 'b'), ('c', 'b', 'a')]
  ```

  - **조합(Combinations)**

  ```python
  from itertools import combinations
  
  l2 = ["a","b","c"]
  c = list(combinations(l1,2))
  print(c)   # [('a', 'b'), ('a', 'c'), ('b', 'c')]
  ```

  - **map 함수를 사용하여, 보기 편하게 출력하기**

    리스트 안의 튜플형태로 출력이 되는데, `map()`을 사용하여 다음과 같이 출력할 수 있다.

  ```python
  import itertools
  
  l1 = ["a","b","c"]
  p = itertools.permutations(l1)
  print(list(map("".join, p)))  # ['abc', 'acb', 'bac', 'bca', 'cab', 'cba']
  ```

- ### `collections`

  - ### 집계를 위한 클래스 : `collections.Counter`

    어떤 원소 x가 주어진 시퀀스에 몇 번이나 등장하는지 세야할 때 사용

    ```python
    import collections
    
    my_list = ["dance","classic", "pop", "classic", "classic", "pop"]
    my_counter = collections.Counter(my_list)
    my_counter   # Counter({'dance': 1, classic': 3, 'pop': 2})
    
    # 원소에 접근하기
    my_counter['classic']  # 3
    
    # Counter.most_common은 가장 많이 나온 아이템부터 출력하는 메소드로, 최대힙과 같은 역할을 할 수 있다.
    my_counter.most_common()   # [('classic', 3), ('pop', 2), ('dance', 1)]
    
    # 가장 많이 나온 튜플을 반환하기
    my_counter.most_common(1)   # [('classic', 3)]  
    
    # 개수 빼기
    my_counter.subtract(['dance'])  # 문자열 빼고 싶은 경우 리스트에 넣어서 빼기. Counter({'dance': 0, 'classic': 3, 'pop': 2})로 바뀜
    
    # 개수가 0 이하인 경우 삭제
    my_counter += collections.Counter()  # Counter({'classic': 3, 'pop': 2})
    
    # 키값 반환
    my_counter.most_common(1)[0][0]  # 'classic'
    
    # dict() 함수를 이용해 딕셔너리로 변환
    my_dict = dict(my_counter)   # {'classic': 3, 'pop': 2}
    ```

    

## 3. List

### 3.1. 정렬 - sorted와 sort

- `a = sorted(list)` : 리스트 값을 정렬하여 a에 저장. 원래 리스트는 정렬되지 않는다. 
- `list.sort()` :  None값 리턴. 원래 리스트를 정렬한다. 

일반적으로 sort()가 복사본을 만들지 않으므로 속도가 더 빠르다. 

#### 특정 인덱스에 따라서 정렬되도록: `lambda` 함수 이용

```python
sorted(student_tuples, key=lambda idx: idx[2])  # 2번째(0, 1, 2, ...에서) 인덱스 값을 기준으로 정렬
```

### 3.2. 리스트 역순으로 생성  ex) [5,4,3,2,1]

- `li = [i for i in range(n, 0, -1)]` : [n,n-1, ....,1] 생성



## 4. Dictionary

### 4.1. 딕셔너리 정렬

#### Key에 의한 정렬

- **sorted(dict.items())**

```python
dict = {"abcde" : 7, "fzowe" : 5, "fko" : 5}
sortedArr = sorted(dict.items())
```

- **sorted(dict.items(), key=operator.itemgetter(0))** : 특정 인덱스0(=key)에 의한 정렬

```python
import operator

dict = {"abcde" : 7, "fzowe" : 5, "fko" : 5}
sortedArr = sorted(dict.items(), key=operator.itemgetter(0))
```

#### Value에 의한 정렬

- **sorted(dict.items(), key=operator.itemgetter(1))** : 특정 인덱스1(=value)에 의한 정렬

```python
import operator

dict = {"abcde" : 7, "fzowe" : 5, "fko" : 5}
sortedArr = sorted(dict.items(), key=operator.itemgetter(1))

print(sortedArr) #  [('fzowe', 5), ('fko', 5), ('abcde', 7)]
```

##### **value에 의한 정렬 -> key에 의한 정렬**

- sorted(dict.items(), **key=operator.itemgetter(1, 0)**)

1을 기준으로 오름차순으로 정렬하고, 같은 값이 나온다면 0을 기준으로 오름차순으로 다시 정렬

```python
import operator

dict = {"abcde" : 7, "fzowe" : 5, "fko" : 5}
sortedArr = sorted(dict.items(), key=operator.itemgetter(1, 0))

print(sortedArr)  # [('fko', 5), ('fzowe', 5), ('abcde', 7)]
```

##### **내림차순 정렬**

- sorted(dict.items(), key=operator.itemgetter(1), **reverse=True)**

```python
import operator

dict = {"abcde" : 7, "fzowe" : 5, "fko" : 5}
sortedArr = sorted(dict.items(), key=operator.itemgetter(1), reverse=True)

print(sortedArr) # [('abcde', 7), ('fzowe', 5), ('fko', 5)]
```

### 4.2. collections.defaultdict

key값이 없을 경우 미리 지정해놓은 초기값을 반환하는 딕셔너리. 

```python
import collections

anagram_dict = collections.defaultdict(list)  # 키값 없는 경우 []반환
anagram_dict[1]  # []
anagram_dict[1].append(1)  # defaultdict(list, {1: [1]})
```



## 5. 문자열

### 5.1. 입력값에 대한 전처리 방법

#### 파이썬 정규표현식

- 단어 문자가 아닌 모든 문자를 공백으로 치환 후 공백 단위로 나누어 단어 리스트로 변환

```python
import re

paragraph = "Bob hit a ball, the hit BALL flew far after it was hit."
result = re.sub(pattern='\W', repl=' ', string=paragraph).lower().split()  
# 출력 ['bob', 'hit', 'a', 'ball', 'the', 'hit', 'ball', 'flew', 'far', 'after', 'it', 'was', 'hit']
```

### 5.2. 해당 문자열이 숫자 여부인지 판단하기 -  `isdigit()`

```python
'a'.isdigit()  # False
'1'.isdigit()  # True

"abc".isdigit()  # False
"1234".isdigit() # True
```



## 6. 원래의 객체를 변화시키지 않는 복사

- muterble한 객체의 변수간 대입을 하면(a라는 리스트가 있는데 b=a로 할당) 같은 메모리 주소를 갖게 되어 복사를 한 객체가 바뀌면 원래 객체도 바뀌게 된다. 이를 방지하기 위해서는

- **얕은 복사** 

  - 리스트 슬라이싱 활용

    ```
    a = [1,2,3]
    b = a[:]
    ```

    ⚠️그러나 mutable객체 안에 mutable객체인 경우에는 a의 값 변경시 b의 값도 변경될 수 있음

- **깊은 복사**

  - copy 모듈의 `deepcopy`메소드 활용하기

# 이진 탐색 - bisect

- bisect는 이진 탐색 알고리즘을 구현한 파이썬 표준 라이브러리이다. `bisect.bisect` 함수는 정렬된 리스트에 값을 삽입할 때 정렬을 유지할 수 있는 인덱스를 리턴한다.
- `bisect.insort(li, num)` 은 `li`에 `num`을 정렬된 위치에 맞도록 삽입




```python
import bisect
a = [60, 70, 80, 90] 
bisect.insort(a, 85)
print(a)  # [60, 70, 80, 85, 90]
```