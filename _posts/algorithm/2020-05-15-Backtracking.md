---
# 게시글 맨위 사진
header:
  # 제목/내용 이미지
  overlay_image: /assets/images/unsplash02.jpg
  # 카톡으로 url 보낼때 사진
  og_image: /assets/images/logo_Algorithm.jpg
  # 출처
  caption: "출처 : [**개인**](https://github.com/94incheon)"
  # 맨 아래 이미지
  teaser: /assets/images/logo_Algorithm.jpg

# 게시글 제목/설명
title:  "Algorithm : 백트래킹이 무엇인지 이해하고, 활용해보자"
excerpt: "Backtracking 을 사용하여 순열 및 다양한 문제 풀이"

# 목차설정부분
toc: true
toc_sticky: true
toc_label: "페이지 주요 목차"

# 카테고리/태그
categories: Algorithm
tags: Backtracking
---

# 백트래킹

## 1. 백트래킹 개념

> - 여러 가지 선택지(옵션)들이 존재하는 상황에서 한가지를 선택한다.
> - 선택이 이루어지면 새로운 선택지들의 집합이 생성된다.
> - 이런 선택을 반복하면서 최종 상태에 도달한다.
>   - 올바른 선택을 계속하면 목표 상태(goal state)에 도달한다.
>
>
>
> - 당첨 리프노드 찾기
>   - 루트에서 갈 수 있는 노드를 선택한다.
>   - 꽝 노드까지 도달하면 최근의 선택으로 되돌아와서 다시 선택한다.
>   - 더 이상의 선택지가 없다면 이전의 선택지로 돌아가서 다른 선택을 한다.
>   - 루트까지 돌아갔을 경우 더 이상 선택지가 없다면 찾는 답이 없다.



- **백트래킹과 깊이 우선 탐색(DFS)과의 차이**
  - 어떤 노드에서 출발하는 경로가 해결책으로 이어질 것 같지 않으면 더 이상 그 경로를
    따라가지 않음으로써 시도의 횟수를 줄임 (Prunning 가지치기)
  - 깊이 우선 탐색이 모든 경로를 추적하는데 비해 **백트래킹은 불필요한 경로를 조기에 차단**
  - 깊이우선 탐색을 가하기에는 경우의 수가 너무나 많음. 즉 **N!** 가지의 경우의 수를 가진 문제에
    대해 깊이우선탐색(DFS) 을 가하면 당연히 처리 불가능한 문제
  - **백트래킹 알고리즘을 적용하면 일반적으로 경우의 수가 줄어들지만 이 역시 최악의 경우에는
    여전히 지도함수 시간(Exponential Time)을 요하는 처리 불가능**



- 루트 노드에서 리프(leaf)노드까지의 경로는 해답후보(candidate solution)가 되는데,
  깊이 우선 탐색을 하여 그 해답후보중에서 해답을 찾을 수 있다.
- 그러나 이 방법을 사용하면 해답이 될 가능성이 전혀 없는 노드의 후손 노드(descendant) 들도
  모두 검색해야 하므로
- 모든 후보를 검사?
  - No!
- **백트래킹 기법**
  - 어떤 노드의 유망성을 점검한 후에 유망하지 않다고 결정되면 그 노드의 부모로 되돌아가
    (backtracking) 다음 자식 노드로 감
  - 어떤 노드를 방문하였을 때 그 노드를 포함한 경로가 해답이 될 수 없으면 그 노드는 유망하지 않다고 하며, 반대로 해답의 가능성이 있으면 유망하다고 한다.
  - **가지치기(pruning): 유망하지 않는 노드가 포함되는 경로는 더 이상 고려하지 않는다.**



## 2. 백트래킹 순서/절차

**백트래킹 순서/절차**

- **백트래킹을 이용한 알고리즘은 다음과 같은 절차**로 진행된다.
  - **상태 공간 트리의 깊이 우선 탐색을 실시**한다.
  - 각 노드가 유망한지를 점검한다.
  - 만일 그 노드가 유망하지 않으면, 그 노드의 부모 노드로 돌아가서 검색을 계속한다.





## 3. N-Queen 문제

> - N x N 서양 장기판에서 배치한 Queen들이 서로 위협하지 않도록
>   n개의 Queen을 배치하는 문제
>   - 어떤 두 Queen도 서로를 위협하지 않아야 한다.
>   - Queen을 배치한 n개의 위치는?

**4-Queens 문제로 축소해서 생각해 보자.**

