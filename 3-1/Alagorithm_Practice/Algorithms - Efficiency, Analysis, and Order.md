## 1.1 Algorithms

- **알고리즘**
    
    - 문제에 대한 해결 과정을 계산하는 고정된 순서의 행동.
        
- **전자계산 알고리즘**
    
    - 컴퓨터를 이용하여 문제를 해결하는 한정된 순서의 명령.
        

### 문제(Problem) 및 인스턴스(Instance)

- **문제**: 해결해야 할 문제점.
    
- **인스턴스**: 문제의 한 특정 값을 바로 지정한 경우.
    

---

## 순차 탐색 (Sequential Search)

### 알고리즘 1.1

```cpp
void seqsearch(int n, const int S[], int x, int& location) {
    location = 1;
    while (location <= n && S[location] != x)
        location++;
    if (location > n)
        location = 0;
}
```

### 예제

**Input**

```
6
10 7 11 5 13 8
5
```

**Output**

```
4
```

---

## 배열 합계 (Adding Array Members)

### 알고리즘 1.2

```cpp
int sum(int n, vector<int>& S) {
    int result = 0;
    for (int i = 1; i <= n; i++)
        result += S[i];
    return result;
}
```

### 예제

**Input**

```
6
10 7 11 5 13 8
```

**Output**

```
54
```

---

## 교환 정렬 (Exchange Sort)

### 알고리즘 1.3

```cpp
void exchangesort(int n, vector<int>& S) {
    for (int i = 1; i <= n; i++)
        for (int j = i + 1; j <= n; j++)
            if (S[i] > S[j])
                swap(S[i], S[j]);
}
```

### 예제

**Input**

```
6
10 7 11 5 13 8
```

**Output**

```
5 7 8 10 11 13
```

---

## 행렬곱셉 (Matrix Multiplication)

### 알고리즘 1.4

```cpp
typedef vector<vector<int>> matrix_t;

void matrixmult(int n, matrix_t A, matrix_t B, matrix_t& C) {
    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= n; j++) {
            C[i][j] = 0;
            for (int k = 1; k <= n; k++)
                C[i][j] += A[i][k] * B[k][j];
        }
}
```

### 예제

**Input**

```
2
2 3
4 1
5 7
6 8
```

**Output**

```
28 38
26 36
```

---

## 알고리즘의 5가지 요건

- 0개 이상의 입력
    
- 1개 이상의 출력
    
- 명확성 (Unambiguity)
    
- 한정된 계산 (Finiteness)
    
- 수행 가능성 (Feasibility)
    

---

## 1.2 효율적인 알고리즘의 중요성

### 이진 탐색 (Binary Search)

### 알고리즘 1.5

```cpp
void binsearch(int n, vector<int>& S, int x, int& location) {
    int low = 1, high = n;
    location = 0;
    while (low <= high && location == 0) {
        int mid = (low + high) / 2;
        if (x == S[mid])
            location = mid;
        else if (x < S[mid])
            high = mid - 1;
        else
            low = mid + 1;
    }
}
```

### 예제

**Input**

```
6
5 7 8 10 11 13
5
```

**Output**

```
1
```

---

## 피보나치 수열 (Fibonacci Sequence)

### 알고리즘 1.6 (재귀)

```cpp
typedef unsigned long long LongInt;

LongInt fib(LongInt n) {
    if (n <= 1) return n;
    else return fib(n - 1) + fib(n - 2);
}
```

### 알고리즘 1.7 (반복)

```cpp
typedef unsigned long long LongInt;

LongInt fib2(int n) {
    vector<LongInt> F;
    if (n <= 1)
        return n;
    else {
        F.push_back(0);
        F.push_back(1);
        for (int i = 2; i <= n; i++)
            F.push_back(F[i-1] + F[i-2]);
        return F[n];
    }
}
```