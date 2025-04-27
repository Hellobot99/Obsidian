## 소개

- 비디오 게임은 여러 개념의 조합

- 주요 구성 요소:

  - 공간(Space): 화면상의 특정 위치에 이미지를 그리기

  - 시간(Time): 객체를 시간에 따라 이동시키기

  - 인터럽트(Interruptions): 사용자 입력을 즉시 처리

  - 멀티태스킹(Multitasking): 여러 작업을 동시에 처리

  

---

  

# 1. Space Programming: curses 라이브러리

  

- curses 라이브러리를 사용해 터미널 화면 제어

- 화면의 특정 좌표에 문자나 문자열 출력 가능

  

## 주요 함수

| 함수 | 설명 |

| initscr() | curses 초기화 |

| endwin() | curses 종료 |

| move(y, x) | 커서를 (y, x)로 이동 |

| addch(c) | 현재 위치에 문자 출력 |

| addstr(s) | 현재 위치에 문자열 출력 |

| refresh() | 가상 화면을 실제 화면에 반영 |

| clear() | 화면 전체 지우기 |

### 예제: hello1.c

```c

#include <curses.h>

  

int main() {

    initscr();

    move(10, 20);

    addstr("Hello, World!");

    refresh();

    getch();

    endwin();

    return 0;

}

```

  

> 컴파일: `gcc -o hello1 hello1.c -lcurses`

  

---

  

# 2. Signal Handling: 사용자 입력 처리

  

- 사용자의 키 입력 등 인터럽트 발생 시 처리

  

## 시그널 종류 예시

| 신호 | 번호 | 기본 동작 |

| SIGINT | 2 | 종료 (Ctrl+C) |

| SIGQUIT | 3 | Core dump 생성 (Ctrl+\) |

  

## 시그널 처리 방법

- 기본 동작 수락: `signal(SIGINT, SIG_DFL)`

- 무시: `signal(SIGINT, SIG_IGN)`

- 사용자 정의 함수 호출: `signal(SIGINT, handler)`

  

### 예제: sigdemo1

```c

#include <stdio.h>

#include <signal.h>

#include <unistd.h>

  

void handler(int signum) {

    printf("Caught signal %d!\n", signum);

}

  

int main() {

    signal(SIGINT, handler);

    while(1) {

        printf("Working...\n");

        sleep(1);

    }

    return 0;

}

```

  

---

  

# 3. Time Handling: 애니메이션 만들기

  

- 프로그램 타이밍 제어를 위해 `sleep()`, `usleep()` 사용

- 알람 발생은 `alarm()` 사용

  

## 주요 함수

| 함수 | 설명 |

| sleep(n) | n초 동안 프로세스 일시 중단 |

| usleep(μs) | 마이크로초 단위 일시 중단 |

| alarm(n) | n초 후 SIGALRM 발생 |

  
### 예제: hello3.c

```c

#include <curses.h>

#include <unistd.h>

  

int main() {

    initscr();

    for (int i = 0; i < 10; i++) {

        move(i, i);

        addstr("Hello");

        refresh();

        sleep(1);

    }

    endwin();

    return 0;

}

```

  

> 화면에 "Hello"가 점점 내려오면서 출력된다.

  

---

  

# 4. 비디오 게임 예제: bounce1d

  

- 단어를 화면에 부드럽게 이동시키고, 키 입력으로 방향과 속도 제어

  

| 키 | 동작 |

| Space Bar | 방향 전환 |

| 's' 키 | 느리게 |

| 'f' 키 | 빠르게 |

| 'Q' 키 | 게임 종료 |