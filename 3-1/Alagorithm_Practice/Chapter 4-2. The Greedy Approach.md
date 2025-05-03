# Scheduling with Deadlines

- 데드라인을 넘기기 전에 처리해야 이득(profit)을 얻을 수 있다.
- **최대 이득**을 얻을 수 있는 경우를 찾아야 한다.
- 데드라인을 넘긴 스케줄은 **처리하지 않아도 된다**.

## Greedy Approach

1. `profit` 기준으로 내림차순 정렬
2. 가능한 경우 set에 스케줄을 넣는다 (feasible한지 확인)

> ![feasible set algorithm diagram](Algorithm_feasible_set.png)

### 기본 알고리즘 (의사 코드)

```c
void schedule(int n, int dealine[], sequence_of_integer &J) { 
	int i; 
	sequence_of_integer K; 
	J = [1]; 
	for (i = 2; i <= n; i++) {
		K = J with i added according to nondecreasing values of deadline[i];
		if (K is feasible) 
			J = K; 
	} 
}
```

### Feasibility 확인 함수

```c
typedef vector sequence_of_integer; 

bool is_feasible(sequence_of_integer& K, sequence_of_integer& deadline){ 
	for (int i = 1; i < K.size(); i++) 
		if (i > deadline[K[i]]) 
			return false; 
	return true; 
}
```

### 개선된 스케줄 함수

```c
void schedule(int n, sequence_of_integer& deadline, sequence_of_integer &J) {
	sequence_of_integer K; 
	J.clear(); 
	J.push_back(0);  // dummy
	J.push_back(1); 

	for (int i = 2; i <= n; i++) {
		K.resize(J.size());
		copy(J.begin(), J.end(), K.begin()); 

		int j = 1; 
		while (j < K.size() && deadline[K[j]] <= deadline[i]) 
			j++; 

		K.insert(K.begin() + j, i); 
		
		if (is_feasible(K, deadline)) {
			J.resize(K.size()); 
			copy(K.begin(), K.end(), J.begin()); 
		} 
	} 
}
```

---

# Huffman Code

- **Prefix Code**: 어떤 코드도 다른 코드의 접두어가 되지 않음
- 우선순위 큐(Priority Queue)를 사용하여 **frequency가 낮은 문자 2개**를 병합

> ![Huffman diagram 1](Huffman1.png)  
> ![Huffman diagram 2](Huffman2.png)

## 허프만 트리 구성

```c
void huffman(int n, PriorityQueue& PQ) {
	for (int i = 1; i <= n - 1; i++) { 
		node_ptr p = PQ.top(); PQ.pop(); 
		node_ptr q = PQ.top(); PQ.pop(); 
		node_ptr r = create_node('+', p->frequency + q->frequency); 
		r->left = p;
		r->right = q; 
		PQ.push(r); 
	} 
}
```

---

## Huffman Encoding (char → string)

- `<char, string>` 형태의 map을 만들어 각 문자의 허프만 코드를 저장

```c
void make_encoder(node_ptr node, string code, map &encoder) { 
	if (node->symbol != '+') 
		encoder[node->symbol] = code; 
	else { 
		make_encoder(node->left, code + "0", encoder); 
		make_encoder(node->right, code + "1", encoder); 
	} 
}
```

> ![Huffman Encoding example](Huffman3.png)

---

## Huffman Decoding (string → char)

- 리프 노드에 도달할 때까지 타고 내려가며 디코딩

```c
void decode(node_ptr root, node_ptr node, string s, int i) {
	if (i <= s.length()) { 
		if (node->symbol != '+') { 
			cout << node->symbol; 
			decode(root, root, s, i); 
		} 
		else { 
			if (s[i] == '0') 
				decode(root, node->left, s, i + 1); 
			else 
				decode(root, node->right, s, i + 1); 
		} 
	} 
}
```

> ![Huffman Decoding example](Huffman4.png)