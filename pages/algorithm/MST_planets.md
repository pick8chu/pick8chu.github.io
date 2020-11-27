---
title: baekjoon - 2887 MST
last_updated: Nov 27, 2020
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: MST_planets.html
summary: MST! 

---

## #. Problem

MST : [Website](https://www.acmicpc.net/problem/2887)

Referred from [https://hsin.hr/coci/archive/2009_2010/](https://hsin.hr/coci/archive/2009_2010/contest7_tasks.pdf) No. 4

Fourth Great and Bountiful Human Empire is developing a transconduit tunnel network connecting all it's planets. The Empire consists of N planets, represented as points in the 3D space. The cost of forming a transconduit tunnel between planets A and B is: TunnelCost[A,B] = min{ |xA-xB|, |yA-yB|, |zA-zB| } where (xA, yA, zA) are 3D coordinates of planet A, and (xB, yB, zB) are coordinates of planet B. The Empire needs to build exactly N - 1 tunnels in order to fully connect all planets, either by direct links or by chain of links. You need to come up with the lowest possible cost of successfully completing this project. INPUT The first line of input contains one integer N (1 ≤ N ≤ 100 000), number of planets. Next N lines contain exactly 3 integers each. All integers are between -109 and 109 inclusive. Each line contains the x, y, and z coordinate of one planet (in order). No two planets will occupy the exact same point in space. OUTPUT The first and only line of output should contain the minimal cost of forming the network of tunnels.



## 1. My approach

To use MST, you'll have to have information of every possible edges, which, in this case, will take O(N^3). So, my approach was failed by time limit.



## 2. Solution

Mind blowing. Learned a lot. Because of "TunnelCost[A,B] = min{ |xA-xB|, |yA-yB|, |zA-zB| }", you can think of x, y and z as a 3 separated edges. By sorting them respectively and combining all of sorted X, Y and Z edges into one vector and sorting it again by it's weight, you can get all sorted edge vector.



That's one thing. Union/Find is also quite confusing in this problem, but by numbering those vertices, you can use plain array for the Union/Find algorithm.

```c++
#include <iostream>
#include <algorithm>
#include <vector>
#include <math.h>

using namespace std;

struct Edge {
	int from, to, cost;
	Edge(int _from, int _to, int _cost)
		:from(_from), to(_to), cost(_cost) {}
};

struct Vert {
	int x, y, z, idx;
	Vert(int a, int b, int c, int _idx) :x(a), y(b), z(c), idx(_idx) {}
};

int N, a, b, c, ans, set[100001];
vector<Edge> e;
vector<Vert> v;


int group(int i) {
	return (set[i] == 0)? i : set[i] = group(set[i]);
}

void join(int i, int j) {
	int a = group(i);
	int b = group(j);

	if (a != b) set[a] = b;
}

void krusical() {
	int selected = 0;
	for (auto item : e) {
		if (group(item.from) != group(item.to)) {
			join(item.from, item.to);
			ans += item.cost;
			selected++;
		}
		else continue;
		if (selected == N - 1) break;
	}

	if (selected != N - 1) {
		printf("N/A");
	}
}

void pushEdges() {
	sort(v.begin(), v.end(), [](const Vert& a, const Vert& b) {
		return a.x < b.x;
		});

	for (int i = 1; i < v.size(); i++) {
		e.push_back(Edge(v[i].idx, v[i - 1].idx, abs(v[i].x - v[i - 1].x)));
	}

	sort(v.begin(), v.end(), [](const Vert& a, const Vert& b) {
		return a.y < b.y;
		});

	for (int i = 1; i < v.size(); i++) {
		e.push_back(Edge(v[i].idx, v[i - 1].idx, abs(v[i].y - v[i - 1].y)));
	}

	sort(v.begin(), v.end(), [](const Vert& a, const Vert& b) {
		return a.z < b.z;
		});

	for (int i = 1; i < v.size(); i++) {
		e.push_back(Edge(v[i].idx, v[i - 1].idx, abs(v[i].z - v[i - 1].z)));
	}

	sort(e.begin(), e.end(), [](const Edge& a, const Edge& b) {
		return a.cost < b.cost;
		});
}

int main() {
	scanf("%d", &N);

	for (int i = 1; i <= N; i++) {
		scanf("%d%d%d", &a, &b, &c);
		v.push_back(Vert(a, b, c, i));
	}

	pushEdges();
	krusical();

	printf("%d", ans);

	return 0;
}
```



| Time - O(3 N log N + E log E + E) | Space - O(A lot?) |
| --------------------------------- | ----------------- |
| 120 ms                            | 15 MB             |



## 3. Epilogue 

What I've learned from this exercise:

- There's always a way.
