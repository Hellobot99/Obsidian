
## Generalized Modulation Waveform

## 파동의 세 가지 요소
- **Amplitude (진폭)**: 파형의 높이 → 소리의 크기 (클수록 높음)
- **Frequency (주파수)**: 1초 동안 반복되는 횟수 → 소리의 높낮이 (높을수록 폭이 좁아짐)
- **Phase (위상)**: 파형의 시작 위치

---

# 푸리에 변환 (Fourier Transform)

## 푸리에의 발견
- 아무리 복잡한 파동이라도 **주기적**이라면 **단순한 파동의 합**으로 표현 가능.
- 이 단순한 파동은 주로 **사인파(sin), 코사인파(cos)** 형태로 나타냄.

## 기본 삼각 함수 표현
- 기본 파동: `f(θ) = a sin(θ)`  
  - `a`: 진폭
  - `θ`: 각도

### 예시
- 빗변이 `a`일 때:
  - 수직변 = `a sin(θ)`
  - 수평변 = `a cos(θ)`

---

# 주기, 주파수, 각속도

| 항목     | 의미                              | 단위        |
|----------|-----------------------------------|-------------|
| 주기 (T) | 파동이 한 번 진동하는 데 걸리는 시간 | 초 (sec)     |
| 주파수 (f) | 1초 동안 진동하는 횟수             | 헤르츠 (Hz) |
| 각속도 (ω) | 1초 동안 회전한 각도                | 도/초 또는 rad/sec |

### 관계식
- `ω = θ / t = 2πf = 2π / T`
- 360도 단위: `ω = 360f = 360 / T`

---

# 직관적 예시
- 태양이 동쪽에서 서쪽으로 12시간에 이동 → 각속도 = 15°/h
- 원형 트랙 1바퀴(360°)를 40초에 돈다면 → 각속도 = 9°/sec

---

# 복잡한 파형의 표현

- 복잡한 파동에서 가장 낮은 주파수 (가장 큰 주기)
- 예: 기본 주기 0.5초 → 기본 주파수 2Hz
- 기본 주파수의 정수배인 파동들이 결합되어 복잡한 파동을 구성

---

# 사인파와 코사인파

- **사인파**
- 주기적인 파동은 기본 주파수가 정수배인 주파수의 파동들로 구성됨

![[wave1.png]]
![[cos.png]]

#### Power Spectrum

![[graph.png]]
- a(k)가 얼마나 함유되어있나를 나타냄.

#### Baseband Bandwidth

- 필터로 인해 가질 수 있는 최대 주파수

![[Pasted image 20250529074747.png]]
필터 전 / 필터 후

#### Passband Bandwidth

- PassBand : 중심이 0 이 아닌 이동된 주파수
- 상한 주파수와 하한 주파수의 사이의 차이


## Digital Modulation

![[Pasted image 20250529075500.png]]

#### ASK (Amplitude Shift Keying)

- 보내면 1, 안보내면 0
- 간섭, 먼 거리에 취약함
 
![[Pasted image 20250529080745.png]]

#### FSK (Frequency Shift Keying)

- 진동수 크면 1, 작으면 0
- bandwidth 가 커야함
![[Pasted image 20250529133539.png]]

#### MFSK (Multilevel FSK)

- 레벨이 여러개인 FSK
- 2f(d) = 1/T(s) 만큼은 띄어야함.

![[Pasted image 20250529133744.png]]

f(c) : 중심 주파수, f(d) : 주파수 간격
- ex) f(c) = 250kHz, f(d) = 25kHz, M =8(L = 3bits) 일 때 8개의 진동수를 구할 수 있음. 
- 75kHz, 125kHz, 175kHz, 225kHz, 275kHz, 325kHz, 375kHz, 425kHz
- 인접 주파수 간섭을 피하기 위해 T = 1 / (2 * f(d)) = 1 / 50 , Ts = L * T = 1 / 150, Date Rate = 1 / Ts = 150kbps

#### BPSK (Binary Phase Shift Keying)

두 가지 페이즈를 사용 (180도 차이)
#### DPSK (Diffetential PSK)

0이면 그대로, 1이면  반대로

#### QPSK (Quadrature PSK)

여러개의 페이즈 사용(사진은 90도 차이)

![[Pasted image 20250605135050.png]]

#### OQPSK (Offset QPSK)

