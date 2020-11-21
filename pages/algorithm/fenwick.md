---
title: Fenwick Tree
last_updated: Nov 21, 2020
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: fenwick.html
summary: summarization of fenwick tree


---

## Fenwick Tree

refer by;
[1](https://minusi.tistory.com/entry/%ED%8E%9C%EC%9C%85-%ED%8A%B8%EB%A6%ACFenwick-Tree-Binary-Indexed-Tree)
[2](https://uldada.tistory.com/5)

```c++
/******************************************************************************

                              Online C++ Compiler.
               Code, Compile, Run and Debug C++ program online.
Write your code in this editor and press "Run" button to compile and execute it.

*******************************************************************************/

#include <iostream>
#include <vector>
#include <bitset>

using namespace std;

vector<int> arr = {1,2,3,4,5,6,7,8,9,10};
vector<int> tree;
    
int sum(int i){
    int ans = 0;
    while (i > 0){
        ans += tree[i];
        i -= i%-i;
    }
    return ans;
}

void update(int index, int diff){
    while(index < tree.size()){
        tree[index] += diff;
        index += (index & -index);
    }
}

void init(){
    tree.resize(arr.size() + 1);
    tree[0] = 0;
    for (int i = 1; i <= arr.size(); ++i)
        update(i, arr[i-1]);
}

int main()
{
    init();
    for(auto i : tree){
        cout << i << ' ';
    }

    return 0;
}
```
