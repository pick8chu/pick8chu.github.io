---
title: Minimum Time To Complete Trips
last_updated: Mar 07, 2023
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: minimum-time-to-complete-trips.html
---

## #. Problem

[Go to the problem on leetcode](https://leetcode.com/problems/minimum-time-to-complete-trips/)


## 1. My approach

Considering the constraints, time.length <= 10^5, totalTrips <= 10^5, brute force approach would not be possible. <br/> I thought of using *map* with *binary search* but it didn't work very well. <br/> The main reason for this is that it needed slight changes of the binary search. Normally binary search would be like below.

```cpp
int binary_search(arr, target){
    mid = (left + right) / 2;
    
    if(arr[mid] == target){
        return mid;
    } else if(arr[mid] < target){
        left = mid + 1;
    } else {
        right = mid - 1;
    }
}
```

But in this problem, because of the constraints, it should be like below.

```cpp
int binary_search(arr, target){
    mid = (left + right) / 2;
    
    if(arr[mid] == target){
        return mid;
    } else if(arr[mid] < target){
        left = mid + 1;
    } else {
        // This is the only difference.
        right = mid;
    }
}
```


So I tried it out, but I didn't solve it due to time limit, which I am not sure why I am getting those. Myabe due to the Map structure? but it'd only take 1e5 at worst. <br/>

```typescript
function minimumTime(time: number[], totalTrips: number): number {
    const m = {};
    let left = 1, right = 1e5 * 1e7;
    time.forEach(el => m[el] = m[el]? m[el]+1:1);

    let currentTrips = 0;
    let mid = Math.floor((left + right)/2);

    while((currentTrips = getT(m, mid)) !== totalTrips){
        // console.log(left, right, mid, currentTrips);

        if(currentTrips > totalTrips){
            right = mid;
        } else {
            left = mid+1;
        }
        mid = Math.floor((left + right)/2);

        if(left >= right){
            // console.log('left >= right', left, right);
            return Math.max(left, right);
        }
    }

    while(currentTrips === getT(m, --mid));

    return mid+1;
};

const getT = (m: {}, currentTime: number): number => {
    let res = 0;
    for(const tripTime in m){
        const no = Number(tripTime)
        res += Math.floor(currentTime/no) * m[tripTime];
    }
    return res;
}
```


## 2. Solution

Solution is visible [here](https://leetcode.com/problems/minimum-time-to-complete-trips/editorial/), so I won't be posting it here. <br/> I will try to solve this once more when I almost forget about it. <br/>


## 3. Epilogue 

What I've learned from this exercise:

- tiny tweaks can make a huge difference.