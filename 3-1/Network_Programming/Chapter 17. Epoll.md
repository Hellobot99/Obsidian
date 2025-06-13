# **1. select 기반 IO 멀티플렉싱의 한계**

- select 함수는 운영체제 커널이 아닌, 사용자 함수 레벨에서 동작함
    
- select 호출 시마다 모든 파일 디스크립터 정보를 매번 전달해야 하므로 비효율적임
    
- select 방식은 운영체제에 관찰 대상을 등록하지 않으므로, 매 반복마다 반복문으로 전체 파일 디스크립터를 검사해야 함
    
- 다양한 OS에서 호환성은 좋으나, 대규모 접속자 처리에 비효율적임

---

# **2. epoll의 등장과 장점**

- epoll은 Linux에서 제공하는 고성능 IO 멀티플렉싱 인터페이스
    
- 상태 변화(이벤트)를 커널이 직접 관리하므로, 매번 정보를 전달할 필요 없음
    
- 관찰 대상의 변경이 있을 때만 커널에 알리면 됨
    
- 반복문으로 전체 파일 디스크립터를 검사하지 않아도 됨
    
- 대규모 동시 접속 처리에 효율적임

---

## **3. epoll 관련 함수와 구조체**

## **주요 함수**

- `epoll_create(size)` : epoll 인스턴스(저장소) 생성
    
- `epoll_ctl(epfd, op, fd, event)` : epoll 저장소에 파일 디스크립터 등록, 삭제, 변경
    
    - op:
        
        - `EPOLL_CTL_ADD` (등록)
            
        - `EPOLL_CTL_DEL` (삭제)
            
        - `EPOLL_CTL_MOD` (변경)
            
- `epoll_wait(epfd, events, maxevents, timeout)` : 이벤트 발생 대기 및 확인

## **구조체**

```c
struct epoll_event {     
	__uint32_t events;   // 이벤트 유형    
	epoll_data_t data;   // 사용자 데이터 (fd 등) 
}; 

typedef union epoll_data {     
	void *ptr;   
	int fd;    
	__uint32_t u32;
	__uint64_t u64; 
} epoll_data_t;
```

---

# **4. epoll 이벤트 유형**

| 이벤트 상수       | 설명                       |
| ------------ | ------------------------ |
| EPOLLIN      | 읽을 데이터가 도착한 경우           |
| EPOLLOUT     | 쓸 수 있는 상태 (출력 버퍼가 비어 있음) |
| EPOLLPRI     | OOB(Out-of-Band) 데이터 도착  |
| EPOLLRDHUP   | 연결 종료 또는 half-close      |
| EPOLLERR     | 에러 발생                    |
| EPOLLET      | Edge Trigger 모드로 동작      |
| EPOLLONESHOT | 한 번만 이벤트 감지 후 자동 해제      |

- 여러 이벤트를 비트 OR 연산으로 함께 등록 가능
    

---

# **5. epoll 기본 사용 예시**

```c
#include <sys/epoll.h> 

int epoll_fd = epoll_create(100);

struct epoll_event event;
event.events = EPOLLIN;
event.data.fd = sockfd; 

epoll_ctl(epoll_fd, EPOLL_CTL_ADD, sockfd, &event);

struct epoll_event events[MAX_EVENTS]; // 이벤트 발생 시 여기에 담음

int nfds = epoll_wait(epoll_fd, events, MAX_EVENTS, -1);
```
---

# **6. epoll 기반 에코 서버 구조**

1. 리스닝 소켓을 epoll 인스턴스에 등록 (EPOLLIN)
    
2. epoll_wait로 이벤트 대기
    
3. 이벤트 발생 시:
    
    - 리스닝 소켓: accept로 클라이언트 소켓 생성, epoll에 등록
        
    - 클라이언트 소켓: 데이터 수신(read), 0이면 연결 종료 및 epoll에서 삭제, 아니면 에코(write)

