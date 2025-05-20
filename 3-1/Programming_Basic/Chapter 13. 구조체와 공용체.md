## 구조체

- 서로 다른 자료형을 하나의 자료형으로 묶음

```c
// 구조체 정의
struct book
{
	char title[50];
	char author[50];
	int pages;
	int price;
};

// 일회용 구조체 변수, 후에 선언 불가
struct
{
	char title[50];
	char author[50];
	int pages;
	int price;
}book1;

// 구조체 정의 및 선언
typedef struct
{
	char title[50];
	char author[50];
	int pages;
	int price;
}book2;

int main(){
	struct book mybook;
	book1 mybook1;
	book2 mybook2;
}
```

- 구조체 정의 때 초기 값 대입 불가능

## 구조체 포인터

- 구조체 변수를 가리키는 주소 값을 저장하는 포인터 변수

```c
typedef struct
{
	char title[50];
	char author[50];
	int pages;
	int price;
}book;

book a;
book *p = &a;
a.price = 20; // 구조체 변수에서 멤버 변수 접근 시 . 사용
p->pages = 10; // 구조체 포인터 변수로 멤버 변수 접근 시 -> 사용
```

## 구조체 배열

```c
struct book { 
char author[50]; 
char title[50]; 
int pages; 
}; 

struct book clang[3] = { 
	{"Deitel", "C How To Program", 600}, 
	{"Al Kelly", "A Book On C", 700}, 
	{"Stephen Prata", "C Primer Plus", 800} 
};
```

## 공용체

- 저장공간을 아끼기 위해 서로 다른 자료형을 동일한 저장 공간에 이용

```c
union data{
	char ch;
	int cnt;
	double real;
} data1, *pdata;
```
## 열거형

- 정수형 상수 목록의 집합

```c
typedef enum color { 
	yellow = 2, red = 3, blue = 4, magenta = 5, green = 6 
} color;

int main() {
	
	color col = yellow;
	printf("%d", col);
	
	return 0;
}
```

