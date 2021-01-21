---
title: javascript
last_updated: Jan 21, 2021
keywords: study
sidebar: mydoc_sidebar
comments: true
permalink: javascript.html

---

## #. Closure



To use variables outside of the functions in inside the functions. It's like wrapping itself with outer function.

Therefore, the variables inside would be private variables, and encapsulated.

### example 1

```javascript

    // Q.
    for(var i = 0; i < 10; i++){
        setTimeout(function(){
            console.log(i);
        }, 100);
    };
    
    
    // 1. var -> let
    for(let i = 0; i < 10; i++){
        setTimeout(function(){
            console.log(i);
        }, 100);
    };
    
	// 2. function
    for(var i = 0 ; i < 10; i++){
      (function(x){
        console.log("outer > i=", i, "x=", x)
        setTimeout(function(){
          console.log("inner  > i=", i,"x=", x)
        }, 100);
      })(i);
    }
    
    // 3. arrow function
    for(var i = 0; i < 10; i++){
        ((x)=>{
            setTimeout(function(){
                console.log(x);
            }, 100);
        })(i)
    };
```



### example 2

referring from here : https://meetup.toast.com/posts/90

```javascript
var counter = {
    _count: 0,
    count: function() {
        return this._count += 1;
    }
}

console.log(counter.count()); // 1
console.log(counter.count()); // 2
counter._count = 4; // <= you can change its value

///////////////////////////////////////////////////////

function counterFactory() {
    // this part is only excuted once when declared
    var _count = 0;

    return function() {
        _count += 1;

        return _count;
    };
}

// counter = function() {_count+=1}
// cause it get return fuction of counterFactory
var counter = counterFactory();
var counter2 = counterFactory();

console.log(counter()); //1
console.log(counter()); //2
console.log(counter2()); //1

// you can't access _count variable

```



### example 3

```javascript
//seven(times(five())); // must return 35
//four(plus(nine())); // must return 13
//eight(minus(three())); // must return 5
//six(dividedBy(two())); // must return 3

//function n uses closure, 
var n = function(digit) {
    //to uses values from this scope
  return function(op) {
      //in here
    return op ? op(digit) : digit;
  }
};
var zero = n(0);
var one = n(1);
var two = n(2);
var three = n(3);
var four = n(4);
var five = n(5);
var six = n(6);
var seven = n(7);
var eight = n(8);
var nine = n(9);

function plus(r) { return function(l) { return l + r; }; }
function minus(r) { return function(l) { return l - r; }; }
function times(r) { return function(l) { return l * r; }; }
function dividedBy(r) { return function(l) { return l / r; }; }
```







## #. [Hoisting](https://www.w3schools.com/js/js_hoisting.asp)

in Javascript, it declares all the variables to the top of the scope.



### example

```javascript
{
    x = 5; // Assign 5 to x

    elem = document.getElementById("demo"); // Find an element
    elem.innerHTML = x;                     // Display x in the element

    var x; // Declare x
}

//this is the same result as below:

{
    var x; // Declare x
    x = 5; // Assign 5 to x

    elem = document.getElementById("demo"); // Find an element
    elem.innerHTML = x;                     // Display x in the element
}
```





## #. Event bubbling

event.stopPropagation...





## #. Arrow function with This scope
