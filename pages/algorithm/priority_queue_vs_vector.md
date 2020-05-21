---
title: Priority Queue Vs Vector with Sort
last_updated: May 21, 2020
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: priority_queue_vs_vector.html
summary: I haven't used much of Priority queue, but here I wanted to compare it to vector which is easier method I'd say, and see if it's actually worth using priority queue. 

---

## #. Motivation

So, last few days, I've been thinking about my job and title. I always introduce myself as a software engineer. And it occurs to me that I could be refer myself as a developer, or programmer. and yet, I still use the term of software engineer not completely knowing what it meant. So I looked it up, and main differences were this;
<br />
- Programmer: umbrella term for developer or software engineer.
- Developer: one who develop code. It's more like describing your thoughts into a coding. The result would not necessarily be efficient nor optimal.
- Software engineer: Developer + One who optimizes code and make the code efficient as time and space wise. That means, one has to have enough **knowledge of data structure, algorithm, math, and how to combine them to make the best efficiency.**
<br />
Of course this is my understanding after some researches, and it might not be accurate. 

<br />
<br />
What my company does is, making a website or app for other companies, so called SI(System Integration). In those process, one has to understand the domain first to see what kind of constraints that we might have. Also, **it's important to understand the differences of many different database, programming languages**, and so on. That is because, to make a website, traditionally, we need to have 1. database, 2. server(back-end), and 3. web(front-end). This means, you will use at least 3 different languages. 

<br />
<br />
To summarize, 

1. Understanding domain knowledge,
2. Choosing frameworks or languages for those 3 different layer(database, server, and web)

would be important. **When one chooses frameworks or languages, one has to consider many aspects, mostly if those would be well connected, and also make a good fit with constraints of domain knowledge.**
<br />
<br />
So as you can assume, what we do is software engineering. However, my position is developing those concepts into codes for now. As time goes by, I will start designing some parts of project deciding which database and frameworks we'll be using and so on. But right now, my job is making more efficient code with, and I think I'm not really doing a great job. So far, I have been doing great on making things done, but without focusing on efficiency nor optimization. Therefore, I started to think that I want to be better at those.  

<br />
<br />
That's why I started to compare Priority queue and Sorted vector to see when to use what.

<br />
<br />
## 1. Experiment

