
# 입출력 스트림 분리의 이점

- 입력에 상관없이 출력 가능
- 버퍼링 기능 향상

---
## 스트림 분리 이후 half close

![[halfclose1.png]]

```c
readfp = fdopen(clnt_sock, "r"); 
writefp = fdopen(clnt_sock, "w"); 

fclose(writefp);
// 이렇게 해도 half close 안됨
```


![[halfclose2.png]]

- dup, dup2 함수

```c
int dup(int filedes); //걍 남는 번호로 복사
int dup2(int filedes, int filedes2); //filede2로 복사

cfd1 = dup(1); 
cfd2 = dup2(cfd1, 7);
```

```c
readfp = fdopen(clnt_sock, "r"); 
writefp = fdopen(dup(clnt_sock), "w"); 
//그냥 소켓 두개 쓰는거

fputs("FROM SERVER: Hi~ client? \n", writefp); 
fputs("I love all of the world \n", writefp); 
fputs("You are awesome! \n", writefp); 
fflush(writefp); 

shutdown(fileno(writefp), SHUT_WR); 
fclose(writefp);
```

