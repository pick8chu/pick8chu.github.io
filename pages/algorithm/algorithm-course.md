---
title: algorithm course
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





## Things to remember

- 10,000 depth of recursion usually takes 1 MB of stack memory.
- 100,000,000 times of calculations would be equivalent to 1 second. (C++)
- 1024 = 2^10. So approximately, 100000 = 100* 2^10 = 2^7 * 2*10 = 2^17.
  - just remember 1000 is around 2^10
- array initialization: **<string.h> memset(flagArrNm, 0, sizeof(flagArrNm))**
- If there's too much of direction on matrix to go, it's often easier and less risky to set an array of those directions. 

```c++
// for example
int upDownRightLeft[] = {{-1,0},{1,0},{0,1},{-1,0}}

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

