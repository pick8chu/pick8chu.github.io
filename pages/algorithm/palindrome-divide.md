---
title: baekjoon - 1509 - 펠린드롬 분할 - DP
last_updated: Jan 18, 2020
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: palindrome-divide.html
summary: MST! 

---

## #. Problem

[Website](https://www.acmicpc.net/problem/1509)



## 1. My approach

-



## 2. Solution



```c++
#include<iostream>
#include<algorithm>

using namespace std;

int ans = 0;
// is it Panlindrome, string [from][to]?
bool isP[2501][2501];
int dp[2501];

int main(){
	string s;
	cin >> s;
	
	// 자리수 맞추기
	s.insert(0," ");
	
	// 길이가 1인 자기자신은 palindrome
	for(int i = 1; i < s.size(); i++) isP[i][i] = true;

	// 길이가 2 string들에 대하여 palindrome check
	for(int i = 1; i < s.size(); i++) {
		if(s[i] == s[i+1]) isP[i][i+1] = true;
	}
	
	// 길이가 3 string들에 대하여 palindrome check
	// i 가 안에 있는 이유는, 길이가 3인것들에 대해 한바뀌 돌아야지
	// 길이가 4인것들에 대해 볼 때, isP[a][b](b-a=3) 이 업데이트되어있기때문
	for(int j = 2; j < s.size(); j++){
        for(int i = 1; i < s.size(); i++){
			if(s[i] == s[j] && isP[i+1][j-1]) isP[i][j] = true;
		}
	}
	
	for (int i = 1; i < s.size(); i++){
		dp[i] = 2147483647;
		for (int j = 1; j <= i; j++){
			if (isP[j][i]) dp[i] = min(dp[i], dp[j - 1] + 1);
		}
	}
	
	cout << dp[s.size()-1];
	
    // while(true){
    //     int a = 0;
    //     int b = 0;
    //     scanf("%d%d",&a,&b);
        
    //     cout<< isP[a][b] << '\n';
    // }

	return 0;
}
```



| Time - O(N^2) | Space - O(N^2) |
| ------------- | -------------- |
|               |                |



## 3. Epilogue 

What I've learned from this exercise:

- There's always a way.
