# JavaScript 

Contents
- [The JavaScript Universe](#the-javascript-universe)
- [Values and Variables](#values-and-variables) 
- [Equality of Values](#equality)
- [Properties](#properties)
- [Mutation](#mutation)
- [Prototypes](#prototypes)
- [Closure](#closure)
- [Promises](#promises)
- [The DOM](#dom)
- [Core web and browser concepts](#core-web-and-browser-concepts)
  - Initial Request
  - DOM tree
  - CSS Object model (CSSOM)
  - Render Tree
  - Rendering sequence (need to learn more about this)
- [Static Vs Dynamic Websites](#static-vs-dynamic-websites)
  - Headless CMS
- [Frontend System Design](#front-end-system-design)



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

----
## Promises
The `Promise` object represents the eventual completion (or failure) of an asynchronous operation and its resulting value
It is a proxy for a value not necessarily known when the promise is created. 


A promise is a returned object to which you attach callbacks, instead of passing callbacks into a function. 
````js
createAudioFileAsync(audioSettings).then(successCallback, failureCallback);
// depending on if it succeeded or failed, it will call that callback.
````
- Callbacks added with then() will never be invoked before the completion of the current run of the JavaScript event loop.
- Multiple callbacks may be added by calling then() several times. They will be invoked one after another, in the order in 
- A common need is to execute two or more asynchronous operations back to back, where each subsequent operation starts when the previous operation succeeds, with the result from the previous step. We accomplish this by creating a promise chain.
````js
doSomething()
  .then((url) => fetch(url))
  .then((res) => res.json())
  .then((data) => {
    listOfIngredients.push(data);
  })
  .then(() => {
    console.log(listOfIngredients);
  });
````

---
## DOM

The Document Object Model (DOM) is the data representation of the objects that comprise the structure and content of a document on the web. 
The DOM represents the document as nodes and objects so programming languages can interact with the page. It is an object orientated representation of the web page. 

For example, the DOM specifies that the `querySelectorAll` method in the below code snippet must return a list of all the `<p>` elements in the document:
````js
const paragraphs = document.querySelectorAll("p");
// paragraphs[0] is the first <p> element
// paragraphs[1] is the second <p> element, etc.
alert(paragraphs[0].nodeName);
````

To access the DOM you just have do use the `document` variable as shown above. You can do something like this even:
````js
<html lang="en">
  <head>
    <script>
      // run this function when the document is loaded
      window.onload = () => {
        // create a couple of elements in an otherwise empty HTML page
        const heading = document.createElement("h1");
        const headingText = document.createTextNode("Big Head!");
        heading.appendChild(headingText);
        document.body.appendChild(heading);
      };
    </script>
  </head>
  <body></body>
</html>
````

---
## Core Web and Browser Concepts

### Initial request
When a browser sends a request to a server to fetch a HTML document, the server returns a HTML page in binary stream format. Basically a text file with the response header `Content-Type: text/html: charset=UTF-8`.
The text/HTML is a MIME Type (tells browser its a HTML document) and the charset says what character encoding it has. 

Using this info, the browser converts the binary format into a readable text file. 

### DOM Tree
This speeds it up. Looks something like:
- HTML
  - head
    - title
    - link
  - body
    - div
      - h1
        - text node
      - p
        - text node
    - script

### CSS Object Model (CSSOM)
CSS, cascading style sheets, make websites look good. 

After constructing the DOM, the browser reads CSS and constructs the CSSOM.
If certain CSS values aren't defined, they're set to the default set by the W3C css standard. 


### Render Tree 
This is a tree-like structure that combines DOM and CSSOM together. 

### Rendering Sequence 
1. When a web page is loaded, the browser first reads the HTML text and constructs a DOM tree from it.
2. Then it processes the CSS and constructs a CSSOM tree from it.
3. Then if constructs the Render Tree from it.
4. Then the browser starts printing the individual elements on the screen, starting with the Layout operation from the render tree
5. It then does the Paint operation from the render tree 
6. It then does the Compositing operation from the render tree (this is when it starts being drawn on the screen)

This sequence above is done by the "browser engine" / "layout engine" / "rendering engine" which resides in the browser. 
Examples include Webkit (safari), Blink (chromium)

Different browser engines do it differently 
- parsing and external resources is the process of reading HTML content and constructing a DOM tree from it."DOM parsing" 


- `FP` is "First paint", the time at which the browser started printing things on the screen -i.e. first pixel of background colour
- `FCP` is "First Contentful Paint", which means the time at whic hthe browser has rendered the first pixel of the content such as text or image 
- `LCP` is "Largest Contentful Paint", the time at which the browser has rendered large pieces of text or image

Recommendations around how long each of these should be is found at "Core Web Vitals" reports via google is one option


---
## Static vs Dynamic Websites

When discussing whether a site is static or dynamic, half of the battle is determining what aspect of the site you’re discussing. The code of the page, delivery of the page, and the client browser can all be considered either static or dynamic.

CODE
  - Static: content is hard-coded on the page
  - Dynamic: references to content that are controlled externally with a CMS or database

Delivery
  - Static: Deliver static code that is pre-rendered (usually via a CDN)
  - Dynamic: code is rendered in real time by the server

Client Browser
  - Static: Page doesn't change, remains static for all who access it 
  - Dynamic: Use JS to change page content, animation, etc in realtime.


We generally refer to the delivery - how a site is rendered. 
Sites can be dynamically generated, cached and served statically. Static sites can have dynamic components. 
Static sites can be less expensive than dynamic sites to build, but hooking up to a headless CMS can make it expensive. 

The more static, the better the load times. 

Usage
- Static sites: Sites that are simple and will not change much - brochure site, blog, landing page 
- Dynamic sites: you want personalisation, login, scalability, CMS, web app


### Headless CMS
A headless CMS is a content management system containing only backend code without frontend design. Content can be retrieved via APIs, which allows creators to publish across any device or digital asset, streamlining workflows for a digital-first world. 

It allows you to manage content in one place and be able to deploy that content across any frontend you choose (i.e pc, phone, smart watch)

---
## Front End System Design

https://www.frontendinterviewhandbook.com/front-end-system-design

Two main kinds of frontend system design are UI components and applications. 
- UI Components
  - Image carousel
  - Selector
- Applications
  - News feed
  - Video watching website
  - Chat application

### UI Components
Focus on RADAD:
1. requirements clarifications/ alignment
  - primary device it will be used on, desktop?
  - which browsers to support?
  - support internationalisation?
  - accessibility?
2. architecture - UI compoent or App, draw diagram
  - i.e. an Image Carousel
    - main image
    - thumbnails
    - image store (client side cache of list of photos to display)
3. Data model - how will it store data passed into it? data structures?
  - component state (i.e. react)
  - lifecycle methods 
  - each component should maintain its own independent state if possible
  - if component has multiple sub-components, best to keep state in parent component
4. API design - what options are allowed etc
  - i.e. you want to reuse components if possible
  - what are configuration options you would allow for the component (props in react). reasonable defaults?
  - open-closed principal: open for extension but closed for modification.
5. Deep dive - UX, performance, Accessibility, internationalisation, multi-device support, security (i.e. input form)
  - UX:
    - reflect state of component to user (loading icon)
    - display an empty state if no items in a list, instead of not rendering anything 
    - destructive actions should have a confirmation step
    - disable interactive elements if they trigger async requests
    - if search inputs involved, each keystroke should not fire a network request
    - handle extreme cases: super long or short strings 
    - if many items, pagination or contain them with a max width/ height
    - keyboard friendliness: keyboard only users , tabbing etc. 
  - Performance
    - loading speed
      - less JS the faster
    - responsiveness to user interactions
      - if user interactions results in requesting data to be loaded over a network, minimising that delay or removing it can be key. 
    - memory space
      - the more memory the component takes up, the slower the browser performances and the experience will feel sluggish / janky 
  - Accessibility (a11y)
    - color contrasts
    - keyboard friendliness
    - visual impairment
    - transcripts for audio
  - Internationalisation 
    - Strings used in the component shouldn't be hardcoded to a specific language 
  - Mutli-device support
    - expected to be used on mobile web? smaller viewport and less powerful




