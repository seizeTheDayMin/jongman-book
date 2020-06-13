# 6장. 무식하게 풀기

## 6.1 도입

- 가장 많이 하는 실수: 쉬운 문제를 어렵게 푸는 것 (복잡하지만 우아한 답안, 쉽고 간단하며 틀릴 가능성이 낮은 답안을 간과)
- '무식하게 푼다(brute-force)'
    - 컴퓨터의 빠른 계산 능력을 이용해 **가능한 경우의 수**를 일일이 나열하면서 답을 찾는 방법
    - ex1. 두 점 사이의 최단 경로 찾기: 모든 경로들을 하나하나 전부 만들고 그중 가장 짧은 경로를 선택
    - ex2. 자원 분배 문제: 분배 방법을 전부 만들어 보기
- 가능한 방법을 전부 만들어 보는 알고리즘들을 가리켜 '완전 탐색(exhaustive search)' 이라함
- 빠르고 정확하게 구현하는 능력 검증 —> 완전 탐색은 더 빠른 알고리즘의 기반이 되므로 완전 탐색에 대해 잘 익혀둘 필요가 있음

## 6.2 재귀호출과 완전탐색

### 재귀호출

자신이 수행할 작업을 **유사한 형태의 여러 조각으로 쪼갠 뒤** 그 중 한 조각을 수행하고, 나머지를 **자기 자신을 호출**해 실행하는 함수

📄 **예제**

- 문제정의: *자연수 n이 주어졌을 때 1부터 n까지의 합을 반환하는 함수 `sum()`의 구현*
    - 입력: 자연수 n
    - 출력: 1부터 n까지의 합

```go
package main

import (
	"fmt"
)

// 1부터 n까지의 합을 계산하는 반복 함수와 재귀함수

// 필수 조건: n >= 1
// 결과: 1부터 n까지의 합을 반환한다.
func sum(n int) int {
	ret := 0
	for i := 1; i <= n; i++ {
		ret += i
	}
	return ret
}

// 필수 조건: n >= 1
// 결과: 1부터 n까지의 합을 반환한다.
func recursiveSum(n int) int {
	if n == 1 {
		return 1
	}
	return n + recursiveSum(n-1)
}

func main() {
	testInput := 10
	loopResult := sum(testInput)
	recursiveResult := recursiveSum(testInput)

	fmt.Println("Result of Loop: ", loopResult)
	fmt.Println("Result of Recursion: ", recursiveResult)
}
```

```go
// Output
Result of Loop:  55
Result of Recursion:  55
```

- 설명
n개의 숫자 합을 구하는 작업을 **n개의 조각**으로 쪼개, 더할 각 숫자가 하나의 조각이 되도록 구현
자기 자신을 호출해 n-1까지의 합을 구한 뒤, n을 더하면 문제해결 (`n + recursiveSum(n-1)`)
- Note: **기저사례 (base case)**
더이상 쪼개지지 않는 가장 작은 작업들 (위 문제에서는 `n=1`인 경우)

해당 문제에서는 기존 코드에 비해 재귀 호출을 통해 얻을 수 있는 별다른 이익이 없음. 하지만 문제의 특성에 따라 재귀 호출은 코딩을 훨씬 간편하게 해 줄 수 있는 강력한 무기. 아래 예제 참고

📄 **예제.** 중첩 반복문 대체하기 

- 문제정의: 0부터 차례대로 번호 매겨진 n개의 원소 중 네개를 고르는 모든 경우를 출력
    - 입력: 원소 수, 선택 원소 수
    - 결과 예: 만약 `n=7`이라면 `(0,1,2,3)`, `(0,1,2,4)`, `(0,1,2,5)`, ..., `(3,4,5,6)`을 출력

```go
package main

import (
	"fmt"
)

func foo(n int) {
	for i := 0; i < n; i++ {
		for j:= i+1; j < n; j++ {
			for k := j+1; k < n; k++ {
				for l := k+1; l < n; l++ {
					fmt.Println("(", i, j, k, l, ")")
				}
			}
		}
	}
}

func main() {
	foo(7)
}
```

네개를 고르기 위해 4중 for 문을 사용. 다섯개를 골라야하는 경우라면 5중 for문 —> 코드가 길어지고 복잡해짐

완전 탐색 알고리즘을 이용시, 반복문을 사용하게되면 코드가 길어지고 복잡도가 올라가며 유연한 코드를 작성할 수 없음

