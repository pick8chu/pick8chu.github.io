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

- Every nodes contains information of its coverage.
- Left node will contain its parent node's first half. **\[start, (start+end)/2\]**
- Right node will contain its parent node's last half. **\[(start+end)/2 + 1, end\]**
- Time Complexity
  - getting sections' sum : approximately, 2 (log (N-1) + 1) => O(log N)
  - update : O(log N)




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
