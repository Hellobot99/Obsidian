
## fopen 함수

- 파일 열기

```c
FILE* fp;
if((fp = fopen("asd.txt","w")) == NULL){
	//파일 열기 에러
}
fclose(fp);
```

![[filemode.png]]

## 파일 입출력 함수

- 파일에 출력하기
- 파일에서 입력받기

```c
FILE *fp = fopen("asd.txt");
char *input, a;

fscanf(fp,"%s",input);
fprintf(fp,"%s",input);

fgets(input, fp);
fputs(input, fp);

a = fgetc(fp);
fputc(a,fp);

fclose(fp);
```

## feof 함수, ferror 함수

- feof : 파일의 내부 포인터 위치가 파일의 끝(EOF) 인지 검사
- ferror : 파일 처리에서 오류가 발생했는지 검사

```c
FILE *fp;
char c;

fp = fopen("asd.c","r");

while(!feof(fp))
	getchar();
```


