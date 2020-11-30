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

\<algorithm\>

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

    

### Thoughts about data type Char

- So, as everyone knows, size of char is 8 bits = 1 byte. However, if you look at an ascii table, it only has 0 to 127, so total 128, which is only 2^7. I honestly don't know why it uses 1 byte, maybe it's just easy to remember that way. (Please make a comment if you know the history.) Anyways, this means -128 to -1 doesn't mean anything while you are coding (maybe it has differnt meaning in it but idk atm). When you wanna encode your string, you could use it like this:

```c++
#include <iostream>
#include <vector>
#include <string>

using namespace std;

int main()
{
    vector<char> v;
    
    string test = "this is a test";
    string ans;
    
    for(const char& el : test){
        ans += el*-1;
    }
    
    cout << ans <<'\n';
    
    for(char& el : ans){
        el *= -1;
    }

    cout << ans <<'\n';

    return 0;
}

```

output is, 

```
��������������
this is a test
```


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

### declaraion of functions in parameter

[](int a, int b){
    content;
}

```c++
sort(arr.begin(), arr.end(), [](const int& a, const int& b){
    return b < a;
});
```

