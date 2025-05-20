## 1-1. Nodes and Links

데이터 링크에서 데이터 통신은 node-to-node 이다.  
node = 단말(터미널), 라우터 (LAN, WAN은 노드로 취급하지 않는다)

## 1-2. Two Types of Links and Two Sublayers

1. **Broadcast Link**: DLC + MAC 로 구성되며 여러 노드가 공유
    
2. **Point-to-Point Link**: DLC 로 구성되며 1대1 연결
    

## 1-3. Two Sublayers

- **DLC (Data Link Control)**
    
    - 두 인접 노드 사이의 통신 절차를 다룸
        
    - Framing, Error Control 기능 수행
        
    - Data transmission type 두 가지: 동기식 또는 비동기식
        
- **MAC (Media Access Control)**
    
    - 공유 매체에 접근하는 규칙을 다룸
        

## 2-1. Framing

메시지를 분리하여 주소 정보를 포함한 비트들을 재포장하는 것.  
메시지를 작게 분리하면 에러에 더 쉽게 대처할 수 있다.

### Frame Size

- 고정형 또는 가변형 (표준에 따라 다름)
    
- 고정형은 프레임 경계를 별도로 정의할 필요 없음
    
- 가변형은 경계를 정의해야 함
    

### Character-Oriented Framing

![[Character-Oriented Framing.png]]

- **Flag (8 bits)**: 시작과 끝을 알리는 비트
    
- **Header (8 bits)**: Source, Destination addresses, Control information 등
    
- **Data (8 bits 단위)**: 8비트씩 전송
    
- **Trailer (8 bits)**: 에러 감지용
    

**Byte Stuffing**: 데이터에 Flag나 ESC 비트가 있을 경우, 해당 비트 앞에 ESC 비트를 추가한다.

### Bit-Oriented Framing

![[Bit-Oriented Framing.png]]

- **Flag (8 bits)**: 시작과 끝을 알리는 비트 (01111110)
    
- **Header**: Source, Destination addresses, Control information 등
    
- **Data**: 많은 수의 비트 전달
    
- **Trailer**: 에러 감지용

**Bit Stuffing**: 1이 연속해서 5번 등장하면 0을 삽입한다.

## 2-2. Error Control

- **Error Detection**: 에러 탐지 (쉬움)
    
- **Error Correction**: 에러 수정 (어려움)
    

### 에러의 종류 및 용어

- **Single-bit error**: 하나의 비트만 에러
    
- **Burst error**: 연속적인 여러 비트 에러
    
- **Redundancy**: 에러 검출을 위해 추가하는 데이터
    
- **Coding**: Redundancy 비트를 생성하는 과정
    
- **Hamming Distance**: 원본과 수신된 데이터 간 다른 비트의 개수 (XOR 연산)
    

### Minimum Hamming Distance for Error Detection

- 에러 s개를 검출하려면 최소 해밍 거리 dmin = s + 1 이어야 한다.
    

### Block Coding

![[Block Coding.png]]

- 원래 k비트 데이터에 r비트의 Redundancy를 추가하여 n = k + r 비트의 코드워드를 만든다.
    
- 예: 사진에서는 두 비트가 오류가 나면 에러 검출이 불가능할 수도 있다.
    

### Linear Block Codes

- Block Coding의 한 종류
    
- **Linear Block Code**는 두 코드워드의 XOR 결과도 유효한 코드워드가 되는 코드
    
- MHD(Minimum Hamming Distance)는 "0이 아닌 코드워드 중 1의 개수가 가장 적은 것"의 1의 개수로 결정된다.
    
    - 예: 101, 011, 110의 경우, 1의 개수가 2개로 가장 적으므로 MHD = 2

## Parity-Check Code

![[parity.png]]

- 원래 데이터에 패리티 비트(parity bit)를 하나 추가해 전송.

- **홀수 패리티** 방식: 모든 비트를 합쳐 **홀수**면 패리티 비트는 `1`, **짝수**면 `0`.

- 수신 시, 받은 비트들을 모두 합쳐 **홀수**면 `신드롬 비트 = 1`, **짝수**면 `0`.

- 신드롬 비트가 `0`이면 오류 없이 정상 수신된 것.

---

## Cyclic Codes (순환 코드)


