---
title: Permutation
last_updated: Dec 1, 2020
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: permutation.html
summary: summary of permutation algorithm


---

## Permutation

refer by;
[exercise problem](https://leetcode.com/problems/permutations/)



### Using flag array

- by using boolean array, you can make permutations of integers.

```c++
void permu(vector<int> cur, int flag[10], int N){
    if(cur.size() == N) return;
    
    for(int i = 0; i < 10; i++){
        if(!flag[i]){
            flag[i] = true;
            cur.push_back(i);
            
            permu(cur, flag);
            
            cur.pop_back();
            flag[i] = false;
        }
    }
}
```



### Using vector arrays

- Or you could just use vector right away.

```c++
void permu(vector<int> cur, vector<int> remain){
    if(remain.size() == 0) return;
    
    for(int i = 0; i < remain.size(); i++){
        cur.push_back(remain(i));
        remain.erase(remain.begin()+i, 1);
        
        permu(cur, remain);
        
        remain.insert(remain.begin()+i, cur.back());
        cur.pop_back();
    }
}
```



- but this one takes a lot of time since erase and insert takes O(N). Here's a better one

```c++
void permu(vector<int> nums, int curIdx){
    if(curIdx == nums.size()) return;
    
    // try to write it down how it works, it helps
    // notice that 'i' is not 'curIdx+1'
    for(int i = curIdx; i < remain.size(); i++){
        swap(nums[i], nums[curIdx]);
        permu(nums, curIdx+1);
        swap(nums[curIdx], nums[i]);
    }
}
```



### next_permutation

- And this one is just insane. C++ provides a function through a library \<algorithm\>. It's just little tricky to use. (prev_permutation function also exists.)
- More explanation is [here](http://www.cplusplus.com/reference/algorithm/next_permutation/)

```c++
// ref : http://www.cplusplus.com/reference/algorithm/next_permutation/

// next_permutation example
#include <iostream>     // std::cout
#include <algorithm>    // std::next_permutation, std::sort

using namespace std;

int main () {
  int myints[] = {1,2,3};

    //sort is needed.
  sort (myints,myints+3);

  cout << "The 3! possible permutations with 3 elements:\n";
  do {
    cout << myints[0] << ' ' << myints[1] << ' ' << myints[2] << '\n';
  } while ( next_permutation(myints,myints+3) );

  cout << "After loop: " << myints[0] << ' ' << myints[1] << ' ' << myints[2] << '\n';

  return 0;
}
```

