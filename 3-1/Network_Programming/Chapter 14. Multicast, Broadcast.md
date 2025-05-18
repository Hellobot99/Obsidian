
## 멀티캐스트

- 멀티캐스트 그룹에 참여하는 구성원들에게 데이터를 전송
- 한번의 데이터를 전송해서 여러 호스트에 데이터 전달
- 224.0.0.0 ~ 239.255.255.255 범위를 갖는 Class D IP 주소를 사용

```c
int send_sock; 
int time_live = 64;

send_sock = socket(PF_INET, SOCK_DGRAM, 0); 
setsockopt(send_sock, IPPROTO_IP, IP_MULTICAST_TTL, 
		(void*)&time_live, sizeof(time_live));
```

- TTL : 패킷이 전달될 수 있는 최대 라우터 수 설정
- UDP receiver 만 bind 필요`

```c
struct ip_mreq { 
struct in_addr imr_multiaddr; // 그룹 주소 
struct in_addr imr_interface; // 
}

int recv_sock; 
struct ip_mreq join_adr;

recv_sock = socket(PF_INET, SOCK_DGRAM, 0);
join_adr.imr_multiaddr.s_addr = “가입할 멀티캐스트 그룹 주소” join_adr.imr_interface.s_addr = “멀티캐스트 그룹에 가입할 자신의 IP 주소”
```

- 그룹 가입

## 브로드캐스트

- 동일한 네트워크 내에 존재하는 호스트에게 데이터를 전송
- sender는 맨 뒤 IP 세자리는 255로 고정, receiver 는 255.255.255.255 고정

```c
//Sender
int send_sock; 
int so_broad = 1;

setsockopt(send_sock, SOL_SOCKET, SO_BROADCAST, (void*)&so_broad, sizeof(so_broad));

//Receiver는 따로 설정 필요없음.
```