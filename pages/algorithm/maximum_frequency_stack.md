---
title: leetcode- Maximum Frequency Stack
last_updated: March 1, 2021
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: maximum_frequency_stack.html

---

## #. Problem

[Maximum Frequency Stack](https://leetcode.com/problems/maximum-frequency-stack/)



## 1. My approach

### Counting whenever popping

Time limits error. I thought it should be fine, since it only takes O(N) every pops.

```c++
class FreqStack {
private:
    vector<int> s;
    
public:
    FreqStack() {
        s.clear();
    }
    
    void push(int x) {
        s.push_back(x);
    }
    
    int pop() {
        map<int, int> m;
        int maxCnt = 0;
        int maxIdx = 0;
        for(int i = 0; i < s.size(); i++){
            m[s[i]]++;
            if(m[s[i]] >= maxCnt){
                maxCnt = m[s[i]];
                maxIdx = i;
            }
        }
        int res = s[maxIdx];
        m.clear();
        s.erase(s.begin()+maxIdx, s.begin()+maxIdx+1);
        return res;
    }
};

/**
 * Your FreqStack object will be instantiated and called as such:
 * FreqStack* obj = new FreqStack();
 * obj->push(x);
 * int param_2 = obj->pop();
 */
```





## 2. Two Maps with stack

Detail Explanations are belows:

1. [Solution page](https://leetcode.com/problems/maximum-frequency-stack/discuss/1086635/C%2B%2B-or-Hash-map-%2B-Stack-or-O(1)-Beats-100-or-Explanation)



```c++
class FreqStack {
private:
    int cur_max;
    unordered_map<int, int> freq_m;
    unordered_map<int, vector<int>> freq_mv;
    
public:
    FreqStack() {
        cur_max = 0;
    }
    
    void push(int x) {
        freq_m[x]++;
        
        if(freq_m[x] >= cur_max){
            cur_max = freq_m[x];
        };
        
        freq_mv[freq_m[x]].push_back(x);
    }
    
    int pop() {
        int res = freq_mv[cur_max].back();
        
        freq_mv[cur_max].pop_back();
        freq_m[res]--;
        
        while(freq_mv[cur_max].empty() && cur_max >= 0) cur_max--;
        
        return res;
    }
};

/**
 * Your FreqStack object will be instantiated and called as such:
 * FreqStack* obj = new FreqStack();
 * obj->push(x);
 * int param_2 = obj->pop();
 */
```




## 3. Epilogue 

What I've learned from this exercise:

- Keep learning, there's always more to learn
