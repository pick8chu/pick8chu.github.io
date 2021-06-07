---
title: Find the unknown digit
last_updated: Jun 7th, 2021
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: find_the_unknown_digit.html
summary: To compare my method and someone else's. 

---

## #. Problem

[Find the unknown digit](https://www.codewars.com/kata/546d15cebed2e10334000ed9/javascript)



## 1. My approach

Brute-force, with eval.


```javascript
/* idea:
    make it as an conditional expression, and eval it, 
    so that it would get true or false.
      
    exceptional cases that made it difficult was, 0. 00 was not acceptable, 
    and also 0# too. therefore, I had to make sure if the expression made sense with 0s,
    if not, it can't be 0.
  
    I could've tried to start from 9 to 0, but since the answer have to be 
    the smallest possible number, starting from 0 was necessary.
    
    what I came up with, was to examine 
    if it's acceptable expression when 0 was assigned.
    1. having 0 in the very front of numbers is not acceptable.
      1-1. however, if that 0 is the only digit, then that's acceptable.  
*/

function solveExpression(exp) {
  console.log('------------------new------------------');
  console.log(exp);
  let op = '+-*=';
  let arr = []
  //  making 'exp' to an actual conditional expression
  exp = exp.replace('=','==');
  //  '--' is not acceptable on 'eval', so parenthesize them.
  if((idx = exp.indexOf('--')) !== -1){
    let idx2 = exp.indexOf('==');
    let temp = exp.split``;
    temp.splice(idx+1, 0, '(');
    temp.splice(idx2+1, 0, ')');
    exp = temp.join``;
  }
  
  //  replacing '?' to 0 -> 9
  console.log(exp);
  for(let i = 0; i <= 9; i++){
    let temp = exp.replace(/\?/g, i);
    
    //  when it's 0, there're more things to check
    if(i === 0){
      let flag = false;
      for(let j = 0; j < temp.length; j++){
        console.log('inner',j);
        if(temp[j] == 0 && (j-1 < 0 || op.indexOf(temp[j-1]) !== -1) && !(j+1 >= temp.length || op.indexOf(temp[j+1]) !== -1)){
          flag = true;
          break;
        }
      }
      console.log('flag',flag);
      if(flag) continue;
    }
    
    //  check if the new expression is correct.
    console.log(temp);
    if(exp.indexOf(String(i)) === -1 && eval(temp)){
      console.log('answer', i);
      return i;
    }
  }
  
  //  if all failed, nothing is possible.
  return -1;
}
```





## 2. [Solutions](https://www.codewars.com/kata/546d15cebed2e10334000ed9/solutions/javascript)

### a. Best practice



```javascript
function solveExpression(exp) {
  exp = exp.replace('=','==').replace('--','+');
  for(var i = 0; i < 10; i++){
    if(eval(exp.replace(/\?/g,i)) && !exp.includes(i)){
        if(!/^00+$/.test(exp.replace(/\?/g,i).split('==')[1]))  return i;
    }
  }
  return -1;
}
```



## 3. Epilogue 

What I've learned from this exercise:

- *replace('--','+')* was quite nice, and regular expression was pretty good too. Need to learn more of regular expressions.
