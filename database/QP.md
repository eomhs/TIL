# Query Processing 
Query processing의 단계
1. Parsing and translation
    - Query를 internal from으로 바꿈 (relational algebra 등)
2. Optimization
    - 가장 효율적인 query evaluation plan으로 바꿈
3. Evaluation
    - Query evaluation engine이 query evaluation plan을 실행하고 결과를 리턴함

Query plan  
Evaluation primitive: 어떻게 평가할지 annotation이 달린 relational algebra  
**Query evaluation plan**: 일련의 primitive operations  
<img src = "https://github.com/eomhs/TIL/blob/main/figures/Query%20plan.png" width="600" height="300"/>    
      
Query Optimization
1. 논리적으로 동일한 expressions를 생성
2. Expressions에 annotate함
3. 예상되는 cost에 따라 가장 cheapest plan을 고름

Query cost 측정  
- 주로 query에 답하는 시간으로 측정됨
- Disk access가 주요한 cost
- 간단함을 위해 여기서는 disk로부터 block transfer 횟수와 seek 횟수를 사용
    - $t_{T}$: 한 block을 transfer하는 시간
    - $t_{S}$: 한 seek을 하는 시간 (disk seek + rotational delay)

Selection Operation
- File scan
- A1: linear search
    - File을 처음부터 끝까지 scan하며 selection condition 확인
    - Cost: $b_{r}$ * $t_{T}$ + 1 *  $t_{S}$
        - $b_{r}$은 relation r의 records들이 가진 block 갯수, file의 block들이 연속적이라 seek는 한번만
    - 만약 selection 조건이 key이면 $b_{r}$/2 * $t_{T}$ + 1 *  $t_{S}$
    
- Index scan
- A2 (primary index, equality on key)
    - Cost: ($h_{i}$ + 1) * ($t_{T}$ + $t_{S}$)
- A3 (primary index, equality on nonkey)
    - Cost: $h_{i}$ * ($t_{T}$ + $t_{S}$) + $t_{S}$ + $t_{T}$ * b
    - b는 matching record를 포함하는 block의 갯수
- A4 (secondary index, equality)
    - Search-key가 candidate key인 경우
        - Cost: ($h_{i}$ + 1) * ($t_{T}$ + $t_{S}$)
    - Search-key가 candidate key가 아닌 경우
        - Cost: ($h_{i}$ + n) * ($t_{T}$ + $t_{S}$): 매우 비쌈
- A5 (primary index, comparison)
    - A\>=V의 경우 V보다 크거나 같은 첫 tuple의 index를 찾고 sequentially scan
    - A\<=V의 경우 처음부터 scan, index를 사용하지 않음
- A6 (secondary index, comparison)
    - Pointer와 I/O 비용이 커서 linear file scan이 대부분 더 나음

External Sort-Merge
- M을 memory size라고 하면
- M block을 읽고 정렬함
- 정렬한 M block을 run에 저장
- 총 run의 수를 N이라고 하면
    - N < M인 경우
        - 각 run에서 block을 하나씩 읽고 가장 작은 record를 output buffer에 저장
        - Page가 비면 다음 block을 가져옴
        - 모두 빌 때까지 반복
    - N >= M인 경우
        - M - 1 크기의 runs로 merge됨
        - 하나의 run으로 merge될 때까지 반복

Nested-Loop Join
- r의 한 tuple마다 s의 모든 tuple을 돌며 더하고 결과에 추가함
- r을 outer relation, s를 inner relation이라고 함

Block Nested-Loop Join
- Block 단위로 수행함
- 개선 방법
    - M - 2 개의 block씩 읽고 나머지 2개의 block을 inner relation buffer와 output buffer로 사용
    - equi-join attribute가 inner relation의 key를 형성하면, 첫번째 match에 멈춤
    - 마지막 block을 활용하기 위해 scan 방향을 차례로 바꿈

Hash Join
- equi-join과 natural join에 적용 가능
- Hash function h가 각 relation의 tuple들을 partition
- h는 JAttrs (equi-join에 사용되는 atrribute)를 {0, 1, ..., n-1}로 mapping
- 같은 partition에 있는 tuple끼리 비교하면 됨

Hash Join Algorithm
- h로 s, r을 partition
- 각 i에 대해
    - $s_i$를 메모리로 불러옴
        - 또 다른 memory-hash를 생성함
    - $r_i$에 있는 tuple을 disk로부터 하나씩 읽음
        - memory-hash로 matching해서 이어 붙인걸 결과에 추가함
- n과 h는 $s_i$가 메모리에 잘 들어가게 조정