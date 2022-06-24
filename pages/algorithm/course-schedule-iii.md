---
title: Course Schedule III
last_updated: Jun 24, 2022
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: course-schedule-iii.html

---


## #. Problem

[Link](https://leetcode.com/problems/course-schedule-iii/)



## 2. My approach

After Sorting with the *'lastday'* ascending order, made a array that will contain the best possiblity on *i*-th time. For example, dp[10] will indicate the most number of courses that one can take on day 10. 

```cpp
class Solution {
public:
    int scheduleCourse(vector<vector<int>>& courses) {
        vector<int> dp = vector<int>(10001, 0);
        int curMax = 0;
        
        sort(courses.begin(), courses.end(), [](vector<int>& a, vector<int>& b){
            if(a[1] == b[1]){
                return a[0] < b[0];
            } else {
                return a[1] < b[1];
            }
        });
        
        for(int i = 0; i < courses.size(); i++){
            if(courses[i][0] > courses[i][1]) continue;
            
            int fineBefore = courses[i][1] - courses[i][0];
            
            for(int j = fineBefore; j >= 0 ; j--){
                dp[j+courses[i][0]] = max(dp[j] + 1, dp[j+courses[i][0]]);
                curMax = max(dp[j] + 1, curMax);
            }
        }
        
        return curMax;
        
    }
};
```

This shows TLE. It should work fine, considering *N = 10,000*. It's only *N^2*.


## 2. Solution

It has better solution however. Refered from [here](https://dev.to/seanpgallivan/solution-course-schedule-iii-48hn).
Containing the smallest number as going through iteration seems like a very good idea.
Very well done.

```cpp
class Solution {
public:
    int scheduleCourse(vector<vector<int>>& C) {
        sort(C.begin(), C.end(), [](auto &a, auto &b) {return a[1] < b[1];});
        priority_queue<int> pq;
        int total = 0;
        for (auto &course : C) {
            int dur = course[0], end = course[1];
            if (dur + total <= end) 
                total += dur, pq.push(dur);
            else if (pq.size() && pq.top() > dur)
                total += dur - pq.top(), pq.pop(), pq.push(dur);
        }
        return pq.size();
    }
};
```
Time Complexity: O(N * log N)

## 3. Epilogue 

What I've learned from this exercise:

- Brain storming!
