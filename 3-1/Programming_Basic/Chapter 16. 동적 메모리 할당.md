
# 동적 할당

- 런타임에 메모리 할당

---
# malloc 함수

- 메모리 할당

```c
int *pi = (int *)malloc(sizeof(int) * 3);
*pi = 1;
pi[1] = 2, pi[2] = 3; 

int **ary = (int **)malloc(sizeof(int) * 4);
for(int i = 0; i < 4; i++){
	ary[i] = (int *)malloc(sizeof(int) * 4);
}

free(pi);
for(int i = 0; i < 4; i++){
	free(ary[i]);
}
free(ary);
```

---
# calloc 함수

- 메모리 할당하면서 0으로 초기화

```c
int *ary = (int *)calloc(3, sizeof(int));
```

---
# realloc 함수

- 이미 할당한 공간을 새로운 크기로 변경

```c
ary = (int *)realloc(ary, sizeof(int) * 10);
```

---

# 자기 참조 구조체

- 구조체의 멤버변수 중의 하나가 자기 자신의 구조체 포인터 변수를 가짐

```c
struct self{
	int n;
	struct self* next;
}
```

---

# 연결 리스트

- 자기 참조 구조체가 연결된 구조

```c
typedef struct self{
	int n;
	struct self* next;
}self;

int num;
self* cur, p = NULL;

for(int i = 0; i < 5; i++){
	scanf("%d",&num);
	cur = (self*)malloc(sizeof(self));
	cur->n = num;
	cur->n = p;
	p = cur;
}
```
