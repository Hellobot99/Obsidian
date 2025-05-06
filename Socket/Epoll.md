##  `epoll`이란?
  

`epoll`은 리눅스에서 대규모 네트워크 서버를 위한 고성능 이벤트 통지(Event Notification) 메커니즘입니다. `select()`나 `poll()`과는 달리 수천 개의 파일 디스크립터를 보다 효율적으로 처리할 수 있습니다.

##  주요 함수  

### 1. epoll_create  

```c
int epoll_create(int size);
```

- epoll 인스턴스를 생성하고 파일 디스크립터를 반환.

- `size`는 과거에는 큐의 크기 역할이었지만, 현재는 의미 없음 (0보다 큰 값 필요).

---
### 2. epoll_ctl

```c
int epoll_ctl(int epfd, int op, int fd, struct epoll_event *event);
```

- epoll 인스턴스에 감시할 fd를 추가/수정/삭제.

매개변수 설명 

`epfd` : epoll 인스턴스 파일 디스크립터 

`op` : `EPOLL_CTL_ADD`, `EPOLL_CTL_MOD`, `EPOLL_CTL_DEL` 

`fd` : 감시할 파일 디스크립터 

 `event` : 감시할 이벤트 정보   

---
### 3. epoll_wait  

```c
int epoll_wait(int epfd, struct epoll_event *events, int maxevents, int timeout);
``` 

- 등록된 fd 중 이벤트가 발생한 것들을 반환.  

---
## 구조체

```c
struct epoll_event {
    uint32_t events;  // 이벤트 종류 (EPOLLIN, EPOLLOUT 등)
    epoll_data_t data; // 사용자 정의 데이터 (fd 등)
};
```  

---
## 주요 이벤트 플래그

- `EPOLLIN`: 읽기 가능
- 
- `EPOLLOUT`: 쓰기 가능
- 
- `EPOLLERR`: 에러 발생
- 
- `EPOLLET`: 엣지 트리거(Edge Trigger)

- `EPOLLLT`: 레벨 트리거(Level Trigger, 기본값)  

---
## 예제: epoll을 이용한 에코 서버  

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <fcntl.h>
#include <errno.h>
#include <sys/epoll.h>
#include <arpa/inet.h>
#include <sys/socket.h>  

#define PORT 8080
#define MAX_EVENTS 10
#define BUF_SIZE 1024  

int main() {
    int server_fd, client_fd, epfd, nfds;
    struct sockaddr_in address;
    socklen_t addrlen = sizeof(address);
    struct epoll_event ev, events[MAX_EVENTS];
    char buffer[BUF_SIZE];  

    server_fd = socket(AF_INET, SOCK_STREAM, 0);
    address.sin_family = AF_INET;
    address.sin_addr.s_addr = INADDR_ANY;
    address.sin_port = htons(PORT);  

    bind(server_fd, (struct sockaddr*)&address, sizeof(address));

    listen(server_fd, 5);  

    epfd = epoll_create(1);
    ev.events = EPOLLIN;
    ev.data.fd = server_fd;
    epoll_ctl(epfd, EPOLL_CTL_ADD, server_fd, &ev);  

    while (1) {
        nfds = epoll_wait(epfd, events, MAX_EVENTS, -1);
        for (int i = 0; i < nfds; i++) {
            if (events[i].data.fd == server_fd) {
                client_fd = accept(server_fd, (struct sockaddr*)&address,
                 &addrlen);
                ev.events = EPOLLIN;
                ev.data.fd = client_fd;
                epoll_ctl(epfd, EPOLL_CTL_ADD, client_fd, &ev);
            } else {
                int bytes = read(events[i].data.fd, buffer, BUF_SIZE);
                if (bytes <= 0) {
                    close(events[i].data.fd);
                } else {
                    buffer[bytes] = '\0';
                    printf("클라이언트: %s\n", buffer);
                    write(events[i].data.fd, buffer, bytes);
                }
            }
        }
    }  

    return 0;
}
```

---
## 참고사항  

- `epoll`은 `select()`/`poll()`보다 성능이 우수하며, 대규모 서버에 적합합니다.

- 기본은 Level Trigger이지만 `EPOLLET` 플래그를 사용하면 Edge Trigger로 동작함.

- 엣지 트리거는 성능은 높지만 구현이 복잡합니다 (non-blocking + 루프 필수).