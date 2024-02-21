# 7. Binary Search

# 1. Binary Search (이진 탐색)

## 1-1. 이론

정렬된 배열이나 벡터에서만 동작한다.

탐색할 때마다 탐색 범위가 절반으로 줄어든다.

1. `tar` : 구하고자 하는 값
2. `low` : 시작인덱스 (`start` , `left` 라고도 함)
3. `high` : 끝인덱스 (`end` , `right` 라고도 함)
4. `mid` : 둘의 중간인덱스. `(low + high) / 2` 로 나오는 정수

`low` : 0

`high` : size - 1

양 끝점에서 시작하면서 `low` , `high` , `mid` 를 옮겨나갈 것이다.

1. 맨 처음 시도 시 `nums[tar] == mid` 일 경우: 정답 발견
2. 아닐 경우, 두 경우로 나누어 `low` , `high` 를 옮겨간다.
    
    `nums[mid] < tar` ⇒ `low = mid + 1` 
    
    `nums[mid] > tar` ⇒ `high = mid - 1`
    
3. 변경된 `low` , `high` 를 사용해 `mid` 를 다시 계산
    
    `mid = (low + high) / 2`
    
4. 과정을 반복하다, `low > high` 로 역전이 일어나면 찾기 실패

두 가지 예제로 확인해보자.

ex1) `tar` : 23

| idx | L = 0 | 1 | 2 | 3 | M = 4 | 5 | 6 | 7 | 8 | H = 9 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| val | 2 | 5 | 8 | 12 | 16 | 23 | 38 | 56 | 72 | 91 |

`nums[mid] < tar` ⇒ `low = mid + 1`

| idx | L = 5 | 6 | M = 7 | 8 | H = 9 |
| --- | --- | --- | --- | --- | --- |
| val | 23 | 38 | 56 | 72 | 91 |

`nums[mid] > tar` ⇒ `high = mid - 1`

| idx | L = M = 5 | H = 6 |
| --- | --- | --- |
| val | 23 | 38 |

`nums[mid] == tar` 이므로 종료

ex2) `tar` : 4

| idx | L = 0 | 1 | 2 | 3 | M = 4 | 5 | 6 | 7 | 8 | H = 9 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| val | 2 | 5 | 8 | 12 | 16 | 23 | 38 | 56 | 72 | 91 |

`nums[mid] > tar` ⇒ `high = mid - 1`

| idx | L = 0 | M = 1 | 2 | H = 3 |
| --- | --- | --- | --- | --- |
| val | 2 | 5 | 8 | 12 |

`nums[mid] > tar` ⇒ `high = mid - 1`

| idx | L = M = H = 0 |
| --- | --- |
| val | 2 |

`nums[mid] < tar` ⇒ `low = mid + 1`

| idx | H = 0 | L = 0 |
| --- | --- | --- |
| val | 2 | 5 |

여기서, `log > high` 로 역전되었으므로 탐색 실패

## 1-2. 구현

```cpp
#define _CRT_SECURE_NO_WARNINGS
#include <iostream>

using namespace std;

int nums[10] = { 2, 5, 8, 12, 16, 23, 38, 56, 72, 91 };

bool binarySearch(int tar, int size)
{
    int low = 0;
    int high = size - 1;
    int mid = 0;

    while (low <= high)
    {
        mid = (low + high) / 2;

        if (nums[mid] == tar)
        {
            return true;
        }
        else if (nums[mid] < tar)
        {
            low = mid + 1;
        }
        else
        {
            high = mid - 1;
        }
    }

    return false;
}

int main()
{
    bool ret = binarySearch(23, 10);    // 1
    // bool ret = binarySearch(4, 10); // 0
    cout << ret;
    return 0;
}

```

# 2. Lower Bound / Upper Bound

이진 탐색의 경우, 찾으면 그걸로 끝이고, bool 을 리턴하면 된다.

그러나, 다음 경우를 생각해보자.

```cpp
int nums = {2, 2, 2, 4, 4, 4};
```

이 경우, 2 나 3 이 있는 것은 알 수 있을지 모르지만, "어디서부터 어디까지가" 2 인지는 모른다. 이럴 때 쓰는 게 Lower/Upper Bound 이다.

tar 의 중복이 있을 경우,

Lower Bound : 시작 인덱스 반환. 없을 경우, tar 보다 큰 첫 인덱스 반환

Upper Bound : 끝 다음의 인덱스 반환. 없을 경우, tar 보다 큰 첫 인덱스 반환

ex1) tar = 2 일 경우,

lb = 0, ub = 3

ex2) tar = 3 일 경우,

lb = 3, ub = 3

즉, 동일한 값이 나왔을 때도 탐색을 계속해야 한다.

## 2-1. Lower Bound

ex1) `tar` : 2

| idx | L = 0 | 1 | 2 | M = 3 | 4 | 5 | H = 6 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| val | 2 | 2 | 2 | 4 | 4 | 4 |  |

`high` 는 최초엔 길이로 시작하며, 변경사항이 생기면 `- 1` 하지 않는다.

이로 인해 `mid` 도 바뀐다.

탈출조건: `low < high`

