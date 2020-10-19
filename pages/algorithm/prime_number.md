---
title: Sorting Prime number
last_updated: Oct 21, 2020
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: prime_number.html
summary: Just to not forget about this method, since it's quite clever. 


---

## #. Intro

I've started solving algorithm exercises again, and as doing so, I wanted to write somewhere down of what I've learned.

## 1. To figure out if a variable is prime.

This one is quite clever. 

### a. Mostly, one can start dividing from 2 to the target number, which will be *O(n)* as follows:

```c++
bool isPrime(int num){
    for(int i=2; i<num; i++){
        if(num % i == 0) return false;
    }
    return true;
}
```

However, the previous one, as you can see, is pretty slow compares to others below. 
If you think about it, dividing by numbers that is bigger than target/2. 
For example, when our target number is 90, dividing it by 46 won't get any integer value. Thereby, you can lower *N* to *N/2* with this.

### b.  Dividing till n/2 as follows:

```c++
bool isPrime(int num){
    for(int i=2; i <= num/2; i++){
        // num/2 has to be included(due to 2)
        if(num % i == 0) return false;
    }
    return true;
}
```

This still would make O(N) tho. Here is the one that I was impressed. 
You can divide it till *sqrt(target)*. 

> For *N* to not be a prime number, it should be able to be divided with 1, N, and at least two more(let's say them *p* and *q*).
>
> 1. If one of *p* or *q* is bigger than *sqrt(target)*, the other will be smaller than *sqrt(target)*. 
>
> 2. If they are both bigger than *sqrt(target)*, then *p > sqrt(target)* * *q > sqrt(target)*  would be bigger than target number itself.
>
> 3. If it's both smaller than *sqrt(target)*, then *p < sqrt(target)* * *q < sqrt(target)* would be smaller than target number itself.
>
> Only 1st scenario would meet the constraint, therefore either *p* or *q* has to be smaller than *sqrt(target)* 

And this will give *O(sqrt(N))* of complexity, which is a huge improvement.

### c.  Dividing till sqrt(N):

```c++
bool isPrime(int num){
    for(int i=2; i*i < num; i++){
        if(num % i == 0) return false;
    }
    return true;
}
```



## 2. To figure out if multiple variables are prime.

It's convenient to have a sorted array in this situation, which you can make it as such:

```c++
bool prime[N]= {false,};
int count = 0;

for(int i = 2; i <= N; i++){
	if(prime[i] == false){
		for(int j = i+i; j <= N; j += i){
			prime[j] = true;
		}
	}
}
```

This code won't work due to 'N' in C++, you have to statically assign number :pensive:



## 3. Epilogue 

This really gave me thoughts on how much it could be easy if you know some mathematic stuff..