- **코드워드를 오른쪽으로 한 비트 순환**해도 여전히 유효한 코드워드가 되는 방식.

- **CRC 코드**라고도 부르며, LAN/WAN에서 널리 사용됨.

- 송신자와 수신자는 **같은 Divisor**를 공유함.

- **단일 에러 및 홀수 개 에러 탐지 가능**.

- 구현이 쉽고 빠르며 하드웨어에서 널리 사용됨.

### CRC 처리 과정

  ![[crc1.png]]

- `dataword = k`, `codeword = n`일 때:

  - **Encoder**: `n - k + 1` 비트의 divisor를 사용해 `n - k`개의 비트를 생성해 `dataword` 뒤에 붙임.

  - **Decoder**: 동일한 divisor를 사용해 `n - k`개의 **신드롬 비트** 생성.

  - 모든 신드롬 비트가 0이면 정상 수신.
### 연산 방식
  
![[crc2.png]]
![[crc3.png]]

- **나눗셈**은 AND, XOR 연산으로 처리:

 - 곱셈: **AND 연산**

 - 뺄셈: **XOR 연산**

- 가장 왼쪽 비트가 0이면, 0만으로 구성된 비트를 빼준다.

- **Divisor**는 보통 `CRC-x`이면 **x+1 비트**이고, **가장 왼쪽과 오른쪽 비트는 1**이어야 함.

---
## ACK / Timeout / Sequence Number

- **ACK**: 전송이 제대로 되었는지 확인.

- **Timeout**: ACK가 사라질 경우 재전송하기 위해 사용.

- **Frame Sequence Number**:

  - Timeout으로 인한 재전송 시 **프레임 중복 방지**.

- **ACK Sequence Number**:

  - 이전 프레임의 ACK가 사라졌을 때, **다음 프레임의 ACK를 이전 것으로 오인하는 것 방지**.

---
## Piggybacking

- **데이터와 ACK를 함께 전송**하여 효율적이며 네트워크 혼잡 감소.

---
## Sliding Window Protocols

- **Stop-And-Wait ARQ**:

  - 하나 전송 → ACK 대기.

- **Go-Back-N ARQ**:

  - 여러 프레임을 연속 전송, 오류 발생 시 **해당 프레임부터 재전송**.

  - 손실된 프레임 뒤의 프레임들은 폐기됨.

- **Selective Repeat ARQ**:

  - 손실된 프레임은 나중에 재전송 받고, **다음 프레임은 버퍼에 저장 후 재조립**.

  - 구현이 복잡함.

- **Adaptive ARQ**:

  - **채널 상태**에 따라 프레임 크기를 동적으로 조절.

---
## Two DLC Protocols - HDLC

### 전송 모드

- **NRM (Normal Response Mode)**:

  - Primary: 명령 전송 / Secondary: 응답만 전송.

- **ABM (Asynchronous Balanced Mode)** :

  - Primary/Secondary 구분 없이 양방향 전송 가능.

### 프레임 종류

  ![[frame1.png]]

- **I-frame (Information frame)**:

  - 사용자 데이터 + 제어 정보 전송.

- **S-frame (Supervisory frame)**:

  - 제어 정보만 전송 (흐름 제어, 오류 제어).

- **U-frame (Unnumbered frame)**:

  - 시스템 관리용 프레임.

### 프레임 구조

- **Flag**: 시작/끝 표시 (비트 패턴 `01111110`)

- **Address**: Secondary Station 주소

- **Control**: 흐름 및 오류 제어 (1 or 2 byte)

- **Information**: 사용자 데이터

- **FCS**: 오류 검출용 CRC (2 or 4 byte)

---
### Control Field 세부 구성
  
![[frame2.png]]
#### I-frame  

- 시작 비트: `0`

- 다음 3비트: **Sequence Number (N(S))**

- 그 다음 1비트: **Poll/Final (P/F)**

- 다음 3비트: **Acknowledgement Number (N(R))**  

#### S-frame  

- 시작 비트: `10`

- 다음 2비트: 종류

  - `00`: Receive Ready (RR)

  - `01`: Receive Not Ready (RNR)

  - `10`: Reject (REJ) → Go-Back-N 방식

  - `11`: Selective Reject (SREJ) → Selective Repeat 방식

