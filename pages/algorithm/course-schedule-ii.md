---
title: leetcode- Course Schedule II
last_updated: Dec 24, 2021
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: course-schedule-ii.html
summary: Graph algorithm - topological algorithm

---

## #. Problem

[Link](https://leetcode.com/problems/course-schedule-ii/)



## 1. My approach

Brute-force. 

I didn't think it as Graph at first. 

It has 3 layers of for(while) loops, would probably make the worst case of N^3.




```c++
class Solution {
public:
    vector<int> findOrder(int num, vector<vector<int>>& arr) {
        if(num == 1) return {0};
        
        vector<int> ans;
        vector<set<int>> v(num);
        vector<bool> taken(num, false);
        for(auto& el : arr){
            v[el[0]].insert(el[1]);
        }
        
        bool finish = false;
        while(!finish){
            finish = true;
            for(int i = 0; i < num; i++){
                if(!taken[i] && v[i].empty()){
                    ans.push_back(i);
                    taken[i] = true;
                    for(auto& el : v){
                        if(el.find(i) != el.end()) el.erase(i);
                    }
                    
                    finish = false;
                }
            }
        }
        
        return ans.size() < num? vector<int>():ans;

    }
};
```



This passed the limits, however, I was only at bottom 5% which made me think I should try again.

| Time(O(N^3)) | Space(O(N)) |
| ------------ | ----------- |
| 112 ms       | 14.3 MB     |



## 2. Different Approach

Although it's not exactly the same with topological algorithm, I managed to make somewhat similar algorithm.

The idea was, to start validating from prerequisite courses to classes that you can take by taking the prerequisite course. This way, you won't need to visit the same node more than once, although there will be cases that it would reach a node and won't add to 'ans' array since it still has prerequisite courses. In this cases, it's possible to visit a node more than once, however it won't do any calculations, so it won't affect as much to the time complexity.

```c++
class Solution {
public:
    vector<int> findOrder(int num, vector<vector<int>>& arr) {
        if(num == 1) return {0};
        
        vector<int> ans;
        vector<set<int>> preToTarget(num);
        vector<set<int>> pre(num);
        vector<bool> taken(num, false);
        
        for(auto& el : arr){
            preToTarget[el[1]].insert(el[0]);
            pre[el[0]].insert(el[1]);
        }
        
        for(int i = 0; i < num; i++){
            if(!taken[i] && pre[i].empty()){
                ans.push_back(i);
                taken[i] = true;
                digDeep(i, ans, preToTarget, pre, taken);
            }
        }
        
        return ans.size() < num? vector<int>():ans;

    }
    
    
    void digDeep(int curIdx, vector<int>& ans, vector<set<int>>& preToTarget, vector<set<int>>& pre, vector<bool>& taken){
        for(auto& el : preToTarget[curIdx]){
            pre[el].erase(curIdx);
            if(!taken[el] && pre[el].empty()){
                ans.push_back(el);
                taken[el] = true;
                digDeep(el, ans, preToTarget, pre, taken);
            }
        }
    }
};
```

Was way faster, since it would visit every nodes only once. But considering the differences of time complexity, it didn't show very much of differences, interestingly.

| Time(O(N or V+E)) | Space(O(N)) |
| ----------------- | ----------- |
| 16 ms             | 17 MB       |






## 3. [Solutions](https://leetcode.com/problems/course-schedule-ii/solution/)

Topological Sort.

The idea doesn't seem to so different from my second approach.



```java
class Solution {
  static int WHITE = 1;
  static int GRAY = 2;
  static int BLACK = 3;

  boolean isPossible;
  Map<Integer, Integer> color;
  Map<Integer, List<Integer>> adjList;
  List<Integer> topologicalOrder;

  private void init(int numCourses) {
    this.isPossible = true;
    this.color = new HashMap<Integer, Integer>();
    this.adjList = new HashMap<Integer, List<Integer>>();
    this.topologicalOrder = new ArrayList<Integer>();

    // By default all vertces are WHITE
    for (int i = 0; i < numCourses; i++) {
      this.color.put(i, WHITE);
    }
  }

  private void dfs(int node) {

    // Don't recurse further if we found a cycle already
    if (!this.isPossible) {
      return;
    }

    // Start the recursion
    this.color.put(node, GRAY);

    // Traverse on neighboring vertices
    for (Integer neighbor : this.adjList.getOrDefault(node, new ArrayList<Integer>())) {
      if (this.color.get(neighbor) == WHITE) {
        this.dfs(neighbor);
      } else if (this.color.get(neighbor) == GRAY) {
        // An edge to a GRAY vertex represents a cycle
        this.isPossible = false;
      }
    }

    // Recursion ends. We mark it as black
    this.color.put(node, BLACK);
    this.topologicalOrder.add(node);
  }

  public int[] findOrder(int numCourses, int[][] prerequisites) {

    this.init(numCourses);

    // Create the adjacency list representation of the graph
    for (int i = 0; i < prerequisites.length; i++) {
      int dest = prerequisites[i][0];
      int src = prerequisites[i][1];
      List<Integer> lst = adjList.getOrDefault(src, new ArrayList<Integer>());
      lst.add(dest);
      adjList.put(src, lst);
    }

    // If the node is unprocessed, then call dfs on it.
    for (int i = 0; i < numCourses; i++) {
      if (this.color.get(i) == WHITE) {
        this.dfs(i);
      }
    }

    int[] order;
    if (this.isPossible) {
      order = new int[numCourses];
      for (int i = 0; i < numCourses; i++) {
        order[i] = this.topologicalOrder.get(numCourses - i - 1);
      }
    } else {
      order = new int[0];
    }

    return order;
  }
}
```



| Time(O(V+E)) | Space(O(V+E)) |
| ------------ | ------------- |
| - ms         | - MB          |



## 3. Epilogue 

What I've learned from this exercise:

- Be more detail oriented, and always aware that there should be better ways.
