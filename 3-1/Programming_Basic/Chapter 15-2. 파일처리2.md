# 이진 파일 입출력

# fwrite, fread 함수

- 파일에 입출력
- 이진 파일모드 쓰려면 rb wb 사용 

```c
size_t fwrite(const void *, size_t, size_t, FILE *); 
size_t fread(void *, size_t, size_t, FILE *);

int buffer[100];

FILE* fp1 = fopen("test.png", "rb");
FILE* fp2 = fopen("copy.png", "wb");

while (fread(buffer, sizeof(int), 100, fp1) != NULL) {
	fwrite(buffer, sizeof(int), 100, fp2);
}

fclose(f1);
fclose(f2);
```

# fseek 함수

- 파일 포인터 옮기기

```c
int fseek(FILE *fptr, long offset, int mode); //Long 타입 주의 ex)100L

fseek(f, 100L, SEEK_SET);
fseek(f, 100L, SEEK_CUR);
fseek(f, -100L, SEEK_END)

ftell(f); // 현재 파일 포인터 반환
rewind(f); // 현재 파일 포인터 0으로
```

# remove, rename 함수

- 파일 삭제, 이름 변경

```c
remove("sample.txt");
rename("sample.txt","new_name.txt");
```


