## 주소

- 메모리의 저장장소 위치를 나타냄
- &를 통해 주소 값을 가져올 수 있다

## 리틀엔디안 & 빅엔디안

- 순서가 서로 반대

## 포인터 변수

- 메모리의 주소를 저장할 수 있는 변수

```c
int i;
int *ptr = &i;
```

## 역참조 연산자

- 포인터 변수 앞에 \* 을 붙임으로서 그 포인터가 가리키는 변수를 지칭

```c
int i;
int *ptr = &i;
*ptr = i + 2;
```

## 배열과 포인터

- 주소값을 이용해서 배열에 접근할 수 있다.

```c
int summary(int *ary){
	arr[5] = 1;
	// arr += 5;
	// arr = 1;
}

int main(){
	int arr[5];
	summary(arr);
}
```

## Call by value

- 실인자에는 영향이 없음

## Call by address

- 실인자에  영향이 끼침

## 문자열 다루기

```c
int i;
char name[20], result[100];

scanf("%s", name);

for (i = 0; i < strlen(name); i++)
	printf("%s\n", &name[i]);
```

## 문자 포인터 배열의 이용

- 포인터의 배열

```c
char *arr[]; // 이차원 배열
```