```go
// n: 전체 원소수
// picked: 지금까지 고른 원소들의 번호
// toPick: 더 고를 원소 수
// 일때, 앞으로 toPick개의 원소를 고르는 모든 방법을 출력한다.
func pick(n int, picked *[]int, toPick int){
	// 기저사례: 더 고를 원소가 없을때 고른 원소들을 출력
	if toPick == 0 { 
		fmt.Println(*picked)
	} else {
		// 고를 수 있는 가장 작은 번호를 계산
		var smallest int

		if len(*picked) == 0 {
			smallest = 0
		} else {
			smallest = (*picked)[len(*picked)-1] + 1
		}
		// 원소하나를 고른다.
		for next := smallest; next < n; next++ {
			picked := append(*picked, next)
			pick(n, &picked, toPick-1)
			picked = picked[:len(picked)-1]
		}
	}
}

func main() {
	// foo(7)
	picked := []int{}
	pick(7, &picked, 4)
}
```

```go
[0 1 2 3]
[0 1 2 4]
[0 1 2 5]
...
[0 4 5 6]
[1 2 3 4]
...
[2 3 5 6]
[2 4 5 6]
[3 4 5 6]
```

- 설명 (`a,b,c,d` 네개의 원소 중에서 두개의 원소를 고르는 과정
    - 직사각형: 우리가 실제로 원하는 답들
    - 화살표: `picked`에 새 숫자를 추가하고 `pick()` 함수를 재귀 호출하는 과정
    - `picked = picked[:len(picked)-1]` (C++: `picked.push_back(next);`): 하나의 답을 만든 뒤에는 이전으로 돌아가 다른 원소를 추가

![6%207077cfcd284c4d7c9492a9e64d5ff389/Untitled.png](6%207077cfcd284c4d7c9492a9e64d5ff389/Untitled.png)

![6%207077cfcd284c4d7c9492a9e64d5ff389/Untitled%201.png](6%207077cfcd284c4d7c9492a9e64d5ff389/Untitled%201.png)

📄 **예제. 보글게임** 

![6%207077cfcd284c4d7c9492a9e64d5ff389/Untitled%202.png](6%207077cfcd284c4d7c9492a9e64d5ff389/Untitled%202.png)

- 문제정의
    - 5x5 크기의 격자에서 상하좌우/대각선으로 인접한 글자들을 이어 단어를 찾아내기
    - 입력: "시작점"(`x`, `y`)과 "찾아야할 단어" (`word`)
    - 출력: "찾아야할 단어"의 존재 유무
    - 결과 예: PRETTY/6.2(b), GIRL/6.2(c), REPEAT/6.2(d)

완전 탐색을 이용하여 단어를 찾아낼 때가지 모든 인접한 칸을 하나씩 시도해보자

- 문제의 분할
    - 각 글자를 하나의 조각으로 고려하기
    - 예: 찾아야할 단어가 "ABCD"라면
        - 현재 좌표에서 "A"가 있는지 확인하고 주변으로 "BCD" 중 "B"가 있는지 찾아본다 (없다면 실패)
        - "A" 기준으로 8개 방향에 "B"가 있다면 이를 기준으로 "CD" 중 "C"가 있는지 찾아본다.  (없다면 실패)
        - ...
- 기저사례의 선택
    1. 위치 `(y,x)`에 있는 글자가 원하는 단어의 첫 글자가 아닌 경우: 실패
    2. 원하는 단어가 한 글자인 경우: 성공

    **NOTE:** 이 둘의 순서는 바뀌면 안됨
    **NOTE:** 입력이 잘못되거나 범위에서 벗어난 경우도 기저 사례로 택하여 처리하면 간결한 코드 처리가 가능 (함소 호출 시점에서 오류 검사가 불필요)

```cpp
const int dx[8] = {-1, -1, -1,  1, 1, 1,  0, 0};
const int dy[8] = {-1,  0,  1, -1, 0, 1, -1, 1};

// 5x5의 보글게임판에서 주어진 단어가 있는지를 확인하는 함수
bool hasWord(int y, int x, const string& word) {
	// 기저사례 1: 시작 위치가 범위 밖이면 무조건 실패
	if(!inRange(y, x)) return false;
	// 기저사례 2: 첫 글자가 일치하지 않으면 실패
	if(board[y][x] != word[0]) return false;
	// 기저사례 3: 단어 길이가 1이면 성공
	if(word.size() == 1) return true;
	
    // 인접한 여덟 칸을 검사한다
	for(int direction=0; direction <8; ++direction) {
		int nextY = y + dy[direction], nextX = x + dx[direction];	
		// 다음 칸이 범위 안에 있는지,첫 글자는 일치하는지 확인할 필요가 없다
		if(hasWord(nextY, nextX, word.substr(1)))
			return true;
	}
	return false;
}
```

- 시간 복잡도 분석
완전탐색의 경우 가능한 답 후보들을 모두 만들어 보기 떄문에, 시간 복잡도 = 가능한 후보 수
    - 보글게임의 경우, 답을 찾으면 바로 종료되므로 별도의 분석이 필요
        - 최악의 경우 = 답이 아예 존재하지 않는 경우: A로 가득찬 경자에서 AAAAH를 찾을때
        - 시작 위치에서 모든 후보를 빠짐없이 검사하여, 단어의 끝에 도달해야 답이 없음을 알게됨
        - 단어의 길이 N에 대해 N-1에 해당하는 행동을 진행하며, 후보의 수는 최대 8^(n-1)로 O(8^n)

**완전탐색 레시피**

완전 탐색으로 문제를 해결하기 위해 필요한 과정은 대략 아래와 같으며, 모든 문제에 항상 적용 되는 것은 아니지만 처음 접근에 대한 대략적인 지침이 될 수 있다.

1. 완전 탐색은 존재하는 모든 답을 하나씩 검사하므로, 걸리는 시간은 가능한 답의 수에 정확히 비례한다. 최대 크기 입력일때 답의 개수를 계산하고 이들을 모두 제한 시간 안에 생성할 수 있는지 확인해본다. 만약 불가능하다면 3부의 설계 패러다임을 적용
2. 가능한 모든 답의 후보를 만드는 과정을 여러 개의 선택으로 나눈다. (조각내기)
3. 하나의 조각에 대해 (일부) 답을 만들고, 나머지 답은 재휘 호출을 통해 얻는다
4. 조각이 하나만 남았거나 남지 않은 경우 기저 사례로 처리

**이론적 배경: 재귀 호출과 부분 문제**

- 중요 개념 "문제(problem)"와 "부분 문제(subproblem)"
"동적 계획법" 이나 "분할 정복"과 같은 중요 디자인 패러다임(3부)을 설명하는데 사용됨
- 직관적으로 쉽게 생각되지 않으므로 예제를 통해 알아보자
문제1: 주어진 자연수 수열을 정렬하라
문제2: {16, 7, 9, 1, 31}을 정렬하라

    같은 문제라고 생각 할 수 있지만 큰 차이가 있다!

    - 수행해야할 "작업"은 같지만 작업을 수행할 "자료"가 다름
    - {1,2,3}을 정렬하는 문제와 {3,2,1}을 정렬하는 문제는 서로 다른 문제이며, 두 문제 중 "문제2"만 "문제의 정의"라고 할 수 있음
- 보글 게임 문제는...
    - 문제의 정의 : '게임판에서의 현재 위치 `(y,x)` 그리고 단어 `word`가 주어질 때 해당 단어를 이 칸에서부터 시작해서 찾을 수 있을까?
    - 문제 해결을 위해 최대 아홉가지 정보를 알아야한다
        - 현재 위치 `(y,x)`에 단어의 첫 글자가 있는가?
        - 윗 칸 `(y-1, x)`에 시작해서, 단어의 나머지 글자들을 찾을 수 있는가?
        - 윗 칸 `(y-1, x-1)`에 시작해서, 단어의 나머지 글자들을 찾을 수 있는가?
        - 나머지 방향(6개)에 대해 단어의 나머지 글자를 찾을 수 있는가?

        2번 이후의 항목은 원래 문제의 한 조각 일뿐 형식이 같은 문제의 결과이며, 이런 문제들을 **원래 문제의 부분 문제**라고 함

### 6.3 문제: 소풍 (문제 ID: PICNIC, 난이도: 하)

**문제**

```cpp
안드로메다 유치원 익스프레스반에서는 다음 주에 율동공원으로 소풍을 갑니다. 
원석 선생님은 소풍 때 학생들을 두 명씩 짝을 지어 행동하게 하려고 합니다. 
그런데 서로 친구가 아닌 학생들끼리 짝을 지어 주면 서로 싸우거나 같이 돌아다니지 않기 때문에, 
항상 서로 친구인 학생들끼리만 짝을 지어 줘야 합니다.

각 학생들의 쌍에 대해 이들이 서로 친구인지 여부가 주어질 때, 
학생들을 짝지어줄 수 있는 방법의 수를 계산하는 프로그램을 작성하세요. 
짝이 되는 학생들이 일부만 다르더라도 다른 방법이라고 봅니다. 
예를 들어 다음 두 가지 방법은 서로 다른 방법입니다.

- (태연,제시카) (써니,티파니) (효연,유리)
- (태연,제시카) (써니,유리) (효연,티파니)
```

제약조건들은 [Algospot > 소풍](https://algospot.com/judge/problem/read/PICNIC) 참고

- 입력
입력의 첫 줄에는 테스트 케이스의 수 C (C <= 50) 가 주어집니다. 각 테스트 케이스의 첫 줄에는 학생의 수 n (2 <= n <= 10) 과 친구 쌍의 수 m (0 <= m <= n*(n-1)/2) 이 주어집니다. 그 다음 줄에 m 개의 정수 쌍으로 서로 친구인 두 학생의 번호가 주어집니다. 번호는 모두 0 부터 n-1 사이의 정수이고, 같은 쌍은 입력에 두 번 주어지지 않습니다. 학생들의 수는 짝수입니다.
- 출력
각 테스트 케이스마다 한 줄에 모든 학생을 친구끼리만 짝지어줄 수 있는 방법의 수를 출력합니다.
- 예제 입력

```
3 
2 1 
0 1 
4 6 # 4명의 학생과 6쌍의 친구
0 1 1 2 2 3 3 0 0 2 1 3 
6 10 
0 1 0 2 1 2 1 3 1 4 2 3 2 4 3 4 3 5 4 5
```

- 예제 출력

```
1
3
4
```

- 예제 입출력 설명
    - 첫 번째 입력 `(2 1)`에는 학생이 두명 밖에 없으므로 이들은 서로 친구이며, 딱 한가지의 방법만 존재
    - 두 번째 입력 `(4 6)`에는 네명의 학생이 모두 서로 친구이며(Why? 4C2=6), 이들은 세 가지 방법이 존재

### 6.4 풀이: 소풍

**완전 탐색**

- 가능한 조합의 수를 계산하는 문제를 푸는 가장 간단한 방법은 완전 탐색을 이용해 조합을 모두 만들어 보는 것
- 재귀 호출을 이용
    - 부분문제: 짝이 없는 학생에게 친구를 찾아주면 남은 학생도 짝지어 지므로 원래 문제와 같은 형태가 됨 —> 전체 문제를 n/2 조각으로 나눠 생각 할 수 있음
    - 기저사례: 짝이 없는 학생이 존재하지 않음

```go
// 코드 6.4 소풍 문제를 해결하는 (잘못된) 재귀 호출 코드

func countPairings(taken []bool, n int) int{
	// 기저 사례: 모든 학생이 짝을 찾았으면 한 가지 방법을 찾았으니 종료한다.
	finished := true
	for i := 0; i < n; i++ {
		if !taken[i] {
			finished = false
		}
	}

	if finished { return 1 }
	// 서로 친구인 두 학생을 찾아 짝을 지어준다
	ret := 0
	for i := 0; i < n; i++ {
		for j := 0; j < n; j++ {
			if !taken[i] && !taken[j] && areFriends[i][j] {
				taken[i] = true
				taken[j] = true
				ret += countPairings(taken)
				taken[i] = false
				taken[j] = false
			}
		}
	}
	return ret
}
```

예제 입력에 대해 아래와 같이 잘못된 출력을 한다

```go
1
6
12
```

문제점은 아래와 같다

- 같은 학생 쌍을 두번 짝지어 준 경우(`(0,1)`, `(1,0)`) 중복으로 카운트
- 다른 순서로 학생들을 짝지어 주는 경우(`(0,1)`, `(2,3)` vs. `(2,3)`, `(0,1)`) 다른 경우로 보고 카운트

이를 해결하기 위해

- 항상 특정 형태를 갖는 답만을 세도록 하자
- 한 방법으로, 사전순으로 가장 먼저 오는 답 하나만 세는 것
ex. `(2,3)`, `(0,1)`이나 `(1,0)`, `(2,3)`은 세지 않지만 `(0,1)`, `(2,3)`은 세는 것

이렇게하면

- 가장 번호가 빠른 학생의 짝은 그보다 번호가 뒤일 수 없어 `(1,0)`과 같은 경우 없어짐
- 번호가 가장 빠른 학생부터 짝을 짓기 때문에 `(2,3)`, `(0,1)` 순서로 짝을 지어주는 경우가 없어짐

```go
func countPairings(taken []bool) int {
	firstFree := -1

	// 짝을 찾지 못한 학생이 있는지
	for i := 0; i < n; i++ {
		if !taken[i] {
			firstFree = i
			break
		}
	}

	//기저사례: 모든 학생이 짝을 찾았다면 한 가지 방법을 찾았으니 종료한다.
	if firstFree == -1 { return 1 }

	ret := 0
	for pairWith := firstFree+1; pairWith < n; pairWith++ {
		// 짝 후보 학생(idx=pairWith)이 아직 짝이 없으며, 해당 학생(idx=firstFree) 과 친구인지
		if !taken[pairWith] && areFriends[firstFree][pairWith] {
			taken[firstFree] = true
			taken[pairWith] = true
			ret += countPairings(taken)
			taken[firstFree] = false
			taken[pairWith] = false
		}
	}

	return ret
}
```

```go
1
3
4
```

**답의 수의 상한**

재귀 호출 알고리즘은 답의 수에 정비례하는 시간이 걸린다. 
프로그램을 짜기 전에 답의 수가 얼마나 될지 예측해보고 모든 답을 만드는데 시간이 얼마나 걸릴지 확인해야한다.

최대 10명의 학생에 대해 Worst Case는 서로서로 모두가 친구인 경우이다.

- 첫번째 학생이 선택할 수 있는 경우의 수는 9(자신을 제외한 학생수)
- 두번째 학생이 선택할 수 있는 경우의 수는 7(자신을 제외한 학생수)
- ...

총 945 경우의 수

### 6.5 문제: 게임판 덮기

생략

[Algospot > 게임판 덮기](https://algospot.com/judge/problem/read/BOARDCOVER)

### 6.6 풀이: 게임판 덮기

생략

### 6.7 최적화 문제

정의: 여러개의 답 중에 가장 "좋은" 답을 찾아 내는 문제들

- ex1. n개의 원소 중에서 r개를 순서 없이 골라내는 방법의 수는? 
: 답이 하나로 정해지며, 더 좋고 덜 좋은 답이 없다. 이는 최적화 문제가 아니다
- ex2. n개의 사과 중에서 r개를 골랐을 때, 무게의 합을 최대화
: 사과를 골라내는 **방법은 여러개(여러개의 답)**인데, 이 중 합이 최대(기준)가 되는 답을 찾는 것으로 최적화 문제이다

동적 계획법을 다루는 8장, 조합 탐색을 다루는 11장, 결정 문제로 해결하는 12장 모두 최적화 문제과 완련되어 있음

📄 **예제. 여행하는 외판원 문제 (Traveling Sales-man Problem, TSP)**

- 문제정의
한 영업사원이 n(2≤n≤10)개의 도시 중 한 도시에서 출발해 모든 도시들을 한 번씩 방문한 뒤 돌아오고자 한다. 각 도시간의 거리가 주어졌을때 가장 짧은 경로는?
- **무식하게 풀어보자**
    - 완전 탐색으로 문제를 풀기 위한 첫 번째 단계는 시간 안에 답을 구할 수 있는지 확인해보는 것
    - 시작점을 고려하지 않고 특정 도시에서 출발한다고 가정하면 n-1개의 도시를 나열하는 방법은 (n-1)!가지

    ![6%207077cfcd284c4d7c9492a9e64d5ff389/Untitled%203.png](6%207077cfcd284c4d7c9492a9e64d5ff389/Untitled%203.png)

    - 최대 도시수 10개이므로 가능한 경우의 수는 9! = 362,880개 (충분히 완전 탐색으로 풀 수 있는 문제이다. But, 최대 도시 수가 늘어난다면 제한시간 내에 풀지 못할 수 있으니 주의!)
- **재귀 호출을 통한 답안 생성**

```go
package main

import (
	"math"
)

var n int
var dist [10][10]float64

func shortestPath(path []int, visited []bool, currentLength float64) float64{
	// 기저 사례: 모든 도시를 다 방문했을 때는 시작 도시로 돌아가고 종료한다.
	if len(path) == n {
		return currentLength + dist[path[0]][path[len(path)-1]]
	}
	
	// 매우 큰 값으로 초기화
	ret := math.Inf(1)

	for next := 0; next < n; next ++ {
		if visited[next] { continue }

		here := path[len(path)-1]
		path = append(path, next)
		visited[next] = true

		// 나머지 경로를 재귀 호출을 통해 완성하고 가장 짧은 경로의 길이를 얻는다
		cand := shortestPath(path, visited, currentLength + dist[here][next])

		ret = math.Min(ret, cand)
		visited[next] = false
		
		path = path[:len(path)-1]
	}
	return ret
}
```

### Continue...