```c
for(i=0; i < event_cnt; i++) 
{
	if(ep_events[i].data.fd == serv_sock) 
	{
		adr_sz = sizeof(clnt_adr); 
		clnt_sock = accept(serv_sock, (struct sockaddr*)&clnt_adr, &adr_sz);
		event.events = EPOLLIN; 
		event.data.fd = clnt_sock; 
		epoll_ctl(epfd, EPOLL_CTL_ADD, clnt_sock, &event); 
		printf("connected client: %d \n", clnt_sock); 
	} 
	else { 
		str_len = read(ep_events[i].data.fd, buf, BUF_SIZE); 
		if(str_len == 0) // close request! 
		{ 
			epoll_ctl(epfd, EPOLL_CTL_DEL, ep_events[i].data.fd, NULL); 
			close(ep_events[i].data.fd); 
			printf("closed client: %d \n", ep_events[i].data.fd); 
		} 
		else 
		{ 
			write(ep_events[i].data.fd, buf, str_len);
		}
	}
}
```

---

# **7. Level Trigger vs Edge Trigger**

| 구분        | Level Trigger                | Edge Trigger          |
| --------- | ---------------------------- | --------------------- |
| 동작 방식     | 버퍼에 데이터가 남아있는 동안 계속 이벤트      | 데이터가 도착하는 순간 한 번만 이벤트 |
| epoll 기본값 | Level Trigger                | EPOLLET 플래그로 명시 설정    |
| 사용 예      | 단순 서버, 데이터 송수신 상황이 다양하지 않을 때 | 복잡한 서버, 데이터 처리 분리 필요시 |

## **Edge Trigger 사용법**

- 소켓을 Non-blocking 모드로 설정 필요 (`fcntl`)
    
- 이벤트 발생 시, 버퍼에 남은 모든 데이터를 반복적으로 읽어야 함
    
- read()가 -1 반환 & errno\==EAGAIN이면 버퍼가 비어있다는 의미

```c
int flag = fcntl(fd, F_GETFL, 0); fcntl(fd, F_SETFL, flag | O_NONBLOCK);
```

---

# **8. errno 활용**

- <errno.h>를 include하여 errno 전역 변수 사용
    
- read/write 등 시스템 콜 에러 원인 파악에 사용
    
- `strerror(errno)`로 에러 메시지 문자열 반환
    
- `perror("msg")`로 에러 메시지 출력

```c
#include <errno.h> 

int main() 
{
	FILE *fp ; 
	fp = fopen ( "aaa.txt ", " r b ");
	if (fp == NULL ) 
	{ 
		printf ( "strerror: % s \ n ", strerror (errno));
		perror ( ”fopen ");
	} 
	else fclose (fp);
	return 0 ;
}
```

---

# **9. 실습 예시: epoll Edge Trigger 기반 에코 서버**

```c
void setnonblockingmode(int fd) 
{    
	int flag = fcntl(fd, F_GETFL, 0);  
	fcntl(fd, F_SETFL, flag | O_NONBLOCK); // 논 블록킹
}

while (1) 
{  
	event_cnt = epoll_wait(epfd, ep_events, EPOLL_SIZE, -1); 
	for (i = 0; i < event_cnt; i++) 
	{   
		if (ep_events[i].data.fd == serv_sock) 
		{ 
			clnt_sock = accept(...);  
			setnonblockingmode(clnt_sock);    
			event.events = EPOLLIN | EPOLLET; // edge trigger 방식으로
			event.data.fd = clnt_sock;         
			epoll_ctl(epfd, EPOLL_CTL_ADD, clnt_sock, &event); 
		} 
		else 
		{        
			while (1) 
			{ 
				str_len = read(ep_events[i].data.fd, buf, BUF_SIZE);   
				if (str_len == 0) { ... }       
				else if (str_len < 0) 
				{        
					if (errno == EAGAIN) break;   
				}
				else 
				{    
					write(ep_events[i].data.fd, buf, str_len); 
				}    
			}    
		}   
	} 
}

```

---

# **10. 결론 및 선택 기준**

![[Pasted image 20250613154526.png]]

- epoll은 대규모 동시 접속 처리에 적합하며, 커널 레벨에서 효율적으로 이벤트를 관리함
    
- Edge Trigger는 고성능 서버에 적합하나, Non-blocking IO와 반복적 데이터 처리가 필수
    
- Level Trigger는 서버 구조가 단순하거나 데이터 송수신 패턴이 다양하지 않을 때 적합
    
- 상황에 따라 적절한 트리거 방식과 IO 모델을 선택해야 함