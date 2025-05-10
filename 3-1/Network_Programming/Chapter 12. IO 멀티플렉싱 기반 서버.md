
## select 함수

int select(int maxfd, fd_set \*readset, fd_set \*writeset, fd_set \*exceptset, const struct timeval \*timeout);

	fd_set set / 파일 디스크립터

파일 디스크립터 설정

	FD_ZERO 0으로 초기화
	FD_SET 정보 등록
	FD_CLR 정보 삭제
	FD_ISSET 정보가 있으면 반환


변화가 없는 파일 디스크립터는 0으로 초기화 되기 때문에 기존 설정을 복사 해놔야함

```c
fd_set set, temp;

FD_ZERO(&set);
FD_SET(0, &set);

while(1){
	temp = set;
	timeout.tv_sec = 5;

	result = select(1, &set, 0, 0, &timeout);

	if(result == -1) {
		break; 
	} 
	else if(result == 0) { 
		puts("Time-out!"); 
	} 
	else { 
		if(FD_ISSET(0, &temps)) { 
			printf("message from console: %s", buf); 
		} 
	}
}
```
