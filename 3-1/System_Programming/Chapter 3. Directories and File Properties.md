## 디렉터리와 파일 속성

### 1. 파일 내용 외의 파일 구성 요소

- 파일은 **내용(Content)** 뿐만 아니라 다음을 포함:
    
    - **속성(Properties)**: 크기, 소유자, 권한 등
        
    - **디렉터리 트리 내 위치**
        

### 2. `ls` 명령어 이해하기

#### 기본 `ls`

- 현재 디렉터리 내 파일 및 디렉터리 이름 목록 출력
    

```bash
$ ls
```

#### `ls -l` 옵션

- 파일 타입, 권한, 링크 수, 소유자, 그룹, 파일 크기, 수정 시간, 이름을 자세히 출력
    

```bash
$ ls -l
```

#### 디렉터리와 파일 구분

- 인자로 디렉터리가 주어지면 내부 목록 출력
    
- 인자로 파일이 주어지면 파일 정보 출력
    

### 3. 파일 시스템 트리 구조

- 루트(`/`)에서 시작하는 트리 구조
    
- 모든 파일과 디렉터리는 이 트리에 포함
    

### 4. 디렉터리란 무엇인가?

- 파일 이름과 디렉터리 목록을 저장하는 특별한 파일
    
- 항상 `.` (현재 디렉터리)와 `..` (상위 디렉터리)를 포함
    

### 5. 디렉터리 읽기

- `dirent.h`의 `dirent` 구조체 사용
    
    - `d_name`: 파일 이름
        

### 6. `ls` 구현 방법 (ls1.c)

- 디렉터리 열기
    
- 항목 읽기
    
- 파일 이름 출력
    

### 7. `ls -l` 구현 방법

- 파일 정보를 얻기 위해 `stat()` 시스템 호출 사용
    

```c
struct stat info;
stat("filename", &info);
```

- 출력 항목:
    
    - 파일 타입 및 권한
        
    - 링크 수
        
    - 소유자 이름
        
    - 그룹 이름
        
    - 파일 크기
        
    - 수정 시간
        
    - 파일 이름
        

### 8. 파일 모드 변환 (권한 및 타입 표시)

- `st_mode`를 마스킹하여 파일 타입 및 권한 디코딩
    
- 비트마스크 사용 예시:
    

```c
if (S_ISDIR(info.st_mode)) printf("d");
if (info.st_mode & S_IRUSR) printf("r");
```

### 9. 사용자/그룹 ID를 문자열로 변환

- `/etc/passwd`, `/etc/group` 파일 참고
    
- `getpwuid()`, `getgrgid()` 함수 사용
    

### 10. ls2.c: 모든 것 합치기

- `ls -l` 스타일 출력 구현
    

### 11. 세 가지 특별한 비트 (Special Bits)

#### 1) Set-User-ID (SUID)

- 실행 시 소유자의 권한으로 프로그램 실행
    
- 예시: `passwd` 명령어는 root 권한으로 실행되어 `/etc/passwd` 수정
    

#### 2) Set-Group-ID (SGID)

- 실행 시 소유 그룹 권한 부여
    
- 그룹 접근 권한을 위해 사용
    

#### 3) Sticky Bit

- `/tmp`와 같은 공개 쓰기 가능 디렉터리에서 다른 사람 파일 삭제 방지
    
- 파일 소유자나 root만 삭제 가능
    

### 12. 파일 속성 설정 및 수정

#### 1) 파일 타입

- 생성 시 설정되며, 이후 변경 불가
    
- 예시:
    

```c
creat("file", mode);   // 일반 파일 생성
mkdir("dir", mode);     // 디렉터리 생성
```

#### 2) 권한 변경

- `chmod()` 시스템 호출 사용
    

```c
chmod("filename", S_IRUSR | S_IWUSR);
```

#### 3) 링크 수

- 파일이 파일 시스템 내에 참조되는 횟수
    

#### 4) 소유자/그룹 변경

- `chown()` 시스템 호출 사용
    

```c
chown("filename", new_uid, new_gid);
```

#### 5) 파일 크기

- 저장된 데이터의 바이트 수
    

#### 6) 접근 및 수정 시간

- `utime()` 시스템 호출로 갱신 가능
    

#### 7) 파일 이름 변경

- `rename()` 시스템 호출 사용
    

```c
rename("oldname", "newname");
```