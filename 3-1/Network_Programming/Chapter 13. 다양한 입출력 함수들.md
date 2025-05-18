# 소켓 입출력 함수 정리
  
## send() 함수

```c
ssize_t send(int sockfd, const void *buf, size_t nbytes, int flags);
```

- 데이터를 소켓으로 전송

- 성공 시 전송한 바이트 수 반환, 실패 시 -1

## recv() 함수

```c
ssize_t recv(int sockfd, void *buf, size_t nbytes, int flags);
```

- 소켓으로부터 데이터 수신

- 성공 시 수신한 바이트 수 반환, 실패 시 -1
  
---

## Flags 설명


 **MSG_OOB** 
	 긴급 데이터 전송 (Out-of-Band)
	수신 측은 `SIGURG` 시그널로 감지하고 `recv(..., MSG_OOB)`로 읽음

**MSG_PEEK** 
	입력 버퍼에 데이터가 있는지 확인 (읽어도 버퍼에서 제거되지 않음)

**MSG_DONTROUTE**
	라우팅 테이블을 거치지 않고 직접 전송 (로컬 네트워크용)

**MSG_DONTWAIT** 
	Non-blocking I/O, 대기하지 않고 즉시 반환

**MSG_WAITALL**
	지정한 바이트 수만큼 모두 수신될 때까지 대기

  

---
## writev() 함수

```c

ssize_t writev(int filedes, const struct iovec *iov, int iovcnt);

```


- 여러 버퍼를 **모아서 한 번에 전송**

- 시스템 콜을 줄여 성능 향상

### 예시

```c
struct iovec vec[2];
char buf1[] = "ABCDEFG";
char buf2[] = "1234567";
int str_len;  

vec[0].iov_base = buf1;
vec[0].iov_len = strlen(buf1);
vec[1].iov_base = buf2;
vec[1].iov_len = strlen(buf2);  

str_len = writev(1, vec, 2);
```

> 결과: `ABCDEFG1234567` 출력 및 `Write bytes: 14`

---
## 📥 readv() 함수

  
```c
ssize_t readv(int filedes, const struct iovec *iov, int iovcnt);
```

- **입력을 여러 버퍼에 분산 저장**
- 한 번의 호출로 여러 공간에 나눠서 수신 가능

### 예시

```c
struct iovec vec[2];
char buf1[BUF_SIZE] = {0};
char buf2[BUF_SIZE] = {0};
int str_len;  

vec[0].iov_base = buf1;
vec[0].iov_len = 5;
vec[1].iov_base = buf2;
vec[1].iov_len = BUF_SIZE;  

str_len = readv(0, vec, 2);
```

> 예시 입력: `I like TCP/IP socket programming.`  
> 출력 결과:
```
First message: I lik
Second message: e TCP/IP socket programming.
```

---
  
## 정리  

- `send/recv`: 단일 버퍼 전송/수신

- `writev/readv`: 다중 버퍼 처리, 시스템 콜 줄이기

- `MSG_OOB`: 긴급 알림, **빠른 전송이 아님** → `SIGURG` 핸들링 필요

- `MSG_PEEK`: 버퍼 내용 미삭제 확인용

- `MSG_DONTWAIT`: 논블로킹 처리 

---