## 다중 접속 서버의 구현 방법

1. Multiprocess Server
2. Multiplexing Server
3. Multithread Server

# Multiprocess Server

## fork() 함수

- 프로세스의 복사본을 생성
	
- 실패 시 -1 리턴, 성공 시 새로운 PID를 리턴
	
- 이미 실행 중인 프로세스의 메모리 영역까지 동일하게 복사

```c
pid = fork(); 

if(pid == 0) //Child
	printf("Child has x= %d\n", ++x); 
else //Parent
	printf("Parent has x= %d, Child pid= %d\n", --x, pid);
```

## 좀비 프로세스

- 실행이 완료 되었음에도 소멸되지 않은 프로세스
- 자식 프로세스의 반환값을 부모 프로세스가 받아야 프로세스가 소멸

## 좀비 프로세스의 소멸 방법

### 1. wait() 함수 (Blocking)

- pid_t wait(int \*status)
	
- int WIFEXITED(int status) 함수를 통해 자식 프로세스가 정상적으로 종료되었는지 확인

```c
wait(&status); 

if(WIFEXITED(status)) 
	printf("Child #1 sent: %d \n", WEXITSTATUS(status));
```


### 2. waitpid() 함수 (Non-Blocking possible)

- pid_t wiatpid(pid_t pid, int \*status, int options)
	
-  WHOWHANG 을 통해 자식 프로세스가 존재하지 않아도 블록킹 상태에 있지 않는다

```c
while((cid = waitpid(-1, &status, WNOHANG))==0) { 
	sleep(3); 
	puts("sleep 3sec."); 
} 

printf("exit while: cid= %d\n", cid); 

if(WIFEXITED(status)) 
	printf("Child sent %d \n", WEXITSTATUS(status));
```

## Signal 함수

- typedef void (\*sighandler_t)(int)
	ex) signal(SIGCHLD, mychild); 자식 프로세스가 종료되면 mychild 함수를 호출
	
- unsigned int alarm(unsigned int seconds)
	
- unsigned int sleep(unsigned int seconds)

```c
void timeout(int sig) { 
	if(sig==SIGALRM) 
		puts("Time out!"); 
	alarm(2); 
}

int main(int argc, char *argv[]) { 
	int i; 
	signal(SIGALRM, timeout); 
	signal(SIGINT, keycontrol); 
	alarm(2);
}
```

## Sigaction 함수

- int sigaction(int signum, const struct sigaction \*act, struct sigaction \*oldact)
	 성공 시 0, 실패 시 1 반환

```c
// sigaction 구조체 정보
struct sigaction { 
	union { 
		void (*sa_handler)(int); // Old style handler 
		void (*sa_sigaction)(int, siginfo_t *, void *); // New style handler 
	} 
	sigset_t sa_mask; // signals to block while handling 
	int sa_flags;
	void (*sa_restorer) (void); // Restore handler 
}
```

- sa_mask: handler 실행 중 임시로 차단할 signal 집합(차단할 signal이 없는 경우 0으로 설정)
	
- sa_flags: handler의 동작 방식을 지정 (0으로 설정)

```c
int main(int argc, char *argv[]) { 
	int i; 
	struct sigaction act; 
	
	act.sa_handler = timeout; 
	sigemptyset(&act.sa_mask); //mask 비트 0으로 초기화(사용 x)
	act.sa_flags = 0; 
	
	sigaction(SIGALRM, &act, 0); 
	alarm(2);
}
```

# 멀티 프로세스 기반 다중접속 서버 모델

## 서버


![[Multiprocess1.png]]

- 서버는 accept 만, 그 후 만들어진 자식 프로세스가 클라이언트에 서비스 제공

![[Multiprocess2.png]]

- parent process는 accept만 하기 때문에 클라이언트 소켓(통신 용)이 필요 없다
- child process 는 클라이언트 통신만 하기 때문에 서버 소켓(accept 용)이 필요 없다

```c
while(1) { 
	adr_sz = sizeof(clnt_adr); 
	clnt_sock = accept(serv_sock, (struct sockaddr*)&clnt_adr, &adr_sz); 
	
	if(clnt_sock == -1) continue; 
	else printf("new client connected: %d\n", clnt_sock);
	
	pid = fork(); 
	
	if(pid == -1) { // parent process
		close(clnt_sock);
		continue; 
	} 
	if(pid == 0) { //child process
		close(serv_sock); 
		while((str_len=read(clnt_sock, buf, BUF_SIZE)) != 0) { 
			// 클라이언트와 통신
		} 
		close(clnt_sock); 
		puts("client disconnected...");
		return 0; 
	}
}
```

## 클라이언트

![[Multiprocess3.png]]

- 입출력 루틴을 분할 하여 입출력을 동시에 진행 할 수 있다

```c
if(connect(sock, (struct sockaddr*)&serv_adr, sizeof(serv_adr))==-1) 
	error_handling("connect() error!"); 
	
pid=fork(); 

if(pid==0) 
	write_routine(sock, buf); 
else 
	read_routine(sock, buf); close(sock);
```