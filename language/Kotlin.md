# Kotlin
## 기초 문법
### 변수
- var: 변수값 변경 가능
- val: 변수값 변경 불가능
- ex) `var a = 3`
### 형변환
- to타입() 함수 사용
- ex)  
```
var a: Int = 3
var s: String = a.toString()
```
### 배열
1차원 배열
- Array<>()를 사용하거나 arrayOf()로 초기화
- ex) 
```
var arr1 = Array<Int>(5, {0})
var arr2 = arrayOf(1, 2, 3, 4, 5)
```
- Array<Int>의 경우 IntArray를 주로 사용  

2차원 배열
- 하나의 값으로 초기화하는 경우 다음의 방법을 사용함
- ex) `var arr = Array(n, {Array(m, {0})})`
### 스택, 힙  
java.util.Stack, java.util.Queue 라이브러리 필요  
스택
- ex) `var s = Stack<Int>()`  

큐
- ex) `var q: Queue<Int> = LinkedList<>()`

### 해시
- 제네릭 안에 각각 key와 value의 타입 선언  
- ex) `var h = HashMap<String, Int>()`

### 힙
- PriorityQueue 
- compareBy를 통해 우선순위의 기준 설정
- ex) `var pq = PriorityQueue<Int>(compareBy {it[0]})`