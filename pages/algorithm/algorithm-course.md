---
title: Algorithm course
last_updated: Oct 24, 2020
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: algorithm-course.html
summary: Summaries of algorithm course


---

## Day 1

### Structure constructor

```c++
struct Node {
    int from, to, cost;
    
    // interesting way of making a constrctor;
    // This is faster than the latter one, so stick with this.
    Node(int from, int to, int cost):
    from(from), to(to), cost(cost){}
    
    // usually it goes like;
    Node(int _from, int _to, int _cost){
    	from = _from;
        to = _to;
        cost = _cost;
    }
    
    // when you need 'std::sort' function, 
    // this helps without making another compare function.
    // const before '{' means that member variable wouldn't be changed.
    bool operator<(const Node& a)const{
        return a.cost < cost;
    }
}
```

------

## Day 3

### How to know if it's DP?

- When you put the same parameter, it always give you the same value, it can be made with DP.
- How to make is, to make those parameters as N-d array. For example, if the parameters are int a, int b, int c, then you can assign it to arr\[a\]\[b\]\[c\] to make it easy.
- Time complexity of DP is at least the size of the DP array. (Cause it will fill all of those up)
- When it's just getting sum of M th array in N, it's faster to go over once which will take M*N than making 2-D array with parameters.



### Array (= vector)

- O(1) to refer a data.

- O(N) to insert/delete a data.

  

### Linked List 

- O(N) to refer a data.

- O(1) to insert/delete a data.  However, to insert/delete middle of the list, it has to go N times to get there first. So it practically it might take O(N).

  

### deque (double ended queue)

- can be pushed or popped on both side.

  

### Priority Queue (When it's made of 1-D array using Binary Tree)

- O(1) to refer a max data.
- O(log N) to insert/delete a data. 
- Insert on the end of the binary tree, and have to switch log N times in worst case. (with its parent)
- Pop works as this; 
  - pop the root
  - put the last element to the root, and switch with its descendants to meet the condition.
  - this will take log N times just like insert. 
- On structure, you may imply like;

```c++
struct Node{
    int a,b,c;
    Node(int _a, int _b, int _c):a(_a),b(_b),c(_c){}
    
    // overriding comparator
    bool operator<(const Node& t) const {
        return /*my*/a > x.a;
    }
}
```

- Priority Queue vs. Sort
  - Priority Queue : When you need to insert/delete often and all you need is max or min value of all data.
  - Sort : If the dataset will not be changed or if the data has to be all sorted.



### Segment Tree