- 다음 1비트: **P/F 비트**

- 다음 3비트: **Acknowledgement Number**
#### U-frame

- 시작 비트: `11`

- 다음 5비트로 **32가지 명령** 표현 가능

---

## Two DLC Protocols - Point to Point Protocol

### PPP 프레임 구조 (7 fields)

|필드명|설명|
|---|---|
|**Flag**|프레임의 시작과 끝을 나타냄. 값: `01111110`|
|**Address**|항상 `11111111`로 고정 (브로드캐스트 주소)|
|**Control**|항상 `00000011`로 고정 (HDLC의 U-프레임 모방)|
|**Protocol**|상위 계층의 어떤 프로토콜인지 식별 (ex. LCP, IP 등)|
|**Payload**|데이터가 들어가는 영역|
|**FCS**|Frame Check Sequence (오류 검출용 CRC)|

---

### Byte Stuffing

- PPP는 바이트 지향 프로토콜이므로 **Byte Stuffing**을 사용
    
- 데이터 영역에 플래그(`01111110`)와 같은 패턴이 나오면 **ESC(`01111101`)** 바이트를 앞에 붙여 전송
    
- ESC 자체도 또 다른 ESC로 이스케이프 처리함
    

---

### PPP Multiplexing

PPP는 다음과 같은 **다른 프로토콜들과 함께 사용**됩니다:

#### 1. LCP (Link Control Protocol)

- 링크 생성, 유지, 설정, 종료
    
- 링크의 옵션 협상
    

#### 2. AP (Authentication Protocol)

- 사용자 인증
    
- **PAP (Password Authentication Protocol)**
    
    - 사용자 이름 + 비밀번호 전송 → 유효성 확인
        
- **CHAP (Challenge Handshake Authentication Protocol)**
    
    - 3-way handshake 방식으로 보안 강화
        
    - 순서:
        
        1. 서버 → 클라이언트로 challenge 값 전송
            
        2. 클라이언트는 challenge 값 + 비밀번호로 해시 → 서버로 전송
            
        3. 서버도 동일 계산 → 결과 비교
            

#### 3. NCP (Network Control Protocol)

- 여러 **네트워크 계층 프로토콜(IP, OSI 등)**을 지원하기 위한 설정용
    
- 예: IPCP (Internet Protocol Control Protocol) – IP 데이터 전송을 위한 설정
    

---

## MAC (Media Access Control)

### 1. Random Access Protocols (경쟁 기반)

- 충돌(Collision) 발생 가능성 O, 우선순위 X
    
- 모든 노드가 동일한 권한으로 데이터를 전송 가능
    
- 대표 프로토콜: **ALOHA**
    

---

### ALOHA

#### ▸ Pure ALOHA

- 아무 때나 프레임 전송 가능
    
- 충돌 가능성 높음
    
- 충돌이 발생하면 **Backoff 알고리즘**을 통해 재전송
    

**Backoff 계산:**

```
T(b) = R(0 ~ 2^k - 1) × (T(p) 또는 T(fr))
k = 시도 횟수
```

**용어 정리:**

- `T(p)` : 최대 전파 지연 시간 (예: 거리 / 빛의 속도)
    
- `T(fr)` : 프레임 하나 전송에 걸리는 시간
    
- `Vulnerable time` : 충돌이 일어날 수 있는 시간 = `2 × T(fr)`
    
- `Throughput` : 단위 시간당 성공적으로 전송된 프레임 비율
    
    - **S = G × e^(-2G)**
        
    - G = 평균적으로 `T(fr)` 동안 생성되는 프레임 수
        

**예시 문제 정리:**

1. **전파 지연 시간 계산**
    
    - 거리: 600km
        
    - T(p) = 600,000 / 300,000,000 = **2ms**
        
    - k = 2, R = {0,1,2,3}
        
    - → T(b) = {0, 2, 4, 6} ms
        
2. **충돌 없는 전송 조건**
    
    - 채널 속도: 200 kbps, 프레임 크기: 200 bit
        
    - T(fr) = 200 / 200,000 = **1ms**
        
    - Vulnerable time = 2 × 1ms = **2ms**
        