즉, `low == high` 일때도 탈출하며, 최종적으로 `low` 를 반환한다.

현재 상황: `nums[mid] > tar`

이 경우,

`high = mid - 1` 이 아니라, `high = mid`

| idx | L = 0 | M = 1 | 2 | H = 3 |
| --- | --- | --- | --- | --- |
| val | 2 | 2 | 2 | 4 |

현재 상황: `nums[mid] == tar`

그러나, 아직 맨 첫 인덱스가 아니기 때문에 탐색 계속 진행

이 경우, `nums[mid] > tar` 와 마찬가지로 `high = mid` 처리

| idx | L = M = 0 | H = 1 |
| --- | --- | --- |
| val | 2 | 2 |

`nums[mid] == tar` ⇒ `high = mid`

| idx | L = H = 0 |
| --- | --- |
| val | 2 |

이 때, `low = high` 이므로 루프 탈출하고 `low` 리턴 ⇒ 0

즉, `tar` 가 중복일 경우, 시작 인덱스 반환

ex2) `tar` : 3

(중간과정 생략)

| idx | L = 0 | M = 1 | 2 | H = 3 |
| --- | --- | --- | --- | --- |
| val | 2 | 2 | 2 | 4 |

`nums[mid] < tar` ⇒ `low = mid + 1`

| idx | L = M = 2 | H = 3 |
| --- | --- | --- |
| val | 2 | 4 |

`nums[mid] < tar` ⇒ `low = mid + 1`

| idx | L = H = 3 |
| --- | --- |
| val | 4 |

이 때, `low = high` 이므로 루프 탈출하고 `low` 리턴 ⇒ 3

즉, `tar` 가 없을 경우, tar 보다 큰 첫 인덱스 반환

## 2-2. Upper Bound

ex1) `tar` : 2

(중간과정 생략)

| idx | L = 0 | M = 1 | 2 | H = 3 |
| --- | --- | --- | --- | --- |
| val | 2 | 2 | 2 | 4 |

현재 상황 : `nums[mid] == tar`

이 경우,

`low = mid + 1` 로 간다.

| idx | L = M = 2 | H = 3 |
| --- | --- | --- |
| val | 2 | 4 |

`nums[mid] == tar` ⇒ `low = mid + 1`

| idx | L = H = 3 |
| --- | --- |
| val | 4 |

이 때, `low = high` 이므로 루프 탈출하고 `low` 리턴 ⇒ 3

즉, `tar` 가 중복일 경우, 끝 다음의 인덱스 반환

ex2) `tar` : 3

(중간과정 생략)

| idx | L = M = 2 | H = 3 |
| --- | --- | --- |
| val | 2 | 4 |

`nums[mid] < tar` ⇒ `low = mid + 1`

| idx | L = H = 3 |
| --- | --- |
| val | 4 |

이 때, `low = high` 이므로 루프 탈출하고 `low` 리턴 ⇒ 3

즉, `tar` 가 없을 경우,  tar 보다 큰 첫 인덱스 반환

## 2-3. 구현

```cpp
#include <iostream>

using namespace std;

// 동일한 수의 배열이 있는 경우
int nums[6] = { 2, 2, 2, 4, 4, 4 };

// 시작 인덱스 반환
// 없을 경우, tar 보다 큰 첫 인덱스 반환
int lowerBound(int tar, int size)
{
    int low = 0;
    // BS : high = size - 1
    int high = size;
    int mid = 0;

    // BS : low <= high
    while (low < high)
    {
        mid = (low + high) / 2;

        if (nums[mid] < tar)
        {
            low = mid + 1;
        }
        // BS : high = mid - 1
        // 같을 경우도 이리로 옴 (계속 탐색 진행)
        else
        {
            high = mid;
        }
    }

    // BS : false 리턴
    return low;
}

// 끝 다음의 인덱스 반환
// 없을 경우, tar 보다 큰 첫 인덱스 반환
int upperBound(int tar, int size)
{
    int low = 0;
    // BS : high = size - 1
    int high = size;
    int mid = 0;

    // BS : low <= high
    while (low < high)
    {
        mid = (low + high) / 2;
        // 같을 경우에도 이리로 옴 (계속 탐색 진행)
        if (nums[mid] <= tar)
        {
            low = mid + 1;
        }
        // BS : high = mid - 1
        else
        {
            high = mid;
        }
    }

    // BS : false 리턴
    return low;
}

int main()
{
    int lb = lowerBound(2, 6); // 0
    // int lb = lowerBound(3, 6); // 3
    int ub = upperBound(2, 6); // 3
    // int ub = upperBound(3, 6); // 3
    cout << "Lower Bound: " << lb << "\n";
    cout << "Upper Bound: " << ub << "\n";
    return 0;
}
```

# 3. Parametric Search (매개변수 탐색)

## 3-1. 이론

이진 탐색과 매우 비슷하지만, 답을 찾았더라도 더 나은 답을 찾아간다는 차이점이 있다.

공유기 3개를 설치하되, 두 공유기 사이의 거리를 최대로 설정하고 싶다.

