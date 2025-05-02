## Scheduling with Deadlines

- 데드라인을 넘기전에 처리해야 이득을 얻을 수 있다.
- 최대의 이득을 얻을 수 있는 경우를 찾아야한다.
- 데드라인을 넘긴 스케줄은 처리하지 않아도 된다.

## Greedy approach

1.  profit 으로 내림차순 정렬
2.  set에 스케줄을 넣어보며 가능한지 확인 

 ![[Algorithm_feasible_set.png]]

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

```c
typedef vector sequence_of_integer; 

bool is_feasible(sequence_of_integer& K, sequence_of_integer& deadline){ 
		for (int i = 1; i < K.size(); i++) 
			if (i > deadline[K[i]]) 
				return false; 
		return true; 
}
```

```c
	void schedule(int n, sequence_of_integer& deadline, sequence_of_integer &J){
	sequence_of_integer K; 
	J.clear(); 
	J.push_back(0);
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

## Huffman Code

- prefix code 가 다른 code에 들어가 있지 않음
- 우선순위 큐를 이용하여 frequency 가 낮은 character 두 개를 pop 하고 합쳐서 다시 push

```c
void huffman(int n, PriorityQueue& PQ) {
	for (int i = 1; i <= n - 1; i++) { 
		node_ptr p = PQ.top(); 
		PQ.pop(); 
		node_ptr q = PQ.top(); 
		PQ.pop(); 
		node_ptr r = create_node('+', p->frequency + q->frequency); 
		r->left = p;
		r->right = q; 
		PQ.push(r); 
	} 
}
```

![[Huffman1.png]]![[Huffman2.png]]

## Encoding Huffman Code (char -> string)

<char, string> 해쉬 맵을 만들어 각 알파벳의 허프만 코드를 Encoding 한다.

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
![[Pasted image 20250502134401.png]]

## Decoding Huffman Code (string -> char)

node 가 + 가 아닐 때까지 (리프노드까지) 타고 내려가서 출력

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

![[Pasted image 20250502134655.png]]
