
## 1. 쓰레드 기본 개념

### 쓰레드란?

- 쓰레드는 하나의 실행 단위를 나타냄
    
- 멀티쓰레드 프로그램은 여러 실행 지점을 가짐 (여러 Program Counter)
    
- 같은 주소 공간을 공유하며 실행됨
    

### 프로세스와 쓰레드의 차이

- 프로세스는 독립된 주소 공간을 가지며, 쓰레드는 동일한 주소 공간을 공유함
    
- 쓰레드는 각각 독립된 레지스터 집합, 프로그램 카운터, 스택을 가짐
    
- 쓰레드 간 전환은 컨텍스트 스위치(context switch)를 요구하며, 각 쓰레드는 TCB(Thread Control Block)를 가짐
    

### 왜 쓰레드를 사용할까?

1. **병렬성(Parallelism)**: 멀티코어 프로세서에서 성능 향상 가능
    
2. **I/O 블로킹 회피**: 하나의 쓰레드가 I/O 작업 중일 때 다른 쓰레드가 CPU 사용 가능
    
3. **데이터 공유 용이**: 같은 주소 공간을 사용하므로 다른 프로세스보다 공유가 간단함
    

### 쓰레드 생성 예시

```c
void *mythread(void *arg) {
    printf("%s\n", (char *)arg);
    return NULL;
}

int main() {
    pthread_t p1, p2;
    pthread_create(&p1, NULL, mythread, "A");
    pthread_create(&p2, NULL, mythread, "B");
    pthread_join(p1, NULL);
    pthread_join(p2, NULL);
    return 0;
}
```

- 실행 순서는 매번 달라질 수 있으며, 이는 운영체제의 스케줄러가 결정함

---

## 2. 경쟁 조건(Race Condition)과 컨텍스트 스위칭

### 공유 변수 업데이트 문제

- 예: `counter = counter + 1` 은 비원자적 연산 (세 단계: 읽기, 수정, 쓰기)
    
- 두 쓰레드가 동시에 이 연산을 수행하면 의도치 않은 결과 발생 가능
    

### 경쟁 조건의 발생 예시 (어셈블리 코드 기반)

```asm
mov 0x8049a1c, %eax
add $0x1, %eax
mov %eax, 0x8049a1c
```

- 두 쓰레드가 동시에 수행하면서 중간 상태에서 문맥 전환이 발생하면 값이 덮어써짐
    

### 용어 정리

- **Race Condition**: 쓰레드 실행 순서에 따라 결과가 달라지는 문제
    
- **Critical Section**: 공유 자원에 접근하는 코드 영역
    
- **Mutual Exclusion**: 동시에 한 쓰레드만 critical section에 진입할 수 있도록 보장하는 기법
    

---
## 3. 락(Lock)

### 락의 목적

- 공유 자원 접근 시 원자적(atomic) 실행 보장
    
- 락은 `lock()`과 `unlock()` 인터페이스를 제공
    

### Pthread 락 사용법

```c
pthread_mutex_t lock = PTHREAD_MUTEX_INITIALIZER;
pthread_mutex_lock(&lock);
balance = balance + 1;
pthread_mutex_unlock(&lock);
```

### 락의 성능 평가 기준

1. **상호 배제(Mutual Exclusion)**
    
2. **공정성(Fairness)**: 락을 기다리는 모든 쓰레드에게 기회 보장
    
3. **성능(Performance)**: 오버헤드 최소화
    

### Test-And-Set 기반 스핀락 구현

```c
typedef struct __lock_t {
    int flag;
} lock_t;

void init(lock_t *lock) {
    lock->flag = 0;
}

void lock(lock_t *lock) {
    while (TestAndSet(&lock->flag, 1) == 1);
}

void unlock(lock_t *lock) {
    lock->flag = 0;
}
```

- **문제점**: 스핀락은 바쁜 대기(spin-wait)로 CPU 자원 낭비
    

### 큐 기반 락

- `park()`와 `unpark()`을 통해 쓰레드를 sleep 상태로 대기시킴
    
- guard 락을 통해 큐 접근 보호
    
- 효율성 향상 및 공정성 보장

---
## 4. 조건 변수 (Condition Variable)

### 개념

- 특정 조건이 만족될 때까지 쓰레드를 대기시키는 동기화 메커니즘
    
- `pthread_cond_wait()`는 락을 해제하고 대기 큐에 쓰레드를 넣은 뒤, 깨어나면 다시 락을 획득함
    

### 예시

```c
int done = 0;
pthread_mutex_t m = PTHREAD_MUTEX_INITIALIZER;
pthread_cond_t c = PTHREAD_COND_INITIALIZER;

void thr_exit() {
    pthread_mutex_lock(&m);
    done = 1;
    pthread_cond_signal(&c);
    pthread_mutex_unlock(&m);
}

void thr_join() {
    pthread_mutex_lock(&m);
    while (done == 0)
        pthread_cond_wait(&c, &m);
    pthread_mutex_unlock(&m);
}
```

- **중요**: `while` 루프 사용 이유는 스푸리어스 웨이크업 방지
    

---
## 5. 생산자-소비자 문제

### 구조

- 생산자는 버퍼에 데이터를 넣고, 소비자는 데이터를 꺼냄
    
- 버퍼가 가득 찼을 경우 생산자는 대기, 비어있을 경우 소비자는 대기
    

### 해결: 조건 변수 2개 사용

- `empty`: 버퍼가 비었음을 알림
    
- `fill`: 버퍼가 찼음을 알림
    

```c
pthread_cond_t empty, fill;
pthread_mutex_t mutex;

void *producer(void *arg) {
    for (...) {
        pthread_mutex_lock(&mutex);
        while (count == MAX)
            pthread_cond_wait(&empty, &mutex);
        put(...);
        pthread_cond_signal(&fill);
        pthread_mutex_unlock(&mutex);
    }
}

void *consumer(void *arg) {
    for (...) {
        pthread_mutex_lock(&mutex);
        while (count == 0)
            pthread_cond_wait(&fill, &mutex);
        get(...);
        pthread_cond_signal(&empty);
        pthread_mutex_unlock(&mutex);
    }
}
```

---
## 6. 동시성 버그 유형

### 1) 원자성 위반

- 코드 블록이 원자적으로 실행되지 않음
    
- 해결: 락으로 원자성 보장
    

### 2) 순서 위반

- 실행 순서 보장이 없어서 초기화되지 않은 변수 접근 가능
    
- 해결: 조건 변수로 순서 보장
    

### 3) 교착 상태 (Deadlock)

- 두 쓰레드가 서로의 락을 기다려 무한 대기
    
- 발생 조건 (4가지 모두 만족 시 발생):
    
    1. 상호 배제
        
    2. 점유와 대기
        
    3. 비선점
        
    4. 순환 대기
        

### 교착 상태 해결 방법

1. 락 순서 통일 (Total Lock Ordering)
    
2. trylock 사용
    
3. 조건 변수로 순서 제어
    
4. 교착 상태 탐지 및 복구