
  
## 문자열 처리 함수

  
### 1. `sscanf()`

- 문자열로부터 포맷에 맞춰 데이터 추출

```c

int sscanf(const char *str, const char *format, ...);

```

**예제**

```c

char *token = "25 54.32 Thompson 56789";

int n1, n2;

char name1[20];

double d1;

  

sscanf(token, "%d %lf %s %d", &n1, &d1, name1, &n2);

printf("%d, %lf, %s, %d\n", n1, d1, name1, n2);

```

  

### 2. `sprintf()`

- 포맷에 맞춰 문자열을 생성

```c

int sprintf(char *str, const char *format, ...);

```

**예제**

```c

char buf[256];

char name[20] = "홍길동";

int num = 23;

double level = 2.37;

  

sprintf(buf, "Name: %s, No: %d, Level: %f", name, num, level);

printf("%s\n", buf);

```

  

---

  

## 파일 입력 함수

  

### 1. `fgets()`

- 파일로부터 한 줄씩 문자열 읽기

```c

char *fgets(char *string, int n, FILE *stream);

```

  

### 2. `fscanf()`

- 파일로부터 포맷에 맞춰 데이터 읽기

```c

int fscanf(FILE *stream, const char *format, ...);

```

  

**파일 읽기 예제**

```c

FILE *f = fopen("sample.txt", "r");

char buffer[100] = {0};

  

while (fgets(buffer, sizeof(buffer), f) != NULL) {

    printf("%s", buffer);

}

fclose(f);

```

  

---

  

## 파일 출력 함수

  

### 1. `fputs()`

- 파일에 문자열 저장

```c

int fputs(const char *s, FILE *stream);

```

  

### 2. `fprintf()`

- 포맷에 맞춰 파일에 출력

```c

int fprintf(FILE *stream, const char *format, ...);

```

  

**파일 쓰기 예제**

```c

FILE *stream = fopen("myfile", "w");

char buffer[] = "abcdefghijklmnopqrstuvwxyz";

  

fputs(buffer, stream);

fclose(stream);

```
 

---

  

## 라인 단위 파일 입출력

  

- 쓰기: `fputs(buf, fp)`

- 읽기: `fgets(buf, size, fp)`

  

---

  

## 문자 단위 파일 입출력

  

- 쓰기: `fputc(ch, fp)`

- 읽기: `fgetc(fp)`

  

---