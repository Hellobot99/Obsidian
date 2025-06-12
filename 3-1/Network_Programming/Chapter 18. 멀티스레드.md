
# 스레드

- 하나의 프로세스 내부에서 동작
- data, heap 영역을 공유
- 스레드간 데이터 교환이 쉬움
- \#include <pthread.h> 

---
# ptread_create 함수

```c
int pthread_create(pthread_t *restrict thread, const pthread_attr_t *restrict attr, void *(*start_routine)(void*), void *restrict arg);

pthread_t t_id; 
int thread_param = 5;

if(pthread_create(&t_id, NULL, thread_main, (void*)&thread_param) != 0) 
{ 
	puts("pthread_create() error"); 
	return -1; 
}

void* thread_main(void *arg) 
{ 
	int i; 
	int cnt = *((int*)arg); // 보이드형 포인터 -> int형 포인터로 변환 후 값 복사
	for(i=0; i < cnt; i++) 
	{ 
		sleep(1); 
		puts("running thread"); 
	} 
	return NULL; 
}
```

- 컴파일 시 -lpthread 옵션 쓰기
- 컴파일 시 안전한 함수로 만들기 위해 -D_REENTRANT 옵션 쓰기

---
# ptread_join 함수

- 스레드 종료 대기(블로킹)

```c
int pthread_join(pthread_t thread, void **status);

pthread_t t_id; 
int thread_param = 5; 
void *thr_ret; // status에 더블포인터라서 포인터변수의 주소

if(pthread_create(&t_id, NULL, thread_main, (void*)&thread_param) != 0) 
{ 
	puts("pthread_create() error"); 
	return -1; 
} 

if(pthread_join(t_id, &thr_ret) != 0)
{ 
	puts("pthread_join() error"); 
	return -1; 
}

void* thread_main(void *arg) 
{ 
	int i; 
	int cnt = *((int*)arg); 
	// Heap 영역 사용 (프로세스와 쓰레드 사이에 데이터 공유) 
	char *msg = (char*)malloc(sizeof(char) * 50); 
	//char msg[50] = {0};// stack: warning 발생 
	strcpy(msg, "Hello, I am thread~ \n"); 
	for(i=0; i < cnt; i++) 
	{ 
		sleep(1); 
		puts("running thread"); 
	} 
	return (void*)msg; 
}
```

---
# 워커 스레드 모델

- 스레드에 일을 시키고, 그 결과를 취합하여 결과 도출
- 공유 메모리에 동시 접근 시 문제 발생 - 임계영역

![[a6.png]]

---
# 스레드 동기화

### 뮤텍스 기반 동기화

- 한번에 하나의 스레드만 공유 자원에 접근 허용
- Lock을 획득 해야만 공유 자원에 접근 가능, 다 쓰면 Lock 반납

```c
int pthread_mutex_init(pthread_mutex_t *mutex, const pthread_mutexattr_t *attr); 
int pthread_mutex_destroy(pthread_mutex_t *mutex);
int pthread_mutex_lock(pthread_mutex_t *mutex); 
int pthread_mutex_unlock(pthread_mutex_t *mutex);

pthread_t thread_id[NUM_THREAD]; 
int i; 
pthread_mutex_init(&mutex, NULL); 

for(i=0; i < NUM_THREAD; i++)
{
	if(i%2) 
		pthread_create(&(thread_id[i]), NULL, thread_inc, NULL); 
	else 
		pthread_create(&(thread_id[i]), NULL, thread_des, NULL);
}

pthread_mutex_destroy(&mutex);

void *thread_inc(void *arg) 
{
	int i; 
	pthread_mutex_lock(&mutex); 
	for(i=0; i<50000000; i++) 
		num += 1; 
	pthread_mutex_unlock(&mutex); 
	return NULL; 
}
```

---
### 세마포어 기반 동기화

- \#include <semaphore.h>
- 한번에 하나의 스레드만 공유 자원에 접근 가능

