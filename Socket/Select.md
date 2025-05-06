## 📌 `select()` 함수란?

  

`select()` 함수는 **다중 입출력(Multiplexing)** 처리를 위한 함수로, 여러 개의 파일 디스크립터(소켓 포함)를 동시에 감시(watch)할 수 있게 해줍니다. 즉, 어떤 소켓이 읽기, 쓰기, 예외 조건이 발생했는지 확인하고, 블로킹 없이 효율적으로 I/O를 처리할 수 있게 해줍니다.  

## 함수 원형
  

```c

#include <sys/select.h>

  

int select(int nfds, fd_set *readfds, fd_set *writefds,

           fd_set *exceptfds, struct timeval *timeout);

```

### 매개변수 설명

- `nfds`: 검사할 디스크립터의 수. 감시할 fd 중 가장 큰 값 + 1

- `readfds`: 읽기를 감시할 fd 집합

- `writefds`: 쓰기를 감시할 fd 집합

- `exceptfds`: 예외 상황을 감시할 fd 집합

- `timeout`: 대기 시간 설정 (NULL이면 무한 대기)

## 주요 매크로

- `FD_ZERO(fd_set *set)`: fd 집합 초기화

- `FD_SET(int fd, fd_set *set)`: fd를 집합에 추가

- `FD_CLR(int fd, fd_set *set)`: fd를 집합에서 제거

- `FD_ISSET(int fd, fd_set *set)`: fd가 집합에 포함되어 있는지 확인

  

---

## 예제: 다중 클라이언트 처리 서버

  
```c

#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <arpa/inet.h>
#include <sys/socket.h>
#include <sys/time.h>
#include <sys/types.h>  

#define PORT 8080

#define BUF_SIZE 1024

int main() {

    int server_fd, client_fd, max_fd, activity, valread, addrlen;
    struct sockaddr_in address;
    char buffer[BUF_SIZE];
    fd_set readfds;  

    // 소켓 생성
    server_fd = socket(AF_INET, SOCK_STREAM, 0);
    address.sin_family = AF_INET;
    address.sin_addr.s_addr = INADDR_ANY;
    address.sin_port = htons(PORT);  

    bind(server_fd, (struct sockaddr*)&address, sizeof(address));
    listen(server_fd, 5);
    addrlen = sizeof(address);

    while (1) {
        FD_ZERO(&readfds);
        FD_SET(server_fd, &readfds);
        max_fd = server_fd;  

        // select 호출

        activity = select(max_fd + 1, &readfds, NULL, NULL, NULL);  

        if (FD_ISSET(server_fd, &readfds)) {

            client_fd = accept(server_fd, (struct sockaddr*)&address, 
            (socklen_t*)&addrlen);
            read(client_fd, buffer, BUF_SIZE);
            printf("클라이언트로부터 메시지: %s\n", buffer);
            write(client_fd, "메시지 수신 완료\n", strlen("메시지 수신 완료\n"));
            close(client_fd);
        }

    }  
    return 0;
}

```

  

---
## 참고사항  

- `select()`는 리눅스/유닉스 환경에서 널리 사용되지만, 많은 클라이언트를 처리할 때는 `epoll`이나 `poll`이 더 효율적일 수 있습니다.

- `select()`는 한 번 호출 후 `fd_set`이 변경되므로, 매 호출 전에 다시 설정해야 합니다.