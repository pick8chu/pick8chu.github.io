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

However, it may cause memory issues if you didn't clear the memory after using it. You can assign null or something else to refresh the assigned memory. 

it's tightly connected to ['curring'](https://okky.kr/article/535403).

### example 1

[check this one too](https://myhappyman.tistory.com/20)

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
    //var은 재정의가 가능하기때문에 1~10까지 가면서 마지막 10으로 재정의가 되는것.
    //let은 재정의가 불가능하기때문에 처음 값(i=1, 2, 3 ...)으로 가지고있는것.
    
    // explanation is here
    // https://stackoverflow.com/questions/31285911/why-let-and-var-bindings-behave-differently-using-settimeout-function
    
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
    
    // 4. async await (this is the right way)
    	function resolveAfter2Seconds(x) {
	  return new Promise(resolve => {
	    setTimeout(() => {
	      resolve(x);
	    }, 500);
	  });
	}

	async function f1() {
	    for(let i = 0; i < 10; i++){
	      console.log(await resolveAfter2Seconds(i)); // 10
	    }
	}

	f1();
    
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


```javascript

(function main() {
    
    
    var x = 4;
    
    function func1(){
        console.log('func1', x);	//by hoisting, this x is new x only existing in this scope, other than global x
        var x = 3;	//lexical scope
    }
    
    function func2(){
        console.log('func2', x);
    }
    
    func1();	// func1 undefined
    func2();	// func2 4
}());

```


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




## #. Event Capturing

The opposite concept of event bubbling. When it has 3 hierarchy and click event is in the deepest node, the event will be captured from 1st node and 2nd node before the deepest node. 
For more informations, click [here](https://javascript.info/bubbling-and-capturing)


## #. Event bubbling

event.stopPropagation : stopping from sending the current event to its parents node
event.stopImmediatePropagation : stopping from sending *all the existing events* to its parents node
For more informations, click [here](https://javascript.info/bubbling-and-capturing)




## #. Arrow function with This scope

## #. Number type size

> JavaScript uses double-precision (64-bit) floating point numbers. 64 bits are 8 bytes, but each number actually takes up an average of 9.7 bytes. Likewise, Chrome shows the size of each individual empty array as 32 bytes and the size of each empty object as 56 bytes.
>
>	from [here](https://www.mattzeunert.com/2016/07/24/javascript-array-object-sizes.html#:~:text=JavaScript%20uses%20double%2Dprecision%20(64,empty%20object%20as%2056%20bytes.)

Number can be -(2^53-1) <= x <= 2^53-1 also including float.

also about type, refer [here](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures)


## #. display: flex;

Putting elements to the center of the div.

```css

div#login_wrapper{
    display: flex;
    height: 100vh;
    justify-content: center; /* horizontally center align */
    align-items: center; /* vertically center align */
    border: solid 1px gray;
}

```