만약, 거리를 2로 설정했을 때 3개가 모두 설치되었다면? 이진 탐색은 여기서 끝날 것이다.

그러나, Parametric Search 는 여기서 더 나아가보는 것이다.

거리 3: 공유기 3개 설치 ⇒ 거리 늘림

거리 4: 공유기 2개밖에 설치 못함 ⇒ 다시 돌아옴

답: 거리 3

즉, 범위를 좁혀가다가, 최적해를 찾는다.

조건을 만족하는지 알기 위해, 이를 구해주는 결정함수를 사용할수도 있으며, 이 함수에 매개변수 (param) 을 넣어주기 때문에 Parametric Search 라고 한다.

여기서 param 은 검사범위의 중간값이다. 즉, `(low + high) / 2` 이며, 계속 변할것이다.

그럼 정답임을 알게되는 시점은? `low > high` 로 역전될 때.

언제 사용할까? 주어진 조건을 만족하는 최댓값, 최솟값 구할 때 주로 사용된다.

## 3-2. 문제: 랜선 자르기 (BOJ 1654)

1. K 개의 랜선이 있고 각각의 랜선 길이는 다름
2. K 개의 랜선을 길이 x 로 잘라서, 랜선 N 개로 만듦
3. 이 때, x 의 최대 길이 구하기

어떻게 구할까? 

1. `K` 개의 랜선을 오름차순으로 정렬한다.
2. 랜선의 길이는 반드시 0 보단 커야 하기 때문에, `low` 는 1부터 시작
3. 랜선을 한번도 안 잘랐다 가정하면, `high` 는 정렬된 배열의 맨 마지막 값, 즉 최댓값이다.
4. `x` 를 전체 랜선 길이 중에서 중간값 `mid` 로 둔다.
5. 결정함수: `K` 개의 랜선을 각각 길이 `x` 로 잘랐을 때,
    1. `N` 개의 랜선이 나온다면 ⇒ `true`
    2. `N` 개의 랜선이 안 나온다면 ⇒ `false`
6. 그런데 여기서, `true` 가 나왔다고 해도 `x` 가 최댓값임은 확신할 수 없다! 따라서, `x` 를 좀 더 늘려본다. ⇒ `low + mid + 1`
7. 만약 `false` 라면, 실패한 것이므로 `x` 를 줄인다. ⇒ `high = mid - 1`
8. 계속 진행하다, `low > high` 이면 종료

```cpp
#define _CRT_SECURE_NO_WARNINGS
#include <iostream>
#include <algorithm>

using namespace std;

int K, N;            // 랜선의 개수, 잘라서 만들고자하는 랜선의 개수
int lanLines[10000]; // K개의 랜선을 저장할 배열

void input()
{
    cin >> K >> N;

    for (int i = 0; i < K; i++)
    {
        cin >> lanLines[i];
    }
}

void init()
{
    // 랜선의 길이 정렬
    sort(lanLines, lanLines + K);
}

// 결정함수
// K개의 랜선을 길이 mid(len)으로 잘라본다.
// N개 이상 만들 수 있다면, 자르는 길이를 더 늘려도 된다. 즉, mid를 올려도 된다.
bool isPossible(int len)
{
    // 아직 랜선을 자르지 않았으므로 cnt를 0으로 초기화한다.
    int cnt = 0;

    // K개의 랜선을 확인하면서 길이 x로 잘랐을 때 총 몇 개의 랜선이 나오는지 계산한다.
    for (int i = 0; i < K; i++)
    {
        cnt += lanLines[i] / len;
    }

    // 랜선이 N 개 이상 나온다면 true를, 그렇지 않다면 false를 반환한다.
    if (cnt >= N)
        return true;
    else
        return false;
}

int psearch()
{
    // 최소 길이는 0이 아니라 1이다.
    int low = 1;
    int high = lanLines[K - 1];
    int result = 0;
    int mid = 0;

    while (low <= high)
    {
        // param : 중간값
        mid = (low + high) / 2;

        // 결정함수
        // K개의 랜선을 길이 mid(len)으로 잘라본다.
        // N개 이상 만들 수 있다면, 자르는 길이를 더 늘려도 된다. 즉, mid를 올려도 된다.
        if (isPossible(mid))
        {
            result = mid;
            low = mid + 1;
        }
        // N 개 이상 만들 수 없다면, 자르는 길이는 줄여야 한다. 즉, mid를 내려야 한다.
        else
        {
            high = mid - 1;
        }
    }
    return result;
}

int main()
{
    // 4 11
    // 802
    // 743
    // 457
    // 539
    // freopen("sample_input.txt", "r", stdin);
    input();
    init();
    int result = psearch();
    cout << result << '\n'; // 200
    return 0;
}
```

- 주의: Parametric Search 문제는 `int` 의 범위를 벗어나는 현상이 매우 자주 발생된다. 혹시 틀렸다면, `long long` 으로 바꿔보자.

```cpp
int psearch()
{
    // 최소 길이는 0이 아니라 1이다.
    long long low = 1;
    long long high = lanLines[K - 1];
    long long result = 0;
    long long mid = 0;
```

이렇게 바꾸면 정답.