```c
int sem_init(sem_t *sem, int pshared, unsigned int value);
// sem : 세마포어의 참조 값 저장
// pshared : 0 일시 하나만 , 이외는 여러개의 프로세스가 접근 가능
// value : 세마포어 초기 값 지정
int sem_destroy(sem_t *sem);
int sem_post(sem_t *sem); // 세마포어 값 1 증가
int sem_wait(sem_t *sem); // 세마포어 값 1 감소, 값이 0 이라면 대기

--------------------------------------
sem_t sem; // 전역

int main()
{
	pthread_t t1, t2; 
	int num1 = 1, num2 = 2; 

	sem_init(&sem, 0, 1);

	pthread_create(&t1, NULL, thread_func1, &num1); 
	pthread_create(&t2, NULL, thread_func1, &num2);

	pthread_join(t1, NULL); 
	pthread_join(t2, NULL); 

	sem_destroy(&sem);
}

void *thread_func1(void *arg) 
{ 
	int thread_num = *(int *)arg;
	sem_wait(&sem);
	printf("Thread %d: Entered the critical section\n", thread_num); 
	for (int i = 0; i < 5; i++) 
	{ 
		printf("[%d] count: %d\n", thread_num, i); 
		sleep(1);
	}
	printf("Thread %d: Exit the critical section\n", thread_num); 
	sem_post(&sem);
}

--------------------------------------
//실행 순서 제어 예시, 초기 값 0
void *thread1_func(void *arg) 
{ 
	printf("Thread 1: Start work\n"); 
	sleep(1); 
	printf("Thread 1: Finish work\n"); 
	sem_post(&sem); 
	return NULL; 
} 

void *thread2_func(void *arg) 
{ 
	sem_wait(&sem); 
	printf("Thread 2: Start work\n"); 
	sleep(1); 
	printf("Thread 2: Finish work\n"); 
	return NULL; 
}
```


### 두 개의 세마포어를 활용한 접근 순서 동기화

![[a7.png]]

```c
static sem_t sem_one; 
static sem_t sem_two;

int main(int argc, char *argv[]) 
{
	pthread_t id_t1, id_t2; 
	
	sem_init(&sem_one, 0, 0); 
	sem_init(&sem_two, 0, 1);
	 
	pthread_create(&id_t1, NULL, read, NULL); 
	pthread_create(&id_t2, NULL, accu, NULL);
	 
	pthread_join(id_t1, NULL); 
	pthread_join(id_t2, NULL);
	
	sem_destroy(&sem_one);
	sem_destroy(&sem_two); 
	return 0; 
}

void *read(void *arg) 
{ 
	int i; 
	for(i=0; i<5; i++) 
	{
		fputs("Input num: ", stdout);
		sem_wait(&sem_two); 
		scanf("%d", &num); 
		sem_post(&sem_one); 
	} return NULL; 
}

void *accu(void *arg) 
{ 
	int sum=0, i; 
	for(i=0; i<5; i++) 
	{
		sem_wait(&sem_one); 
		sum += num; 
		sem_post(&sem_two); 
		printf("sum: %d\n", sum); 
	}
	printf("Result: %d\n", sum); 
	return NULL; 
}
```

---
# pthread_detach 함수

- 스레드에 할당된 메모리 자원을 소멸시킴 (논블로킹)

```c
int pthread_detach(pthread_t thread);

while(1)
{
	clnt_adr_sz = sizeof(clnt_adr); 
	clnt_sock = accept(serv_sock, (struct sockaddr*)&clnt_adr,&clnt_adr_sz);
	
	pthread_mutex_lock(&mutx); 
	clnt_socks[clnt_cnt++]=clnt_sock; 
	pthread_mutex_unlock(&mutx);
	
	pthread_create(&t_id, NULL, handle_clnt, (void*)&clnt_sock);
	pthread_detach(t_id);
}
```

