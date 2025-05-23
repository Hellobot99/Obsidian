
# 표준 입출력 함수

- 이식성이 좋음
- 버퍼링을 통해 성능 향상

	clock_gettime 함수 
	- 시간 측정을 위한 표준 함수
	```c
	clock_gettime(CLOCK_MONOTONIC, &start);
	clock_gettime(CLOCK_MONOTONIC, &end);
	```

---
# 소켓 통신에서의 표준 입출력 함수 사용

- 양방향 통신이 쉽지 않음.
- fflush 함수의 호출이 필요
- 소켓 디스크립터(int)를 FILE 구조체 포인터로 변환해야함

---
## fdopen 함수

- int fd -> FILE \*fp

```c
FILE* fdopen(int fd, const char *mode);

//example
sock = socket(PF_INET, SOCK_STREAM, 0);
readfp = fdopen(sock, "r"); 
writefp = fdopen(sock, "w");
//입출력 분리
```

---
## fileno 함수

- FILE \*fp -> int fd

```c
int fileno(FILE* stream);
```

---

```c
서버

readfp = fdopen(clnt_sock, "r"); 
writefp = fdopen(clnt_sock, "w"); 

while(!feof(readfp)) // feof(readfp): 클라이언트 접속 종료 확인 
{ 
	fgets(message, BUF_SIZE, readfp); 
	fputs(message, writefp); 
	fflush(writefp); 
}

fclose(readfp); 
fclose(writefp);
```

```c
클라이언트

readfp = fdopen(sock, "r"); 
writefp = fdopen(sock, "w");

fputs(message, writefp); 
fflush(writefp); 
fgets(message, BUF_SIZE, readfp); 
printf("Message from server: %s", message);

fclose(writefp); 
fclose(readfp);
```

