## 운영체제

- **운영체제 (OS)**: 하드웨어와 소프트웨어 자원을 관리하고, 하드웨어를 가상화하며, 시스템 호출을 통해 서비스를 제공함.
    
- **커널**: 운영체제의 메모리 상주 핵심 부분.
    

### 예제

```plaintext
[응용 프로그램] → [시스템 호출] → [커널] → [하드웨어 접근]
```

## 쉘(Shell)

- 유닉스 인터페이스로 다음을 반복 수행:
    
    1. 프롬프트 표시
        
    2. 명령어 읽기
        
    3. 명령어 처리
        
    4. 명령어 실행
        

### 예제

```bash
$ ls
$ whoami
```

## 필수 명령어

- 유닉스 시스템을 탐색하고 관리하는 기본 명령어.
    

### 예제

```bash
$ pwd   # 현재 디렉토리 출력
$ cd /home  # 디렉토리 이동
$ mkdir new_folder  # 새 폴더 생성
```

## 프로젝트 1: `who` 명령어

### 목적

- 현재 로그인한 사용자 목록 표시
    

### 출력 예시

```plaintext
hank   lft/0   Jun   8   08:34  (ausnix5)
mary   lft/0   Jun   8   08:22  (machine.austin.ibm)
john   pts1    Jun   8   08:05  (computer.knu.ac.kr)
jan    pts4    Jun   8   09:19  (puff.wisc.edu)
```

### `who` 동작 원리

- **메뉴얼 페이지(man)**: `man who`, `man utmp`
    
- **주요 파일**: `/var/run/utmp`, `/var/log/wtmp`, `/var/log/btmp`
    
- **주요 구조체**: `utmp` 구조체를 파일에서 읽음
    

### 파일 처리 시스템 호출

- **open()**: 파일 열기
    
- **read()**: 파일 읽기
    
- **close()**: 파일 닫기
    

### 예제

```c
int fd = open("/var/run/utmp", O_RDONLY);
struct utmp record;
read(fd, &record, sizeof(record));
close(fd);
```

### 시스템 호출

- 사용자 프로그램이 운영체제에 서비스 요청하는 방법
    
- 예시: `open`, `read`, `write`, `close`, `stat`, `lstat`
    

### 파이프(Pipes)

- 한 프로세스의 출력을 다른 프로세스의 입력으로 연결
    
- 문법: `command1 | command2`
    

### 예제

```bash
$ who | grep hank
```

### `who` 구현

- **who1.c**: 기본 utmp 레코드 읽기
    
- **who2.c**: 출력 개선 및 빈 레코드 제거
    
- **who3.c**: 버퍼링 추가하여 시스템 호출 최적화
    

## 프로젝트 2: `cp` 명령어

### 목적

- 파일을 복사하는 명령어
    

### `cp` 동작 원리

- **creat()**: 파일 생성/덮어쓰기
    
- **write()**: 파일에 데이터 쓰기
    
- **cmp**: 두 파일 비교
    

### 예제

```bash
$ cp source.txt destination.txt
$ cmp source.txt destination.txt
```

### 버퍼링(Buffering)

- 시스템 호출 횟수를 줄이기 위해 데이터를 블록 단위로 읽고 쓰는 방법
    
- 예시: BUFFERSIZE = 4096 바이트
    

## 시스템 호출 비용

- 사용자 공간과 커널 공간 간 전환은 비용이 많이 듦
    
- 시스템 호출 최소화로 성능 향상 가능
    

## 로그아웃 처리

- utmp 파일 업데이트 과정:
    
    1. `O_RDWR` 모드로 utmp 파일 열기
        
    2. 해당 터미널 레코드를 찾아 읽기
        
    3. `lseek()`로 위치 조정 후 수정된 레코드 쓰기
        
    4. 파일 닫기
        

### lseek()

- 파일의 현재 읽기/쓰기 위치를 이동시키는 시스템 호출
    
- 기준:
    
    - 0: 파일 시작 지점
        
    - 1: 현재 위치
        
    - 2: 파일 끝
        

### 예제

```c
lseek(fd, -sizeof(struct utmp), SEEK_CUR);
write(fd, &new_record, sizeof(new_record));
```
