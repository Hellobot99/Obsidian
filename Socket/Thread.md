## 개요

C에서 멀티스레딩(Multithreading)은 주로 POSIX Threads(Pthreads)를 통해 구현합니다. 이는 `pthread` 라이브러리를 사용하여 여러 실행 흐름을 동시에 처리할 수 있게 합니다.

---
## 주요 함수

### 1. `pthread_create()`

- 새 스레드를 생성합니다.
    
- **형식:**
    

```c
int pthread_create(pthread_t *thread, const pthread_attr_t *attr,
                   void *(*start_routine)(void *), void *arg);
```

- **매개변수:**
    
    - `thread`: 생성된 스레드 ID를 저장할 변수
        
    - `attr`: 스레드 속성 (NULL이면 기본 속성)
        
    - `start_routine`: 스레드가 실행할 함수
        
    - `arg`: 함수에 전달할 인자
        
### 2. `pthread_join()`

- 특정 스레드가 종료될 때까지 기다립니다.
    
- **형식:**
    

```c
int pthread_join(pthread_t thread, void **retval);
```

### 3. `pthread_exit()`

- 현재 스레드를 종료합니다.
    
- **형식:**
    

```c
void pthread_exit(void *retval);
```

### 4. `pthread_mutex_*()`

- 스레드 간의 동기화를 위한 뮤텍스(Mutex) 관련 함수
    
- 주요 함수:
    
    - `pthread_mutex_init()`
        
    - `pthread_mutex_lock()`
        
    - `pthread_mutex_unlock()`
        
    - `pthread_mutex_destroy()`
        

---
## 🧵 예제 코드

```c
#include <pthread.h>
#include <stdio.h>
#include <unistd.h>

void* print_message(void* arg) {
    char* message = (char*)arg;
    for (int i = 0; i < 5; ++i) {
        printf("%s\n", message);
        sleep(1);
    }
    return NULL;
}

int main() {
    pthread_t thread1, thread2;

    pthread_create(&thread1, NULL, print_message, (void*)"Thread 1 실행 중");
    pthread_create(&thread2, NULL, print_message, (void*)"Thread 2 실행 중");

    pthread_join(thread1, NULL);
    pthread_join(thread2, NULL);

    printf("모든 스레드 종료\n");
    return 0;
}
```

---
## ⚠️ 주의사항

- `pthread_create()`를 호출하면 **즉시 스레드가 실행**되므로, 스레드 간 동기화 필요 시 `mutex` 또는 `condition variable`을 사용해야 합니다.
    
- 스레드는 전역 변수나 힙을 공유하기 때문에 **경쟁 조건(Race Condition)**이 발생할 수 있습니다.
    
- `-lpthread` 옵션을 컴파일 시 추가해야 합니다.
    

```bash
gcc -o thread_example thread_example.c -lpthread
```