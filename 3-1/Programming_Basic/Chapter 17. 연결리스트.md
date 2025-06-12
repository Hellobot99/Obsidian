
```c
typedef struct data{
	int d;
	struct data* next;
}data;

data* h = NULL;
curr = (data*)malloc(sizeof(data));
curr->d = num;

// 맨 앞에 추가
curr->next = h;
h=curr;

// 맨 뒤에 추가
if(h==NULL) h = curr;
else{
	while(p->next!=NULL){
		p = p->next;
	}
	p->next = curr;
}

// 중간에 추가 (오름차순인 경우)
if(h==NULL) h = curr;
while(p!=NULL){
	if(p->data > data)
		break;
	q = p;
	p = p->next;
}
```