```c++
#include<iostream>
#include<vector>
#include<queue>
#include<time.h>

using namespace std;

//	when parameters are typed as const, it cannot be changed
bool sortOption1(const int a, const int b) {
	return a > b;
}

double start, endTime;

int main() {

	printf("1. priority queue with 'Greater' comparator\n");

	start = clock();

	priority_queue<int, vector<int>, greater<int>> pq;

	for (int i = 0; i < 100; i++) {
		pq.push(9);
		pq.push(7);
		pq.push(5);
		pq.push(3);
		pq.push(1);
		pq.push(2);
		pq.push(4);
		pq.push(6);
		pq.push(8);
	}
	endTime = clock();

	printf("    execution time : %10f\n", endTime - start);

	while (!pq.empty()) {
		printf("%4d->", pq.top());
		pq.pop();
	}


	printf("\n-------------------------------------------\n");
	printf("2. priority queue with 'less' comparator\n");

	start = clock();

	priority_queue<int, vector<int>, less<int>> pq2;

	for (int i = 0; i < 100; i++) {
		pq2.push(9);
		pq2.push(7);
		pq2.push(5);
		pq2.push(3);
		pq2.push(1);
		pq2.push(2);
		pq2.push(4);
		pq2.push(6);
		pq2.push(8);
	}

	endTime = clock();

	printf("    execution time : %10f\n", endTime - start);

	while (!pq2.empty()) {
		printf("%4d->", pq2.top());
		pq2.pop();
	}

	printf("\n-------------------------------------------\n");
	printf("3. vector with sort only once at the end\n");

	start = clock();

	vector<int> pq_v;

	for (int i = 0; i < 100; i++) {
		pq_v.push_back(9);
		pq_v.push_back(7);
		pq_v.push_back(5);
		pq_v.push_back(3);
		pq_v.push_back(1);
		pq_v.push_back(2);
		pq_v.push_back(4);
		pq_v.push_back(6);
		pq_v.push_back(8);
	}

	sort(pq_v.begin(), pq_v.end());


	endTime = clock();

	printf("    execution time : %10f\n", endTime - start);

	for (auto i : pq_v) {
		printf("%4d->", i);
	}


	printf("\n-------------------------------------------\n");
	printf("4. vector with sort option only once at the end\n");

	start = clock();

	vector<int> pq_v2;

	for (int i = 0; i < 100; i++) {
		pq_v2.push_back(9);
		pq_v2.push_back(7);
		pq_v2.push_back(5);
		pq_v2.push_back(3);
		pq_v2.push_back(1);
		pq_v2.push_back(2);
		pq_v2.push_back(4);
		pq_v2.push_back(6);
		pq_v2.push_back(8);
	}

	sort(pq_v2.begin(), pq_v2.end(), sortOption1);


	endTime = clock();

	printf("    execution time : %10f\n", endTime - start);

	for (auto i : pq_v2) {
		printf("%4d->", i);
	}


	printf("\n-------------------------------------------\n");
	printf("5. vector with sort as PQ\n");

	start = clock();

	vector<int> pq_v3;

	for (int i = 0; i < 100; i++) {
		pq_v3.push_back(9);
		sort(pq_v3.begin(), pq_v3.end(), sortOption1);
		pq_v3.push_back(7);
		sort(pq_v3.begin(), pq_v3.end(), sortOption1);
		pq_v3.push_back(5);
		sort(pq_v3.begin(), pq_v3.end(), sortOption1);
		pq_v3.push_back(3);
		sort(pq_v3.begin(), pq_v3.end(), sortOption1);
		pq_v3.push_back(1);
		sort(pq_v3.begin(), pq_v3.end(), sortOption1);
		pq_v3.push_back(2);
		sort(pq_v3.begin(), pq_v3.end(), sortOption1);
		pq_v3.push_back(4);
		sort(pq_v3.begin(), pq_v3.end(), sortOption1);
		pq_v3.push_back(6);
		sort(pq_v3.begin(), pq_v3.end(), sortOption1);
		pq_v3.push_back(8);
		sort(pq_v3.begin(), pq_v3.end(), sortOption1);
	}

	endTime = clock();

	printf("    execution time : %10f\n", endTime - start);

	for (auto i : pq_v3) {
		printf("%4d->", i);
	}

	
	printf("\n-------------------------------------------");
	printf("-------------------------------------------\n");
	printf("Verdict: \n");
	printf("Vector sort is twice faster than priority queue only when you sort it after pushing all elements.,\n");
	printf("However, when you have to sort everytime you push elements, pririty queue shows far better efficiency.\n");
	printf("Therefore, it depends on usage, when you have to sort elements as you push them then use Priority queue.\n");
	printf("But all you need is one sort after pushing all the elements, then vector is better.\n");

	/*
Vector sort is twice faster than priority queue only when you sort it after pushing all elements. However, when you have to sort everytime you push elements, pririty queue shows far better efficiency. Therefore, it depends on usage, when you have to sort elements as you push them then use Priority queue. But all you need is one sort after pushing all the elements, then vector is better.
	*/


	return 0;
}
```
<br />
<br />
Result : 
```
1. priority queue with 'Greater' comparator
    execution time :   3.000000
-------------------------------------------
2. priority queue with 'less' comparator
    execution time :   3.000000
-------------------------------------------
3. vector with sort only once at the end
    execution time :   2.000000
-------------------------------------------
4. vector with sort option only once at the end
    execution time :   2.000000
-------------------------------------------
5. vector with sort as PQ
    execution time : 164.000000
-------------------------------------------

```


## 2. Epilogue 

As the verdict, 

1. When you have to sort whenever you push elements : priority queue is way efficient. **(result 1 vs 6)**
2. However, all you need is 1 time sort at the end: vector is better. **(result 1 vs 3)**
<br />
<br />
Interesting result, but the result seems pretty predictable since they were made for different purposes.
