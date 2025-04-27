## 소개

- 파일과 디바이스의 유사점과 차이점을 이해한다.

- 디바이스 연결의 속성(attributes)을 관리하는 방법을 배운다.

- 디바이스 드라이버를 제어하는 방법을 학습한다.

- 배워야 할 것:

  - 시스템 콜: `fcntl`, `ioctl`

  - 함수: `tcsetattr`, `tcgetattr`

  - 명령어: `stty`, `write`  

---
  

# 1. 디바이스는 파일과 같다

  

- 디바이스 (마이크, 키보드, USB 등)는 **파일처럼 읽고 쓰기 가능**.

- 리눅스에서는 모든 디바이스를 파일로 취급.

- `/dev` 디렉토리에 파일 이름으로 존재.

  

### 파일 I/O 명령어

- `read()`, `write()`, `open()`, `close()`, `lseek()`
  

---

  

# 2. 터미널(tty)과 파일 시스템

  

- `tty`: 현재 연결된 터미널 파일 경로를 출력.

- `/dev/pts/X`: 가상 터미널의 파일명.

  

```bash

$ tty

/dev/pts/2

  

$ cp /etc/passwd /dev/pts/2

$ ls > /dev/pts/2

```

  

> 터미널도 파일처럼 읽고 쓸 수 있다!

  

---

  

# 3. 파일 I/O 시스템 콜
  

- `open(path, flags, mode)`

- `read(fd, buffer, count)`

- `write(fd, buffer, count)`

- `close(fd)`

  

---

  

# 4. 디스크 연결 속성

  

- **open()** 호출 시 파일과 프로세스 간 연결 생성.

- 속성:

  - **버퍼링(Buffering)**

  - **자동 추가(Append) 모드**

  

## 시스템 콜: `fcntl`

- 파일 디스크립터를 제어하기 위한 시스템 콜.

  

## 예제: 버퍼링 설정 (O_SYNC)

```c

fcntl(fd, F_SETFL, O_SYNC);

```

  

> `O_SYNC` 설정 시 커널 버퍼를 거치지 않고 바로 디스크에 기록.

  

## 예제: 자동 추가 모드

```c

fcntl(fd, F_SETFL, O_APPEND);

```

  

> `O_APPEND` 설정 시 파일 끝에 자동으로 쓰기.

  

---

  

# 5. 터미널 연결 속성

  

- 터미널 연결은 추가 속성(Control Settings)을 가짐.

  

## 명령어: `stty`

```bash

$ stty -a

```

- 터미널 드라이버 설정을 읽거나 수정.

- 예:

  - `stty erase X` : X 키를 지우기 키로 설정

  - `stty -echo` : 입력 중 키보드 입력 숨김

  

---

  

# 6. 터미널 설정 변경하기

  

## 시스템 콜

- `tcgetattr(fd, struct termios *info)`: 현재 설정 읽기

- `tcsetattr(fd, TCSANOW, struct termios *info)`: 설정 변경

  

### struct termios 구조체

- 입력 플래그

- 출력 플래그

- 제어 플래그

- 로컬 플래그

- 제어 문자 배열 (c_cc)

  

---

  

# 7. 예제: echo 상태 읽고 변경하기

  

### echo 상태 확인 프로그램 (echostate.c)

```c

#include <stdio.h>

#include <termios.h>

#include <unistd.h>

  

int main() {

    struct termios info;

    tcgetattr(STDIN_FILENO, &info);

    if (info.c_lflag & ECHO)

        printf("echo is ON\n");

    else

        printf("echo is OFF\n");

    return 0;

}

```

  

### echo 상태 변경 프로그램 (setecho.c)

```c

#include <stdio.h>

#include <termios.h>

#include <unistd.h>

  

int main(int argc, char *argv[]) {

    struct termios info;

    tcgetattr(STDIN_FILENO, &info);

    if (argv[1][0] == 'y')

        info.c_lflag |= ECHO;

    else

        info.c_lflag &= ~ECHO;

    tcsetattr(STDIN_FILENO, TCSANOW, &info);

    return 0;

}

```

  

---

  

# 8. 다른 디바이스 제어: ioctl()

  

- `ioctl(fd, request, ...)` 사용.

- 디바이스 드라이버에 다양한 명령을 전달할 수 있음.