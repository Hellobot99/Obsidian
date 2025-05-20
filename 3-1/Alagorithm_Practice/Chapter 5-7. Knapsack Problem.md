## Fractional Knapscak Problem

- 아이템이 분할 가능한 경우 그리디로 풀 수 있다.
- 단위 무게당 가치가 높은 거부터 채우면 됨.

```c
void knapsack1(int& maxprofit, int& totweight) { 
	maxprofit = totweight = 0; 
	for (int i = 1; i <= n; i++) { 
		if (totweight + items[i].weight <= W) { 
			maxprofit += items[i].profit; 
			totweight += items[i].weight; 
		} 
		else { 
			maxprofit += (W - totweight) * items[i].profit_per_unit; 
			totweight += (W - totweight); 
			break;
		} 
	} 
}
```

---
## 0-1 Knapsack Problem

- 아이템이 분할 불가능한 경우 그리디로 풀 수 없다. DP or Backtracking

---
#### DP 접근

![[Pasted image 20250520114814.png]]

- P \[몇 번 아이템까지 선택가능인지] \[최대 무게]
- 왼쪽 서브 트리 : 선택 x (선택안할거니까 선택가능에서 제외)
- 오른쪽 서브 트리 : 선택 o (선택했으니 무게에서 선택한 아이템 무게 빼기)

```c
int knapsack3(int n, int W, int w[], int p[], map, int> &P) { 
	if (n == 0 || W <= 0) 
		return 0; 
	int lvalue = (P.find(make_pair(n-1, W)) != P.end()) 
	? P[make_pair(n-1, W)] : knapsack3(n-1, W, w, p, P); 
	int rvalue = (P.find(make_pair(n-1, W-w[n])) != P.end()) 
	? P[make_pair(n-1, W-w[n])] : knapsack3(n-1, W-w[n], w, p, P); 
	P[make_pair(n, W)] = (w[n] > W) 
	? lvalue : max(lvalue, p[n] + rvalue); 
	return P[make_pair(n, W)]; 
}
```

---
#### Backtracking 접근

- Promising : 무게 합이 최대 무게 보다 작아야함.
- Pruning : 계산된 bound가 지금까지 찾은 최고 profit 보다 작거나 같으면 잘라내기
- 무게 당 가치 내림차순으로 정렬
ex) n =4,W = 16, p\[40,30,50,10], w\[2,5,10,5], p/w\[20,6,5,2]

![[Pasted image 20250520120134.png]]

1. 방문한 노드에서 얻을 수 있는 최대 이익 계산 후 maxprofit 최신화
2. bound 계산해서 maxprofit 보다 작으면 잘라내기

```c
bool promising(int i, int profit, int weight) { 
	int j, k, totweight; 
	float bound; 
	if (weight >= W) return false; 
	else { 
		j = i + 1; bound = profit; 
		totweight = weight; 
		while (j <= n && totweight + w[j] <= W) { 
			totweight += w[j]; 
			bound += p[j]; j++; 
		} 
		k = j; 
		if (k <= n) 
			bound += (W - totweight) * ((float)p[k] / w[k]); 
		return bound > maxprofit; 
	} 
}

void knapsack4(int i, int profit, int weight) { 
	if (weight <= W && profit > maxprofit) { 
		maxprofit = profit; array_copy(include, bestset); 
	} 
	if (promising(i, profit, weight)) { 
		include[i + 1] = true; 
		knapsack4(i + 1, profit + p[i + 1], weight + w[i + 1]); 
		include[i + 1] = false; 
		knapsack4(i + 1, profit, weight); 
	} 
}
```