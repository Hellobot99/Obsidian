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

  ![[frame.png]]

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
  
![[Pasted image 20250507230035.png]]
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