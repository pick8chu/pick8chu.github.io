---
title: Assigning Meeting Rooms
last_updated: May 11, 2020
keywords: android, app, google map, navigation drawer, dynamical menu
sidebar: mydoc_sidebar
comments: true
summary: "Original Problem : https://www.acmicpc.net/problem/1931"
permalink: algorithm-greedy-baekjoon_1931.html

---

## #. Problem

Original Problem : https://www.acmicpc.net/problem/1931

## 1. Solution

So, we will make a vector of pair of int, int which would be, *vector<pair<int,int>> list*, and store all the inputs. So the solution logic that I came out is, below. I figure, it's way easier just look into the source code.

```c++
#include<cstdio>
#include<algorithm>
#include<vector>

using namespace std;

vector<pair<int, int>> list;

int N;

bool sortWithSecondPair(const pair<int,int> &a, const pair<int,int> &b){
    // 3. first priority is the second pairs,
    //		and next is the first pairs.
	if(a.second == b.second){
		return a.first < b.first;
	}
	else {
		return a.second < b.second;
	}
}

int greedy(vector<pair<int,int>> &list){
    // 2. Sort the *list*
	sort(list.begin(), list.end(), sortWithSecondPair);
	
	pair<int,int> cur(list[0].first, list[0].second);
	int ans = 1;
	
    // 4. Since it's sorted, N for loop would work.
	for(int i = 1; i < list.size(); i++){
		if(list[i].first >= cur.second){
			cur.first = list[i].first;
			cur.second = list[i].second;
			ans++;
		}
	}
	
	return ans;
}

int main(){
    // 1. Put all the pairs into the *list*.
	scanf("%d", &N);
	int tempA, tempB;
	for(int i = 0; i < N; i++){
		scanf("%d %d", &tempA, &tempB);
		pair<int, int> temp(tempA, tempB);
		list.push_back(temp);
	}
	
	printf("%d", greedy(list));
		
	return 0;
}
```



## 2. Things that I've learned

### a. Sort is super useful.

Obviously it could've been O(N^2) problem it it wasn't for sorting. Or, I'm sure there's better way with better efficiency, but sorting would make things way easier with your own configurations. 

### b. For the third parameter of the sorting function.

1. Parameters has to be ***const*** type, which I wasn't sure. The return type is also has to be ***bool***.

2. When you use it, it doesn't need a parenthesis. 

   | O                                                        | X                                                          |
   | -------------------------------------------------------- | ---------------------------------------------------------- |
   | sort(list.begin(), list.end(), ***sortWithSecondPair***) | sort(list.begin(), list.end(), ***sortWithSecondPair()***) |





## 2. Epilogue 

It was interesting to see how sorting could make things more efficient that I thought.
