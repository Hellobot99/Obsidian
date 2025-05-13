
## 파이프 함수

- int pipe(int filedes[2])

![[pipe.png]]

```c
pipe(fds1); 
pipe(fds2); 
pid = fork()

if(pid == 0) { 
	write(fds1[1], str1, sizeof(str1)); 
	read(fds2[0], buf, BUF_SIZE); 
	printf("Child Proc output: %s\n", buf); 
} 
else{ 
	read(fds1[0], buf, BUF_SIZE); 
	printf("Parent proc output: %s\n", buf); 
	write(fds2[1], str2, sizeof(str2)); 
	sleep(3); 
}
```