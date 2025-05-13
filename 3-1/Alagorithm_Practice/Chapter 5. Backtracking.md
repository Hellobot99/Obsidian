# 백트래킹 (Backtracking)

- 조건에 맞는 부분 집합을 찾기 위해 **DFS 방식**으로 탐색함
    
- 조건에 부합하지 않는 경로는 **미리 가지치기(Pruning)하여 탐색 속도를 높임
    
- **State Space Tree**: 가능한 모든 경우를 트리 형태로 표현한 것
    
- **Promising**: 현재 경로가 유망한지를 판단하는 함수
    
- **Pruning**: promising하지 않으면 더 이상 하위 노드를 탐색하지 않음
    

---

## n-Queens Problem

- `n x n` 체스판에 `n`개의 퀸을 서로 공격하지 않게 배치하는 문제
    
- 각 퀸은 서로 다른 **행**에 위치하므로, **열**만 결정하면 됨
    
- Pruning 조건: 같은 열 또는 대각선에 퀸이 있는 경우

![[nqueen.png]]

```cpp
void queens(int i) {
    int j;
    if (promising(i)) {
        if (i == n)
            cout << col[1] through col[n];
        else {
            for (j = 1; j <= n; j++) {
                col[i + 1] = j;
                queens(i + 1);
            }
        }
    }
}

bool promising(int i) {
    int k = 1;
    bool flag = true;
    while (k < i && flag) {
        if ((col[i] == col[k]) || (abs(col[i] - col[k]) == i - k))
            flag = false;
        k++;
    }
    return flag;
}
```

---

## The Sum of Subsets Problem

- 부분집합의 합이 `W`가 되는 모든 경우를 찾는 문제
    
- Pruning 조건: 오름차순 정렬 후, `weight + w[i+1] > W` 이거나 `weight + total < W` 인 경우

![[sumofsubset.png]]

```c
void sum_of_subsets(int i, int weight, int total) {
    if (promising(i, weight, total)) {
        if (weight == W)
            cout << include[1] through include[i];
        else {
            include[i + 1] = true;
            sum_of_subsets(i + 1, weight + w[i + 1], total - w[i + 1]);
            include[i + 1] = false;
            sum_of_subsets(i + 1, weight, total - w[i + 1]);
        }
    }
}

bool promising(int i, int weight, int total) {
    return (weight + total >= W) && (weight == W || weight + w[i + 1] <= W);
}
```

---

## m-Coloring Problem

- 무방향 그래프에서 인접한 정점끼리 **다른 색**으로 칠하는 문제
    
- Pruning 조건: 인접 정점 중 이미 같은 색으로 칠해진 것이 있으면 false


![[mcoloring.png]]

```c
void m_coloring(int i) {
    int color;
    if (promising(i)) {
        if (i == n)
            cout << vcolor[1] through vcolor[n];
        else {
            for (color = 1; color <= m; color++) {
                vcolor[i + 1] = color;
                m_coloring(i + 1);
            }
        }
    }
}

bool promising(int i) {
    int j = 1;
    bool flag = true;
    while (j < i && flag) {
        if (W[i][j] && vcolor[i] == vcolor[j])
            flag = false;
        j++;
    }
    return flag;
}
```

---

## Hamiltonian Circuits Problem

- **해밀턴 순환**: 시작점에서 출발하여 **모든 정점을 정확히 한 번씩 방문**하고 다시 시작점으로 돌아오는 경로
    
- Pruning 조건:
    
    - 인접하지 않은 정점으로 이동하는 경우
        
    - 이미 방문한 정점을 또 방문하는 경우
        
    - 마지막 정점이 시작 정점으로 돌아가지 못하는 경우


```c
void hamiltonian(int i) {
    int j;
    if (promising(i)) {
        if (i == n - 1)
            cout << vindex[0] through vindex[n - 1];
        else {
            for (j = 2; j <= n; j++) {
                vindex[i + 1] = j;
                hamiltonian(i + 1);
            }
        }
    }
}

bool promising(int i) {
    int j;
    bool flag;
    if (i == n - 1 && !W[vindex[n - 1]][vindex[0]])
        flag = false;
    else if (i > 0 && !W[vindex[i - 1]][vindex[i]])
        flag = false;
    else {
        flag = true;
        j = 1;
        while (j < i && flag) {
            if (vindex[i] == vindex[j])
                flag = false;
            j++;
        }
    }
    return flag;
}
```
