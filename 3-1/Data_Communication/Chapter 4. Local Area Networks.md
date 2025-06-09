
## MAC address

- 6 바이트
- 물리적 주소이기 때문에 한 기기에 하나만 붙는다

---
# 이더넷

### Standard Ethernet (10 Mbps)

- Udp 식 확인 안하고 그냥 보냄
- Ack 확인 안함
- 패킷의 최소치를 맞춰야 되서 패딩 값 넣음

- ex) 4A:30:10:21:10:1A 일 때
	도착 했을 때 첫번째 뒤의 숫자 A는 1010 인데 마지막이 0이므로 unicast
- ex) 47:20:1B:2E:08:EE  일 때
	도착 했을 때 첫번째 뒤의 숫자 7은 0111 인데 마지막이 1이므로 multicast
- ex) FF:FF:FF:FF:FF:FF 는 broadcast

### Fast Ethernet (100 Mbps)

- Standard 보다 빠름, 불필요한 패딩이 많아짐

### Giga Ethernet (1000 Mbps)

- Fast 보다 빠름, 불필요한 패딩이 많아짐

### 10-Gigabit Ethernet (10 Gbps)

- Fast 보다 빠름, 불필요한 패딩이 많아짐

---

## 서비스

#### BSS (Basic Service Set)

- 기본 셋

![[Pasted image 20250608232313.png]]

#### ESS (Extended Service Set)

- BSS 모아 둔 거

![[Pasted image 20250608232608.png]]

## Station Type

- WIFI 공간
- 스테이션의 이동성을 기준으로 세 가지 유형으로 구분
	No-transition - 고정
	BSS-transition - 같은 ESS 내에서 이동
	ESS-trasition - 힌 ESS에서 다른 ESS로 이동

### DCF (Distribution Coordination Function)

![[Pasted image 20250608233429.png]]

- Contention service
- 보내기 전에 RTS를 전송, RTS를 받으면 주변에 CTS 전송, RTS, CTS를 받으면 전송을 중단함
- Hidden-Station-Station Problem (범위 밖에 있는 Station 과 충돌 나는 문제)

### PCF (Point Coordination Function)

- 선택적 접근 방식
- DCF보다 우선권을 가짐
- AP (Access Point)가 네트워크를 제어하며, 충돌을 막아줌

### Fragmentation

- 프레임을 작게 여러번 보내는게 효율적이다.

---

## Frame Format

![[Pasted image 20250608234848.png]]

#### FC 필드

![[Pasted image 20250608234955.png]]
- 4가지의 경우가 생기는데, 각 케이스마다 4개의 주소 필드에 들어가는 게 달라짐

### Exposed Station Problem 

![[Pasted image 20250608235410.png]]

- A 때문에 C->D가 지연됨으로써 손실 발생

#### ISM band

산업적, 과학적, 의료적 목적으로 열어둔 공짜 대역폭

### Baseband Layer

- 슬롯 하나에 625 μs
### One-slot frame

- **259 μs**는 주파수 도약(hopping) 및 제어용으로 사용됨.
    
- 전송에 사용 가능한 시간 = `625 − 259 = 366 μs`
    
- **1 MHz 대역폭 + 1 bit/Hz**라면 → **366 bits 전송 가능**
    
- **N = 240 bits** (Payload)
### Three-slot frame

- 총 길이: `3 × 625 − 259 = 1616 μs`
    
- 전송 길이 = 1616 bits
    
- 전송 중에 **3 슬롯 동안 같은 주파수(hop)** 유지
    
- **N = 1490 bits** (Payload)
### Five-slot frame

- 총 길이: `5 × 625 − 259 = 2866 μs`
    
- 전송 길이 = 2866 bits
    
- **5 슬롯 동안 같은 hop** 유지
    
- **N = 2740 bits** (Payload)