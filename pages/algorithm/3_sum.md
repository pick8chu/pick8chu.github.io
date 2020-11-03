---
title: leetcode- 3 sum
last_updated: Nov 3, 2020
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: 3_sum.html
summary: To compare my method and someone else's. 

---

## #. Problem

https://leetcode.com/problems/3sum/



## 1. My approach

So, I firstly tried with 3 for loops, and of course I failed. After that, I tried with 2 for loop + binary search. Since 'nums.length <= 3000', it ought to work, it'd be O(N^2 * log N), which will be less than 1 min approximately.

### a. code

```c++
#include <algorithm>

class Solution {
public:
    int binarySearch(int left, int right, int target, vector<int>& nums){
        if(left > right || (left+right)/2 > nums.size()-1 || (left+right)/2 < 0){
            // cout<< left << "-" << right << "-" << nums.size()-1 << '\n';
            return -1;
        }
        int pivot = nums[(left+right)/2];
        if(target == pivot){
            return (left+right)/2;
        }
        else if(target < pivot){
            // cout<<"target < pivot\n";
            return binarySearch(left, (left+right)/2-1, target, nums);
        }
        else if(target > pivot){
            // cout<<"target > pivot\n";
            return binarySearch((left+right)/2+1, right, target, nums);
        }
        return -1;   
    }
    
    vector<vector<int>> threeSum(vector<int>& nums) {
        vector<vector<int>> ans;
        
        sort(nums.begin(), nums.end());
        
        int zeroStart = -1;
        int zeroLength = 0;
        int sup;
        for(int i = 0; i < nums.size(); i++){
            if(sup == nums[i]){
                zeroLength++;
            } else {
                if(zeroLength > 3){
                    nums.erase(next(nums.begin(), zeroStart), next(nums.begin(), zeroStart + zeroLength-3));
                    i -= (zeroStart + zeroLength-3);
                }
                sup = nums[i];
                zeroStart = i;
                zeroLength = 0;
            }
        }
        if(zeroLength > 3){
            nums.erase(next(nums.begin(), zeroStart), next(nums.begin(), zeroStart + zeroLength-3));
        }
                
        
        for(int i = 0; i < nums.size(); i++){
            for(int j = i+1; j < nums.size(); j++){
                // cout<< nums[i] << '-' << nums[j] << '\n';
                int targetIdx = binarySearch(0, nums.size()-1, (nums[i]+nums[j])*(-1), nums);
                if(targetIdx != -1 && targetIdx != i && targetIdx != j){
                    vector<int> tmp = {nums[i], nums[j], nums[targetIdx]};
                    sort(tmp.begin(), tmp.end());
                    ans.push_back(tmp);
                }
            }
        }
        
        // cout << binarySearch(0, nums.size()-1, 5, nums);
        
        sort(ans.begin(), ans.end());
        ans.erase(unique(ans.begin(), ans.end()), ans.end());
        
        return ans;
    }
    
};
```



So, it was quite bad. I actually won't be able to solve with this, due to the cases where they have a lot of same numbers. However, I added some codes to erase continuous numbers leaving only 3. 

| Time    | Space |
| ------- | ----- |
| 1456 ms | 68 MB |

It took about 1.4 s, which was more than I expected. 



## 2. Googled approach

So, this one is way simple. It has 1 for loop but 2 variable starting from the beginning and also from the end. It's easier to see the code.



```c++
#include <algorithm>

class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        vector<vector<int>> ans;
        
        sort(nums.begin(), nums.end());
        
        //this part
        int zeroStart = -1;
        int zeroLength = 0;
        int sup;
        for(int i = 0; i < nums.size(); i++){
            if(sup == nums[i]){
                zeroLength++;
            } else {
                if(zeroLength > 3){
                    nums.erase(next(nums.begin(), zeroStart), next(nums.begin(), zeroStart + zeroLength-3));
                    i -= (zeroStart + zeroLength-3);
                }
                sup = nums[i];
                zeroStart = i;
                zeroLength = 0;
            }
        }
        if(zeroLength > 3){
            nums.erase(next(nums.begin(), zeroStart), next(nums.begin(), zeroStart + zeroLength-3));
        }
        //till here
        
        
        for(int i = 0; i < nums.size(); i++){
            for(int j = i+1, k = nums.size()-1; j < k; ){
                if(nums[j] + nums[k] + nums[i] == 0){
                    vector<int> temp = {nums[i], nums[j], nums[k]};
                    ans.push_back(temp);
                    j++;
                    k--;
                    while(nums[j] == nums[j-1] && j < k) ++j;
                    while(nums[k] == nums[k+1] && j < k) --k;
                }
                else if(nums[j] + nums[k] + nums[i] < 0){
                    j++;
                }
                else if(nums[j] + nums[k] + nums[i] > 0){
                    k--;
                }
            }
        }
        
        sort(ans.begin(), ans.end());
        ans.erase(unique(ans.begin(), ans.end()), ans.end());
        
        return ans;
    }
};
```



This one actually worked very well. Although this one only have trouble in time complexity on the test case of [0,0,0, ... ,0,0], by adding "this part :arrow_up:" it got about 1.8 times faster. So this has about O(N^2), but nevertheless you can see quite big time differences compares to the first approach.

|                        | Time   | Space   |
| ---------------------- | ------ | ------- |
| not adding "this part" | 416 ms | 37.3 MB |
| adding "this part"     | 232 ms | 25.8 MB |

Considering root(3000) ≒ 54.8, time complexities of the first(1456 ms) and the second(232 ms) didn't have a lot of differences.

## 3. Almost-like-a-solution approach

I was very surprised how some tiny changes could make such big differences. I'll mark what's the main differences that actually shorten the time.



```c++
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        vector<vector<int>> ans;
        
        // reduce like 10 ms
        if(nums.size() < 3){
            return ans;
        }
        
        sort(nums.begin(), nums.end());
        
        for(int i = 0; i < nums.size(); i++){
            //this helped a bit(like 60 ms),  
            //eliminating continuous numbers.
            if (i > 0 && nums[i] == nums[i - 1]) continue;
            for(int j = i+1, k = nums.size()-1; j < k; ){
                // So interestingly enough, this helped a lot(like 80ms?).
                // Refering a vector takes a lot of time I guess. 
                int sum = nums[j] + nums[k] + nums[i];
                if(sum == 0){
                    // this one too. declaring in this way definitely 
                    // shorten the time.
                    ans.push_back(vector<int>{nums[i], nums[j], nums[k]});
                    j++;
                    k--;
            		// this helped a bit too(like 40 ms),
                    // eliminating continuous numbers.
                    // But if you think about it, even tho it has while loop
                    // which makes this as a 3 for loop,
                    // it still helps to get it faster. cleaver.
                    while(nums[j] == nums[j-1] && j < k) ++j;
                    while(nums[k] == nums[k+1] && j < k) --k;
                }
                else if(sum < 0){
                    j++;
                }
                else if(sum > 0){
                    k--;
                }
            }
        }
        
        return ans;
    }
};
```



And this one only gives us 76 ms. 

| Time  | Memory  |
| ----- | ------- |
| 76 ms | 20.2 MB |



## 3. Epilogue 

What I've learned from this exercise:

- referring vector/array/map actually could take more time than you think.
- 'for' loop can be used with two different variables, I just need to be more creative.
- numbers of for or while doesn't necessarily add more time. 
