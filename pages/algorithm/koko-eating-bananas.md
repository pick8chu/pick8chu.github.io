
---

title: leetcode- Koko Eating Bananas
last_updated: Jan 21, 2022
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: koko-eating-bananas.html
summary: Interesting ways of implementing binary search

---


## #. Problem

[Link](https://leetcode.com/problems/koko-eating-bananas/)



## 1. My approach

Assuming it would be somewhat bruteforce, I focus on finding a number to start, to reduce the total time of searching. 

```cpp
class Solution {
public:
    int minEatingSpeed(vector<int> piles, int h) {
        sort(piles.begin(), piles.end(), [](int& a, int& b){return a > b;});
        
        int idx = 1 <= h/piles.size() ? piles.size()-1 : h - piles.size();
        int curMin = piles[idx];
        while(!done(piles, h, curMin)) curMin++;
        return curMin;
    }
    
    bool done(vector<int>& piles, int h, int curMin){
        int ans = 0;
        for(auto pile : piles){
            ans += ceil(pile/(float)curMin);
        }
        // cout << h << " >= " << ans << endl;
        return h >= ans;
    }
};
```
I failed with the case: [312884470] 312884469.
So I changed *curMin* to start from 1 and now stuck at the part where "ans +=" since pile + pile could bigger than 2^31 -1 (int max). 


## 2. Solutions

Using binary search from min of 1 and max among piles, you can navigate the minimum number.

```c++
int minEatingSpeed(vector<int>& piles, int h) {
        int low = 1;
        int high = INT_MIN;
        
        // number lies between min and max-element
        for(auto x : piles){
            high = max(x,high);
        }
        
        int val = 0;
        while(low<high){
            int mid = (low+high)/2; val = 0;
            // calculate val for each mid assumed
            for (auto x : piles) val += (x+mid-1)/mid; 
            
            if (val <= h) high = mid; 
            
            else low = mid+1; 
        }
        return low;
    }
```




## 3. Epilogue 

What I've learned from this exercise:

- Interesting to know that binray search could be applied in different situations.