3. **Throughput 계산**
    
    - T(fr) = 1ms
        
    - G 값에 따라 S 계산:
        
        - G = 1 → S ≈ 0.135
            
        - G = 0.5 → S ≈ 0.184 (최대 처리량)
            
        - G = 0.25 → S ≈ 0.152
            

---

#### ▸ Slotted ALOHA

- **슬롯 시간 단위**로 나눠서 전송 → 충돌 가능성 줄어듦
    
- Vulnerable time = `T(fr)`
    
- **Throughput 공식:**
    
    - S = G × e^(-G)
        
    - 최대 Throughput: 0.368 (G = 1일 때)
        

**예시 문제 정리:**

1. **시스템에서 초당 1000개의 프레임을 생성**
    
    - T(fr) = 200 / 200,000 = **1ms**
        
    - G = 1
        
    - S = G × e^(-G) = 1 × e^(-1) ≈ **0.368**
        
    - Throughput = 1000 × 0.368 = **368개 성공**
        
2. **시스템에서 초당 500개의 프레임을 생성**
    
    - G = 0.5
        
    - S = 0.5 × e^(-0.5) ≈ **0.303**
        
    - Throughput = 500 × 0.303 = **151개 성공**
        
3. **시스템에서 초당 250개의 프레임을 생성**
    
    - G = 0.25
        
    - S = 0.25 × e^(-0.25) ≈ **0.195**
        
    - Throughput = 250 × 0.195 = **49개 성공**

---
## CSMA (Carrier Sense Multiple Access)

CSMA는 충돌을 줄이기 위해 **전송 전에 매체 상태를 감지하는 방식

![[CSMA1.png]]

---
### Persistence 방식

![[CSMA2.png]]
### 1-persistent CSMA

- 채널을 계속 감지하다가, **idle(비어있음)** 상태가 감지되면 **즉시 전송**
- 단점: 여러 스테이션이 동시에 감지 후 전송할 경우 **충돌 가능성 매우 높음**

### Non-persistent CSMA

- 채널을 감지했는데 **busy** 상태이면 **랜덤 시간 기다린 뒤 다시 감지**
- 장점: 충돌 확률 감소  
- 단점: 대기 시간 증가 → 평균 지연 증가 가능성

### p-persistent CSMA (슬롯 기반)

- **시간을 슬롯 단위로 나누어** 사용
- 슬롯 시작 시 채널이 idle이면:
  - 확률 `p`로 전송
  - 확률 `1 - p`로 **1 슬롯 대기 후 다시 시도**
- 만약 누가 전송하고 있으면: 일정 시간 **backoff**

---
## CSMA/CD

- CSMA 를 사용하며 Collision Detection 기능이 있다.
- 보내다가 충돌 발생 시 멈추고 나중에 재 전송한다.
- 데이터를 모두 보낸 후에 충돌이 발생한 걸 알아도 버퍼 특성상 재전송이 불가능 하기 때문에 프레임을 전송하는 도중에 충돌이 났는지 알아야 한다.
- CSMA/CD에서 충돌을 감지하려면 프레임 전송이 끝나기 전까지 충돌 신호가 돌아와야 하므로, 프레임 전송 시간은 최소 2배의 전파 지연 시간 이상이어야 한다.

예시 문제

- 대역폭 : 10 Mbps
- Maximum paropagation time : 25.6

minimum size of frame 의 값은? 

25.6 * 2 = 51.2 (왔다갔다하는 시간)
51.2 * 10 = 512bits

#### Throuput

- CSMA/CD는 **ALOHA**나 **슬롯 ALOHA**보다 훨씬 더 높은 처리량을 보장함
- **1-persistent 방식**에서는 G = 1일 때 처리량이 최대이며 **약 50%** 수준
- **nonpersistent 방식**은 G가 3~8 사이일 때 처리량이 **최대 90%까지** 올라갈 수 있음

CSMA/CA 도 있다.

---
## Controlled Access

- Reservation : 데이터 스테이션은 데이터를 보내기 전에 미리 예약을 한다.
![[reservation.png]]

- Polling : Primary station 과 secondary stations 사이에서 Primary 가 secondary 한테 Poll을 보내면 secondary stations이 대답하는 구조
![[poll.png]]

- Token Passing : station 사이에서 토큰이 돌면서 제어권을 획득

## 4 LINK-LAYER ADDRESSING

