##  `poll()` 함수란?  

`poll()` 함수는 `select()`와 비슷한 역할을 하며, 여러 파일 디스크립터(주로 소켓)의 상태를 감시할 수 있도록 해주는 함수입니다. `select()`보다 더 많은 수의 파일 디스크립터를 효율적으로 다룰 수 있다는 장점이 있습니다. 

##  함수 원형  

```c
#include <poll.h>  

int poll(struct pollfd fds[], nfds_t nfds, int timeout);
```

### 매개변수 설명  

- `fds[]`: 감시할 파일 디스크립터 구조체 배열
- `nfds`: `fds` 배열에 포함된 파일 디스크립터 개수
- `timeout`: 대기 시간 (밀리초 단위)
  - `0`: 즉시 반환 (non-blocking)
  - `-1`: 무한 대기
  - 양의 정수: 지정된 시간만큼 대기  

##  구조체 정의
  

```c

struct pollfd {
    int fd;         // 감시할 파일 디스크립터
    short events;   // 감시할 이벤트 (입력, 출력 등)
    short revents;  // 실제 발생한 이벤트
};

```
##  감시 가능한 이벤트  

- `POLLIN`: 읽기 가능

- `POLLOUT`: 쓰기 가능

- `POLLERR`: 에러 발생

- `POLLHUP`: 연결 종료

- `POLLNVAL`: 유효하지 않은 파일 디스크립터  

---
## 예제: `poll()`을 이용한 서버

  
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <arpa/inet.h>
#include <poll.h>  

#define PORT 8080
#define BUF_SIZE 1024

#define MAX_CLIENTS 10  

int main() {
    int server_fd, client_fd, addrlen;
    struct sockaddr_in address;
    struct pollfd fds[MAX_CLIENTS];
    char buffer[BUF_SIZE];  

    server_fd = socket(AF_INET, SOCK_STREAM, 0);
    address.sin_family = AF_INET;
    address.sin_addr.s_addr = INADDR_ANY;
    address.sin_port = htons(PORT);  

    bind(server_fd, (struct sockaddr*)&address, sizeof(address));
    listen(server_fd, 5);
    addrlen = sizeof(address);  

    // 초기화
    fds[0].fd = server_fd;
    fds[0].events = POLLIN;
    for (int i = 1; i < MAX_CLIENTS; i++) fds[i].fd = -1;  

    while (1) {
        int activity = poll(fds, MAX_CLIENTS, -1);
        if (activity < 0) continue;
        if (fds[0].revents & POLLIN) {
            client_fd = accept(server_fd, (struct sockaddr*)&address, 
            (socklen_t*)&addrlen);
            
            for (int i = 1; i < MAX_CLIENTS; i++) {
                if (fds[i].fd == -1) {
                    fds[i].fd = client_fd;
                    fds[i].events = POLLIN;
                    break;
                }
            }
        }  

        for (int i = 1; i < MAX_CLIENTS; i++) {
            if (fds[i].fd != -1 && (fds[i].revents & POLLIN)) {
                int bytes = read(fds[i].fd, buffer, BUF_SIZE);
                if (bytes <= 0) {
                    close(fds[i].fd);
                    fds[i].fd = -1;
                } else {
                    buffer[bytes] = '\0';
                    printf("클라이언트 %d: %s\n", i, buffer);
                    write(fds[i].fd, "메시지 수신 완료\n", strlen("메시지 수신 완료
                    \n"));
                }
            }
        }
    }  

    return 0;
}

```

---
##  참고사항  

- `poll()`은 `select()`와 달리 `fd_set` 대신 구조체 배열을 사용해 더 많은 소켓을 감시할 수 있습니다.

- `poll()`도 성능에 한계가 있으며, 대규모 서버에서는 `epoll` 사용을 고려해야 합니다.