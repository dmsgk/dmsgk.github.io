---
title:  "삽입정렬(Insertion sort) 개념 이해하고 구현하기"
date:   2021-11-02 10:30:51 +0900
categories: algorithm
---

정렬 시리즈를 다시 공부하면서 공부했던 내용을 간단히 블로그에 업로드해보려 합니다. 이번 글에서는 삽입정렬을 다루어 보도록 하겠습니다.

### 삽입정렬(Insertion sort)은 무엇인가?

삽입정렬은 간단히 말하자면 i 번째 요소를 정렬하기 위해 i-1번째부터 1번째까지 순차적으로 탐색하면서 자신에게 맞는 위치를 찾는 방식으로 정렬하는 알고리즘입니다. 두 번째 수부터 시작해 같은 작업을 반복하기 때문에 i번째 수를 삽입하고자 할 때는 첫 번째부터 i-1번째는 수가 정렬된 상태이므로 정렬된 상태에 i번째 수를 넣는 위치를 찾을 수 있는 것이죠. 

삽입정렬을 구현하는 간단한 수도코드를 작성해보았습니다.

- for i in (2번째 요소부터 끝까지) 
  - j = i -1로 시작해 현재 삽입하고자 하는 값보다 작거나 같은 값이 나올 때까지 탐색한다.
    - 그러면서 동시에 기존의 값을 하나씩 밖으로 밀어낸다. 그래야 원하는 위치에 현재 값을 삽입할 수 있다.
  - 원하는 위치가 나오면 현재 값을 삽입한다. 이렇게 되면 첫번째부터 i번째 수까지는 정렬된 상태가 된다.



### Python으로 삽입정렬 구현하기

파이썬으로 삽입정렬을 구현하면 다음과 같이 나타낼 수 있습니다.

```python
def insertion_sort():
    global array
    for i in range(1, len(array)):
        temp = array[i]
        prev_idx = i-1
        while prev_idx >= 0 and array[prev_idx] > temp:
            array[prev_idx+1] = array[prev_idx]
            prev_idx -= 1
        array[prev_idx+1] = temp
```

### 삽입정렬(Insertion sort)의 시간복잡도 및 공간복잡도

- **시간복잡도 : Ω(n), Θ(n^2), O(n^2)**

  삽입정렬은  만일 배열이 정렬되어 있다면 한 번만 비교하게 되므로  **최선의 경우 Ω(n)**입니다. 

  그러나 최악의  경우 (n-1) + (n-2) + ... + 1 = (n-1)n /2 번 연산해야 하므로 시간복잡도는 **O(n^2)** 가 됩니다.  평균의 경우도 **Θ(n^2)** 로 나타나게 됩니다.

- **공간복잡도**

   주어진 배열 내에서 교환을 통해 정렬을 하기 때문에 삽입정렬의 공간복잡도는 **O(n)**입니다. 





### References

- https://gyoogle.dev/blog/algorithm/Insertion%20Sort.html
- https://github.com/GimunLee/tech-refrigerator/blob/master/Algorithm/%EC%82%BD%EC%9E%85%20%EC%A0%95%EB%A0%AC%20(Insertion%20Sort).md#%EC%82%BD%EC%9E%85-%EC%A0%95%EB%A0%AC-insertion-sort