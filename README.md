# JavaScript 

Contents
- [The JavaScript Universe](#the-javascript-universe)
- [Values and Variables](#values-and-variables) 
- [Equality of Values](#equality)
- [Properties](#properties)
- [Mutation](#mutation)
- [Prototypes](#prototypes)
- [Closure](#closure)

---
## The Javascript Universe

A mental model: 
- A value is separate to our code 
- Our code interacts with values, but values exist in a completely separate space 
- Code is more like a list of instructions - if sarements, variable declarations etc
- Values are: booleans, numbers, strings, symbols, functions, objects, null, undefined

Primitive values: A permanent part of JS. You can point to them but can't create, destroy or change them
- Undefined
- Null
- Booleans
- Numbers
- BigInts
- Strings
- Symbols 

Objects and Functions are also values but you can manipulate them.
- Objects ({}, but also arrays are considered objects too, dates etc)
- Functions

No other fundamental value types. Check the type with `console.log(typeof("hello"))`


---
## VAlues and Variables

Primitive Values are Immutable (unchangable)
- i.e.
````js
let arr = [212, 8, 506];
let str = 'hello';

console.log(arr[0]); // 212
console.log(str[0]); // "h"

arr[0] = 420;
console.log(arr); // [420, 8, 506]

str[0] = 'j'; // WE CAN'T CHANGE IT! 
````

Variables are wires that just point to values, we can change what they point to


---
## Equality 
- Strinct Equality `a === b`
  - pretty much the same as 'same value qaulity' except two rare cases
- Loose equality `a == b`
  - many issues arise, do not use in modern code.
- same value qaulity `Object.is(a, b)`
  - points to the same value

---
## Properties
````js
let sherlock = {
  surname: 'Holmes'
  age: 64
}
````
In the above example, sherlock is a variable but surname and age are not - they are properties. 
Properties belong to a particular object. 

Both variables and properties act like 'wires', but the wires of properties start from objects rather than variables. 
Read it like `console.log(sherlock.age)`

---
## Mutation
````js
let sherlock = {
  surname: 'Holmes',
  address: { city: 'London' }
};

let john = {
  surname: 'Watson',
  address: sherlock.address
};
````

Address is simply a property which points or wires to the other object of { city: 'London' }.
````js
john.surname = 'Lennon';
john.address.city = 'Malibu';
````

Changing John's surname simply changes the string value his surname points to. 
Changing John's city in the object adress is pointing to, actually changes that object. So it changes it for both of them 


Mutation is a fancy way of saying "change". 

If we only wanted to change John's address, we would need to do:
````js
john.address = { city: 'Malibu' }
````

Alternative solution with no object mutation is to completely change the wire that the john variable points to - i.e. create a new object from scratch (ie reassign the variable):
````js
john = {
  surname: 'Lennon'
  address: { city: 'Malibu' }
}
````

Let vs Const 

You can use the const keyword instead of let. 
When you use const you create read-only variables, but we can still mutate the object our variable points to. 


---
## Prototypes

````js
let human = {
  teeth: 32
};

let gwen = {
  // We added this line:
  __proto__: human,
  age: 19
};

console.log(gwen.teeth) // 32!
````

We have instructed it to continue searching for our missing property on another object. 
We can think of it as a special `__proto__` wire.

If your objects prototype has its own prototype - it will still work. It just checks in the order.

Shadowing:
````js
let human = {
  teeth: 32
};

let gwen = {
  __proto__: human,
  // This object has its own teeth property:
  teeth: 31
};

console.log(gwen.teeth) // 31 (because it checks gwen for teeth first)
````

you can check the prototype of any object in JS: `console.log(obj.__proto__)`, by default it has the Object prototype 
We can remove this default by doing this:
````js
let weirdo = {
  __proto__: null
}
````

Prototypes are unusual and most frameworks never embraced them. 

---
## Closure

A closure is the combination of a function bundled together (enclosed) with references to its surrounding state.
A closure gives you access to an outer functions scope from an inner function. In JS, closures are created every time a function is created, at function creation time. 

````js
function init() {
  var name = 'Mozilla'; // name is a local variable created by init
  function displayName() {
    // displayName() is the inner function, a closure
    console.log(name); // use variable declared in the parent function
  }
  displayName();
}
init();
````
- the displayName() function is only available within the body of init()


Closures are useful because they let you associate data with a function that operates on that data
````js
function makeSizer(size) {
  return function () {
    document.body.style.fontSize = `${size}px`;
  };
}

const size12 = makeSizer(12);
const size14 = makeSizer(14);
const size16 = makeSizer(16);
````
size12, size14, and size16 are now functions that resize the body text to 12, 14, and 16 pixels, respectively. You can attach them to buttons (in this case hyperlinks) as demonstrated in the following code example.
````js
document.getElementById('size-12').onclick = size12;
document.getElementById('size-14').onclick = size14;
document.getElementById('size-16').onclick = size16;
````