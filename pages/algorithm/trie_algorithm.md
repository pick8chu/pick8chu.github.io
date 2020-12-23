---
title: Trie Algorithm (for Strings)
last_updated: Dec 23, 2020
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: trie_algorithm.html
summary: summary of Trie algorithm


---

## Trie Algorithm

refer by;
1. [Korean Explanation](https://hwan-shell.tistory.com/133)
2. [Korean Explanation2](https://twpower.github.io/187-trie-concept-and-basic-problem)
3. [geeksforgeeks](https://www.geeksforgeeks.org/trie-insert-and-search/)


## Implementation

- So, it has 2 different ways of doing it, one is inserting recursively(in a way), and the other is iteratively.

### Recursive


```c++
struct Trie{

	bool is_terminal; // this represents end of string
	Trie* children[ALPHABETS];	// ALPHABETS = int 26

	// Constructor
	Trie(): is_terminal(false){
		memset(children, 0, sizeof(children));
	}

	// Delete all children
	~Trie(){
		for(int i = 0; i < ALPHABETS; ++i){
			if(children[i])
				delete children[i];
		}
	}

	void insert(const char* key){
		if(*key == '\0'){	// end of string
			is_terminal = true;
		}
		else{
			int index = char_to_index(*key);

			if(children[index] == 0)
				children[index] = new Trie();
			//	this is recursive, you are calling
			//	your children's function. 
			//	'key + 1' means just next charater from String.
			//	for examples, char* a = "abc", a+1 => "bc"
			children[index]->insert(key + 1);
		}
	}

	Trie* find(const char * key){
		if(*key == 0){
			return this;
		}

		//char_to_index = [](char* alpha){return *alpha-'a';}
		int index = char_to_index(*key);
		//children has initialized as 0
		if(children[index] == 0){
			return NULL;
		}

		return children[index]->find(key+1);
	}

	bool string_exist(const char * key){
		if(*key == 0 && is_terminal){
			return true;
		}

		int index = char_to_index(*key);
		if(children[index] == 0){
			return false;
		}
		return children[index]->string_exist(key + 1);
	}

};
```



### Iterative

- This is from [Geeks for geeks](https://www.geeksforgeeks.org/trie-insert-and-search/)

```c++
// C++ implementation of search and insert 
// operations on Trie 
#include <bits/stdc++.h> 
using namespace std; 
  
const int ALPHABET_SIZE = 26; 
  
// trie node 
struct TrieNode 
{ 
    struct TrieNode *children[ALPHABET_SIZE]; 
  
    // isEndOfWord is true if the node represents 
    // end of a word 
    bool isEndOfWord; 
}; 
  
// Returns new trie node (initialized to NULLs) 
struct TrieNode *getNode(void) 
{ 
    struct TrieNode *pNode =  new TrieNode; 
  
    pNode->isEndOfWord = false; 
  
    for (int i = 0; i < ALPHABET_SIZE; i++) 
        pNode->children[i] = NULL; 
  
    return pNode; 
} 
  
// If not present, inserts key into trie 
// If the key is prefix of trie node, just 
// marks leaf node 
void insert(struct TrieNode *root, string key) 
{ 
    struct TrieNode *pCrawl = root; 
  
    for (int i = 0; i < key.length(); i++) 
    { 
        int index = key[i] - 'a'; 
        if (!pCrawl->children[index]) 
            pCrawl->children[index] = getNode(); 
  
        pCrawl = pCrawl->children[index]; 
    } 
  
    // mark last node as leaf 
    pCrawl->isEndOfWord = true; 
} 
  
// Returns true if key presents in trie, else 
// false 
bool search(struct TrieNode *root, string key) 
{ 
    struct TrieNode *pCrawl = root; 
  
    for (int i = 0; i < key.length(); i++) 
    { 
        int index = key[i] - 'a'; 
        if (!pCrawl->children[index]) 
            return false; 
  
        pCrawl = pCrawl->children[index]; 
    } 
  
    return (pCrawl != NULL && pCrawl->isEndOfWord); 
} 
  
// Driver 
int main() 
{ 
    // Input keys (use only 'a' through 'z' 
    // and lower case) 
    string keys[] = {"the", "a", "there", 
                    "answer", "any", "by", 
                     "bye", "their" }; 
    int n = sizeof(keys)/sizeof(keys[0]); 
  
    struct TrieNode *root = getNode(); 
  
    // Construct trie 
    for (int i = 0; i < n; i++) 
        insert(root, keys[i]); 
  
    // Search for different keys 
    search(root, "the")? cout << "Yes\n" : 
                         cout << "No\n"; 
    search(root, "these")? cout << "Yes\n" : 
                           cout << "No\n"; 
    return 0; 
} 
```
