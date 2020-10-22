---
title: Etc.
last_updated: Oct 20, 2020
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: algorithm-etc.html
summary: Every other stuff -too tiny to make a new page- 


---

## C++

### Vector

#### 1. useful functions

\<algorithm>

- std::sort(v.begin(), v.end())
- std::unique(v.begin(), v.end())
- it moves duplicated elements on the back, and returns the number of the first duplicated elements.
- ! The vector has to be sorted.
- For example, { 0, 1, 1, 2, 2, 2, 3} => {0, 1, 2, 3, 1, 2, 2, 3} and returns 5. Therefore, you could use it as such:

```c++
    std::sort(v.begin(), v.end());
    v.resize(std::unique(v.begin(), v.end()) - v.begin());
```

- v1 == v2 is true when v1 and v2 has the same elements in the same orders.

  - &v1 == &v2 or *v1 == *v2 won't be true tho.

    

### Passing parameters

- & - pass by references
  - It doesn't make a copy of the value, but give you the real value. Thereby, if you change this, the original data(which is the same) will be changed.

```c++
void foo(int& value) {
    value = 6; 
} 
int main() {
    int value = 5;
    // 6
    cout << "value = " << value << '\n';
    foo(value);
    // 5
    cout << "value = " << value << '\n';
    return 0;
}
```



- const & - pass by references
  - by doing so, you can implicitly show that it's not changeable, and also you can reduce time of making a copy.
- \* - pass by address
  - literally.

```c++
void foo(int* ptr) {
    *ptr = 6; 
} 
int main() {
    int value = 5; 
    // 5
    std::cout << "value = " << value << '\n'; 
    foo(&value);
    // 6
    std::cout << "value = " << value << '\n'; 
    return 0; 
}
```