- 같은 행에 위치할 수 없다.
- 모든 경우의 수 4x4x4x4 = 256
- **해를 찾기 위한 선택의 과정을 트리로 표현한 것**
  - 상태공간트리

![image-20200515094052881](/assets/images/Algorithm/image-20200515094052881.png)

**깊이우선탐색 vs 백트래킹**

- 깊이우선탐색
  - 155가지 경우의 수
- 백트래킹
  - 27가지 경우의 수



**N-Queen 문제**

- 퀸 N개를 크기의 체스판 안에 서로를 공격할 수 없도록 배치하는 모든 경우를 구하는 문제

- 후보해의 수
  - 8-Queen 문제
  - `64C8` = 44억
- 실제 해의 수 : 이 중에서 실제 해는 92개뿐
- 즉, 44억 개가 넘는 후보해의 수 속에서 92개를 최대한 효율적으로 찾아내는 것이 관건



```python
# 2606. N-Queen 문제

def backtrack(idx): # idx = 행
    global N, result
    # 최종상태인지 확인하고, 최종상태이면, 정답(해)
    if idx == N:
        # 다 찾았음, 해
        result += 1
        return
    # 해당 상태에서 선택할 수 있는 후보군 생성
    # 노드가 유망한지 확인 : 열, 상향대각, 하향대각
    for i in range(N):
        # if 열이 유망하고, 대각들이 유망
        if not visited[i] and not dia_1[idx+i] and not dia_2[N + i - idx - 1]:
            # 모든 후보군에 대해서 다음 상태 실행
            visited[i] = 1
            dia_1[idx + i] = 1
            dia_2[N + i - idx - 1] = 1

            backtrack(idx+1)

            visited[i] = 0
            dia_1[idx + i] = 0
            dia_2[N + i - idx - 1] = 0


T = int(input())
for tc in range(1, T+1):
    N = int(input())
	# 각 행에는 1개의 Queen만 올 수 있음
    visited = [0] * N # 열의 사용여부를 판단하는 리스트

	# 대각 유망성을 판단할 리스트
	# 대각 (N * 2 - 1)
	# 상향대각 : i,j 의 합이 같음
    dia_1 = [0] * (2*N - 1)
	# 하향대각 : i,j 의 차가 일정
    dia_2 = [0] * (2*N - 1) # N + j - i - 1


    result = 0
    backtrack(0)
    print(f'#{tc} {result}')
```

![image-20200515141138418](/assets/images/Algorithm/image-20200515141142103.png)





## 4. 순열 구하기

**백트래킹을 이용하여 순열 구하기**

```python
# 순열 만들기
def backtrack(result, selected, idx, N): # 배열, 사용여부, 인덱스, 개수
    if idx == N:
        print(result)
        return

    # 사용가능한 선택지 후보군에 대해서 다음단계로 진행
    for i in range(N):
        if not selected[i]:
            result[idx] = i

            selected[i] = 1
            backtrack(result, selected, idx + 1, N)
            selected[i] = 0

N = 5
result = [0] * N
selected = [0] * N

backtrack(result, selected, 0, N)
```



**{1,2,3,4,5,6,7,8,9,10} 의 부분집합(Powerset)중 원소의 합이 10인 부분집합을 모두 출력하시오.**

```python
# {1,2,3,4,5,6,7,8,9,10} 의 부분집합에서 합이 10인 부분집합 출력

def backtrack(arr, idx, N, selected, sum_num):
    # 백트래킹(가지치기)
    if sum_num > 10:
        return

    if idx == N:
        # 총합이 10인 경우에만, 출력
        if sum_num == 10:
            for i in range(N):
                if selected[i]:
                    print(arr[i], end=' ')
            print()
        return

    selected[idx] = 1
    # sum_num += arr[idx]
    backtrack(arr, idx + 1, N, selected, sum_num + arr[idx])

    selected[idx] = 0
    # sum_num -= arr[idx]
    backtrack(arr, idx + 1, N, selected, sum_num)


arr = [1,2,3,4,5,6,7,8,9,10]
backtrack(arr, 0, len(arr), [0]*10, 0)



########## 라이브러리 사용한 풀이 ##########
from itertools import permutations

arr = [i for i in range(1, 11)]
for i in range(1, 11):
    perm = list(permutations(arr, i))
    for p in perm:
        if sum(list(p))==10:
            print(list(p))
# len( permutations(arr) ) => 360만 10! => 10팩토리얼


from itertools import combinations

arr = [i for i in range(1, 11)]
for i in range(len(arr)+1):
    for p in combinations(arr, i):
        if sum(list(p)) == 10:
            print(list(p))
```