- [check this for visual aids](https://www.acmicpc.net/blog/view/9)

- **Segment tree can be used in various purposes. It's all up to what it'll store on its parents node.**

- Every nodes contains information of its coverage.
- Left node will contain its parent node's first half. **\[start, (start+end)/2\]**
- Right node will contain its parent node's last half. **\[(start+end)/2 + 1, end\]**
- Time Complexity
  - getting sections' sum : approximately, 2 (log (N-1) + 1) => O(log N)
  - update : O(log N)

```c++
// Segment tree example:
// every node has to have its coverage, but in this example, parameter shows the coverage.


/*
	1. update
	
	index : 트리 노드 인덱스(tree 기준 != array 기준)
	left, right : 노드가 커버하는 범위
	targer : 변경하고자 하는 인덱스(array 기준)
	value : 변경하고자 하는 값
*/
void update(int index, int left, int right, int target, int value) {
	// 이 두개는 end condition

	// 범위를 벗어났다면 끝
	if (target < left || right < target) {
		return;
	}
	// 도착
	if (left == right) { 
		tree[index] = value; 
		return;
	}

	// 범위 안에 들어가는 경우, left, right로 보내본다.
	// coverage : left ~ (left+right)/2
	update(index * 2, left, (left + right) / 2, target, value);		
	// coverage : (left+right)/2 + 1 ~ right
	update(index * 2 + 1, (left + right) / 2 + 1, right, target, value);	
	
	// 보내본 것이 다 끝나면 왼쪽, 오른쪽 값이 update가 되었을 것.
	// 자식들의 노드 값을 가지고 자기 값을 변경한다.
	tree[index] = tree[index * 2] + tree[index * 2 + 1];
}
// 이게 펜윅트리와 더 비슷하다
/*
void update(vector<long long> &tree, int node, int start, int end, int index, long long diff) {
	if (index < start || index > end) return;
	tree[node] = tree[node] + diff;
	if (start != end) {
		update(tree,node*2, start, (start+end)/2, index, diff);
		update(tree,node*2+1, (start+end)/2+1, end, index, diff);
	}
}
*/



/*
	2. query
	
	index : 트리 노드 인덱스(tree 기준 != array 기준)
	left, right : 노드가 커버하는 범위
	query_left, query_right : 더셈을 구하고자 하는 구간
*/
//query_left, query_right 가 구해야하는 범위
int query(int index, int left, int right, int query_left, int query_right) {
	//범위를 완전히 벗어났을 때
	if (left > query_right || right < query_left) return 0;
	//범위에 들어갔을 때
	if (query_left <= left && right <= query_right) return tree[index];

	//범위에 들어왔을때
	return (query(index * 2, left, (left + right) / 2, query_left, query_right) +
		query(index * 2 + 1, (left + right) / 2 + 1, right, query_left, query_right));
}


/*
	3. init
	
*/
void init(int index, int left, int right) {
	if (left == right) {
		//hard to understand, so I recommend you to drawing a tree and try it
		tree[index] = data[left];
		return;
	}
	init(index * 2, left, (left + right) / 2);
	init(index * 2 + 1, (left + right) / 2 + 1, right);
	tree[index] = tree[index * 2] + tree[index * 2 + 1];
	return;
}

```

### Hash map, set
- By converting key value into hash value, it reduces referring cost to O(1).
- However, if the number of key values are too high, hash values can be collide, and when it happenes it makes a link list on it → it may slow it down a bit.

------


## Day 4

### Stack

- [Example Problem](https://www.acmicpc.net/problem/2493)
- This can be used in many purposes, just have to be cleaver....

```c++

#include <iostream>
#include <vector>
#include <stack>

using namespace std;

int N, T;
stack<pair<int, int>> s;
vector<int> ans;

int main() {
    scanf("%d", &N);

    ans.resize(N);
    for (int i = 1; i <= N; i++) {
        int temp;
        scanf("%d", &temp);

        while (!s.empty() && s.top().first <= temp) s.pop();
        if (!s.empty()) ans[i-1] = s.top().second;
        s.push(pair<int, int>(temp, i));
    }

    for(auto item : ans){
        printf("%d ", item);
    }

	return 0;
}
```


### 'Two pointer' can be very useful
- it could either start with i = 0, j = 0, or i = 0, j = N.

### Modular (%)
- When the answer is get % of something, whenever you add or multiply, you can use % operation. 
- However, for subtraction and division, you may not use % each time since (a / x) % y != (b / x) % y.

### Prime numbers(Aristotle)
- Easy to remember, and understand.

```c++

// [연습A-0022] 소수경로 

#include <queue>
#include <algorithm>
#include <iostream>
#include <math.h>
#include <string.h>

using namespace std;

struct MyStruct
{
	int startN, cnt;
	MyStruct(int _start, int _cnt) :startN(_start), cnt(_cnt) {}
};

int T, startN, endN;
bool prime[10000] = { true, };
bool visited[10000];
int ans, offset[] = { 1, 10 ,100, 1000 };
queue<MyStruct> q;

bool bfsFn(const MyStruct& tempS) {
	for (int i = 0; i < 4; i++) {
		// pow 대신에 offset으로 하는게 좋다.
		// 까먹지 말것 1
		int base = tempS.startN - tempS.startN/offset[i]%10*offset[i];
		for (int j = 0; j < 10; j++) {
			int temp = base + j * offset[i];
			if (temp == endN) {
				ans = tempS.cnt;
				return true;
			}
			else if (!visited[temp] && prime[temp]) {
				q.push(MyStruct(temp, tempS.cnt+1));
				visited[temp];
			}
		}
	}
	return false;
}

int main() {
	scanf("%d", &T);

	for(int i = 0; i < 10000; i++){
		prime[i] = true;
	}

	for (int i = 2; i < 10000; i++) {
		if (prime[i]) {
			for (int j = i * 2; j < 10000; j += i) {
				prime[j] = false;
			}
		}
	}
	//1000 미만은 범위에서 벗어나니까 나중에 queue에 넣지 않도록
	// 까먹지 말것 2
	for (int i = 0; i < 1000; i++) {
		prime[i] = false;
	}

	memset(visited, 0, sizeof(visited));
	for (int loop = 1; loop <= T; loop++) {
		scanf("%d%d", &startN, &endN);
		
		ans = 0;
		visited[startN] = true;
		
		q.push(MyStruct(startN, 1));
		while (!q.empty()) {
			MyStruct tempS = q.front();
			q.pop();
			if (bfsFn(tempS)) {
				queue<MyStruct>().swap(q);
			}
		}

		printf("#%d %d\n", loop, startN == endN? 0:ans);
	}

	return 0;
}
```


### GCD(greatest common dividor)
- it can be used to find coprime.
  - for example, from 5/10, gcd of 5 and 10 is 5, and by dividing it to both numbers, it'll be 1/2.


```c++
//[교육A-0004] 금괴 

#include <queue>
#include <iostream>

using namespace std;

int T, N, M, ans;

int gcd(int a, int b) {
	if (b == 0) return a;
	else return gcd(b, a%b);
}

int main() {
	scanf("%d", &T);

	for (int loop = 1; loop <= T; loop++) {
		scanf("%d%d", &N, &M);

		int dividor = gcd(N%M, M);
		// 금괴를 이러붙여 1개라고 생각하면 됩.
		// 서로 서로소일 때, 자르는 갯수는 사람수-1이다.
		// 여기서 * gcd 값을 하면 된다.
		ans = (M / dividor - 1) * dividor;

		printf("#%d %d\n", loop, ans);
	}

	return 0;
}
```

------

## Day 5

### Pascal's triangle

- Super fast/easy way to get answer to combinations.

```c++
#include <iostream>

using namespace std;

//This will be overflowed
long long pas[100][100];

int main()
{
    for(int i = 1; i < 100; i++){
        //제일 처음과 마지막 값은 넣어주기.
        pas[i][0] = pas[i][i] = 1;
        for(int j = 1; j < i; j++){
            pas[i][j] = pas[i-1][j-1] + pas[i-1][j];
        }
    }
    
    for(int i = 0; i < 100; i++){
        pas[i][0] = pas[i][i] = 1;
        for(int j = 0; j <= i; j++){
            printf("%lld ", pas[i][j]);
        }
        printf("\n");
    }
    
    return 0;
}
```

------

## Things to remember

- 10,000 depth of recursion usually takes 1 MB of stack memory.
- 100,000,000 times of calculations would be equivalent to 1 second. (C++)
- 1024 = 2^10. So approximately, 100000 = 100* 2^10 = 2^7 * 2*10 = 2^17.
  - just remember 1000 is around 2^10
- array initialization: **<string.h> memset(flagArrNm, 0, sizeof(flagArrNm))**
- If there's too much of direction on matrix to go, it's often easier and less risky to set an array of those directions. 

```c++
// for example
int upDownRightLeft[] = { {-1,0}, {1,0}, {0,1}, {-1,0} };

for(int i = 0; i < 4; i++){
    int x += upDownRightLeft[0];
    int y += upDownRightLeft[1];
    if(!visited[x][y] && x >= 0 && x < size && y >= 0 && y < size){
        doDfsOrBfs(arr[x][y]);
    }
}
```



- Time Complexity 

  - Binary search - 
    $$
    log_2N
    $$

  - Sort - 
  	$$
    N log_2N
    $$

- You have to make sure why the solution you came up with is not working with reasons. If you don't know why it's not working, you would keep coming back thinking it might be right.
