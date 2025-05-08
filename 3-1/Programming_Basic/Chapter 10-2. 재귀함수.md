## 재귀

- 함수 구현에서 자기 자신의 함수를 호출

## 피보나치 수열

```c
//재귀적 구현
int fib( unsigned int n ) {
	static int fibScore[Max] ={0};
	if (n == 0 || n == 1) 
		return n;
	if ( fibScore[n] == 0 ) 
		fibScore[n] = fib(n-1) + fib(n-2); 
	return fibScore[n]; 
}

//반복적 구현
int fib( unsigned int n ) { 
	int fibScore[Max] = {0}; 
	int i; 
	fibScore[0] = 0; 
	fibScore[1] = 1; 
	for(i = 2; i <= n; i++) { 
		fibScore[i] = fibScore[i-1] + fibScore[i-2]; 
	} 
	return fibScore[n]; 
}
```

## 유클리드 호제법

```c
return gcd(min, max % min);
```

- 큰 수를 작은 수로 나눈 나머지와 작은 수를 돌리면 됨
## 이진탐색

```c
if(arr[mid] > value)
	return Bs(arr, value, low, mid - 1);
else if(arr[mid] < value)
	return Bs(arr, value, mid + 1, high);
else
	return mid;
```

- 절반씩 범위를 줄이며 탐색
## 난수

```c
srand(time(NULL));
int rn = rand() % 10 + 1;
```


