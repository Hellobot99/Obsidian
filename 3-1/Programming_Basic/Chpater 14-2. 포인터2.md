## 다중 포인터

- 포인터 변수의 주소 값을 갖는 포인터 변수

```c
int i = 1;
int *point = &i;
int **point2 = &point;

printf("%d",**point2); // **point = i
```

## 포인터 배열

- 주소 값을 저장하는 포인터를 요소로 하는 배열

```c
int *pAry[3];
```

## 이차원 배열의 의미

```c
int tAry[][3] = {{1, 20, 12}, {3, 5, 16}}; 일 경우
sizeof(tAry) // 24
sizeof(tAry[0]) // 12
sizeof(tAry[0][0]) // 4
```

## 이차원 배열에서 원소의 표현

```c
tAry[i][j] 를 표현 하고 싶을 때

*( tAry[i] +j )
*( *(tAry + i) + j )
*( *tAry + (열의 수)*i + j )
*( &tAry[0][0] + (열의 수)*i + j )
( *(tAry + i) )[j]
```

## 함수 포인터

- 함수의 주소 값을 저장하는 변수

```c
void *add (int a, int b);
void (*pfary[4]) (double*, double, double) = {add, substract, multiply, devide};
리턴 타입, argument 같아야 함
```