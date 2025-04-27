### 소켓 옵션 개요

- **소켓 옵션**은 소켓의 특성(버퍼 크기, 연결 유지 등)을 제어할 수 있는 설정.
    
- 옵션은 프로토콜 레벨별로 나뉨:
    
    - `SOL_SOCKET`: 소켓 자체 설정 (ex. 버퍼 크기, 주소 재사용)
        
    - `IPPROTO_IP`: IP 레벨 설정 (ex. TTL 설정)
        
    - `IPPROTO_TCP`: TCP 레벨 설정 (ex. Nagle 알고리즘 제어)

---

### 소켓 옵션 다루는 함수

## 1. `getsockopt()` : 속성 조회

**설명**

- 속성의 현재 값을 확인할 때 사용.
    
- 예: **송신 버퍼 크기**를 조회할 때.
    

**형식**

```c
int getsockopt(int sock, int level, int optname, void *optval, socklen_t *optlen);
```

optval, optlen 에 주소 값을 넣어서 값을 반환 받음

- `sock`: 속성을 확인할 속싱화 파일 디스크립터
    
- `level`: 프로토콜 레벨 (`SOL_SOCKET`, `IPPROTO_TCP` 등)
    
- `optname`: 확인할 옵션 이름
    
- `optval`: 결과를 저장할 버퍼 주소
    
- `optlen`: 버퍼 크기 (before: 설정, after: 되된 크기) 

**간단 예시** (송신 버퍼 크기 조회)

```c
int sock = socket(PF_INET, SOCK_STREAM, 0);
int snd_buf;
socklen_t len = sizeof(snd_buf);

getsockopt(sock, SOL_SOCKET, SO_SNDBUF, (void*)&snd_buf, &len); // 주소값 보내기
printf("Send Buffer Size: %d\n", snd_buf);
```

---

## 2. `setsockopt()` : 속성 설정

**설명**

- 속성 값을 변경할 때 사용.
    
- 예: **포트 번호 재사용**가능하게 만들 때.
    

**형식**

```c
int setsockopt(int sock, int level, int optname, const void *optval, socklen_t optlen);
```

- `sock`: 속성을 변경할 속싱화 파일 디스크립터
    
- `level`: 프로토콜 레벨
    
- `optname`: 변경할 옵션 이름
    
- `optval`: 설정할 값의 주소
    
- `optlen`: 값의 크기
    

**간단 예시** (주소 재사용 설정)

```c
int sock = socket(PF_INET, SOCK_STREAM, 0);
int option = 1;
setsockopt(sock, SOL_SOCKET, SO_REUSEADDR, (void*)&option, sizeof(option));
```

---

## 파일 정보 요약

|함수|용도|예시|
|---|---|---|
|`getsockopt`|현재 옵션 값 확인|송신 버퍼 크기 조회|
|`setsockopt`|옵션 값 설정|포트 번호 재사용 허용|


---

### 버퍼 크기 다루기

- 기본 송수신 버퍼 크기 확인 및 변경 가능.
    
- 설정한 크기와 실제 반영된 크기가 다를 수 있음 (커널이 조정함).
    

---

### TCP 연결 종료와 Time-Wait

- TCP 연결 종료 시 **Time-Wait** 상태가 발생.
    
- Time-Wait 동안 같은 포트 재사용 불가 → 서버 재시작 시 문제가 됨.
    
- 해결 방법: `SO_REUSEADDR` 옵션 설정 → 포트 번호 재사용 가능

```c
//바인드 에러 없애기
int sock = socket(PF_INET, SOCK_STREAM, 0);
int option = 1;
setsockopt(sock, SOL_SOCKET, SO_REUSEADDR, (void*)&option, sizeof(option));
```

---

### Nagle 알고리즘

- 작은 데이터 묶어 보내 트래픽 줄이는 TCP 기본 동작.
    
- 지연을 줄이고 싶다면 `TCP_NODELAY` 옵션을 설정해 Nagle 알고리즘을 끌 수 있음.