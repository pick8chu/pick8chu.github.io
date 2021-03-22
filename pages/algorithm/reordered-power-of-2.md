---
title: leetcode- Reordered Power of 2
last_updated: March 22nd, 2021
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: reordered-power-of-2.html
summary: map

---

## #. Problem

[Problem link](https://leetcode.com/problems/reordered-power-of-2/)



## 1. My approach 

I first thought of using a map, since you only need to see if you have the same amount of the numbers, but I was thinking of making a vector<map<int,int>>, which the contents of vector would be 1 to 10^9. I just thought it won't work. But if I think about it, I only need to make 32 vectors, 2^0 to 2^32. Well, it could've been faster.

Instead, how I did was, get all the permutations, and see if that's power of 2.

```c++
class Solution {
public:
    bool reorderedPowerOf2(int N) {
        string temp = to_string(N);
        vector<bool> flag(10, true);
        bool res = false;
        for(int i = 0; i < temp.size(); i++){
            if(temp[i] == '0') continue;
            flag[i] = false;
            res |= recursive(string(1,temp[i]), temp, flag);
            flag[i] = true;
        }
        
        return res;
    }
    
    bool recursive(string curS, const string& s, vector<bool>& flag){
        if(curS.size() == s.size()){
            int temp = stoi(curS);
            while(temp % 2 == 0) temp /= 2;
            
            return temp == 1;
        }
        
        bool res = false;
        for(int i = 0; i < s.size(); i++){
            if(flag[i]){
                flag[i] = false;
                res |= recursive(curS+s[i], s, flag);
                flag[i] = true;
            }
        }
        
        return res;
    }
};
```

| Time - O(9! * 32(at most)) | Space - O(1) |
| -------------------------- | ------------ |
| 1392 ms                    | 6 MB         |

## 2. Solution

1. [Solution page](https://leetcode.com/problems/reordered-power-of-2/solution/)
2. [Discussion page](https://leetcode.com/problems/reordered-power-of-2/discuss/1120153/C%2B%2B-Super-Simple-and-Short-Solution-0-ms-Faster-than-100)



Since it only has 32 values that are inside of INT, I could have get 32 numbers, and compare it to the number. But the way you compare it would be counting, otherwise you'll have to do the permutation which makes it very slow.



### With Sort

```c++
class Solution {
public:
    bool reorderedPowerOf2(int N) {
        string N_str = sorted_num(N);
        for (int i = 0; i < 32; i++)
            if (N_str == sorted_num(1 << i)) return true;
        return false;
    }
    
    string sorted_num(int n) {
        string res = to_string(n);
        sort(res.begin(), res.end());
        return res;
    }
};
```



### With hash map

```c++
class Solution {
public:
    bool reorderedPowerOf2(int N) {
        unordered_map<int, int> digit_count_n;
        countDigit(N, digit_count_n);
        for (int i = 1; i > 0 && i / 10 < N; i <<= 1) {
            unordered_map<int, int> digit_count_i;
            countDigit(i, digit_count_i);
            if (digit_count_n == digit_count_i) {
                return true;
            }
        }
        return false;
    }

    void countDigit(int num, unordered_map<int, int>& digit_count) {
        for (int i = num; i > 0; i /= 10) {
            int v = i % 10;
            digit_count[v]++;
        }
    }
};
```



| Time - O(32) = O(1) | Space - O(1) |
| ------------------- | ------------ |
| 0 ms                | 5.9 MB       |



## 3. Epilogue 

What I've learned from this exercise:

- Always compares with smaller side first. 
