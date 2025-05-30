# Branch and Bound (분기 한정법)

- 백트래킹의 개선된 형태. 백트래킹은 DFS 기반이지만, Branch and Bound는 **최적화 문제**에 특화됨
    
- 탐색 방식
    
    - BFS (queue 사용)
	    
	    ![[bound1.png]]
        
    - Best-First Search (priority queue 사용, 더 효율적)
	    
	    ![[bound2.png]]
        

## 핵심 아이디어

- 각 노드는 현재 상태 + 이 상태에서 기대 가능한 해답의 하한선(bound)을 포함
    
- bound가 현재 최적해(best)보다 크거나 같으면 **가지치기(pruning)**
    

---

## 0-1 Knapsack 문제 예시 (Best-First Search)

```c
void best_first_branch_and_bound(state_space_tree T, int &best) {
    priority_queue_of_node PQ;
    node u, v;

    initialize(PQ);
    v = root of T;
    best = value(v);
    insert(PQ, v);

    while (!empty(PQ)) {
        remove(PQ, v);
        if (bound(v) is better than best) {
            for (each child u of v) {
                if (value(u) is better than best)
                    best = value(u);
                if (bound(u) is better than best)
                    insert(PQ, u);
            }
        }
    }
}
```

- best: 현재까지 발견한 최적해의 값
    
- bound(u): u 노드에서 가능한 최고 예상 값 (낙관적 추정치)
    

---

# Traveling Salesperson Problem (TSP)

- 모든 도시를 한 번씩 방문하고 시작 도시로 돌아오는 순회 경로 중에서 **가중치 합이 최소인** 해밀턴 순환 경로를 찾는 문제

---

## Dynamic Programming 방식

- 부분집합 A를 비트마스킹으로 표현
    
- `D[i][A]`: 정점 i에서 시작하여 A 집합의 도시를 거쳐 1번 도시로 돌아가는 최소 비용
    
- 점화식:
    

```c
D[i][A] = min(W[i][j] + D[j][A - {j}])
```

- 보조 함수
    
    - `count(A)`: A에 포함된 도시 수
        
    - `isIn(i, A)`: 도시 i가 A에 포함되어 있는지 확인
        
    - `diff(A, j)`: A에서 도시 j를 뺀 집합

![[bound3.png]]

---

## Branch and Bound 방식 (TSP)

### 상태 공간 트리

- 각 노드는 현재까지 선택된 경로(예: `[1, 3, 4]`)를 저장
    
- 방문하지 않은 도시의 **최소 간선 가중치**를 이용해 bound 계산
    

### bound 계산 예시

- 현재 경로 길이 + 방문하지 않은 각 도시에서 나가는 최소 간선의 합(출발점은 포함)
    

### Best-First 방식

- 우선순위 큐를 사용하여 bound가 낮은 노드를 먼저 확장
    
- 모든 도시를 방문한 경우(leaf 노드)는 실제 경로 비용으로 최적해 갱신

![[bound4.png]]

---

## TSP Branch and Bound 구현 요약

```c++
typedef struct node {
    int level;
    ordered_set path;
    int bound;
} nodetype;
```

- `length(path)`: 현재 경로 길이 계산
    
- `bound(node)`: 낙관적 비용 추정치
    
- `hasOutgoing(i, path)`: 경로에서 i가 출발지로 사용되었는지
    
- `hasIncoming(j, path)`: 경로에서 j가 도착지로 사용되었는지

![[bound5.png]]