- 패킷을 **어떤 경로로 보낼지는 다른 정보가 필요**, 경로는 알 수 없음
- IP 헤더에 있는 **Source IP**와 **Destination IP**는 누가 보냈고, 누가 받아야 하는지를 명확히 함
![[ipadress.png]]

Unicast Address :
	A2:34:45:11:92:F1 처럼 두번째 숫자가 짝수

Multicast address : 
	A3:34:45:11:92:F1 처럼 두번째 숫자가 홀수

Broadcast address :
	FF:FF:FF:FF:FF:FF 처럼 모든 숫자가 F

## CSMA/CD

- CSMA 를 사용하며 Collision Detection 기능이 있다.
    
- 보내다가 충돌 발생 시 멈추고 나중에 재전송한다.
    
- 데이터를 모두 보낸 후에 충돌이 발생한 걸 알아도 버퍼 특성상 재전송이 불가능하기 때문에, 프레임을 전송하는 **도중에** 충돌이 났는지 알아야 한다.
    
- CSMA/CD에서 충돌을 감지하려면 **프레임 전송이 끝나기 전까지** 충돌 신호가 돌아와야 하므로, 프레임 전송 시간은 **최소 2배의 전파 지연 시간 이상**이어야 한다.
    

### 예시 문제

- 대역폭 : 10 Mbps (즉, 10^7 bps)
    
- Maximum propagation time : 25.6 μs
    

최소프레임전송시간=2×25.6μs=51.2μs최소 프레임 전송 시간 = 2 \times 25.6 \mu s = 51.2 \mu s  
최소프레임크기=107×51.2μs=512 bits =64 bytes최소 프레임 크기 = 10^7 \times 51.2 \mu s = 512 \text{ bits } = 64 \text{ bytes}

따라서 Ethernet의 최소 프레임 크기는 **64 bytes**.

---

## Throughput

- CSMA/CD는 **ALOHA**나 **슬롯 ALOHA**보다 훨씬 더 높은 처리량을 보장함.
    
- **1-persistent 방식**:
    
    - G = 1일 때 최대 처리량, 약 **50%**
        
- **nonpersistent 방식**:
    
    - G = 3 ~ 8일 때 최대 처리량, 약 **90%**
        
- * G: offered load (단위 시간당 평균 프레임 수)
    

---

## CSMA/CA

- Collision Avoidance 방식으로, **무선 네트워크에서 주로 사용**됨.
    
- CSMA/CD는 충돌 후 처리이지만, CA는 **충돌 자체를 피하는 방식**
    

---

## Controlled Access

> 하나의 채널을 여러 스테이션이 공유할 때, 충돌 없이 순서를 정해 접근하는 방식

### Reservation

![[reservation.png]]

- 시간 슬롯이 존재하며, **미리 예약**한 스테이션만 데이터 전송 가능
    
- 예약 프레임 → 데이터 프레임 순서로 전송됨 (Figure 37 참고)
    

### Polling

![[poll.png]]

- **Primary Station**이 모든 통신 제어권 가짐
    
- **Secondary Station**은 Primary로부터 Poll을 받을 때만 응답 가능
    
- Master-Slave 구조 (Figure 38 참고)
    

### Token Passing

- 논리적 링 구조에서 **Token**이 돌며 제어권이 전달됨
    
- Token을 받은 스테이션만 전송 가능 (Figure 39 참고)
    

---

## 4. Link-Layer Addressing

> IP 주소만으로는 **중간 경로의 링크 주소**를 결정할 수 없음  
> 따라서 링크 계층 주소 (MAC address)가 필요

![[ipadress.png]]
### Address 종류

- **Unicast Address** : 한 호스트에만 전달
    
    - 예시: A2:34:45:11:92:F1 (첫 바이트의 두 번째 비트 = 0, 짝수)
        
- **Multicast Address** : 특정 그룹에 전달
    
    - 예시: A3:34:45:11:92:F1 (첫 바이트의 두 번째 비트 = 1, 홀수)
        
- **Broadcast Address** : 네트워크 내 모든 호스트에게 전달
    
    - 예시: FF:FF:FF:FF:FF:FF
        

> MAC 주소는 48비트 (6바이트), 12자리 16진수로 표현됨