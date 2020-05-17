---
title: JavaScript Revisited
tags:
- JavaScript
---
## JavaScript

* Language of the Web
* Introduced in 1995 as a way to add programs to web pages
* ECMAScript Standard

## Values, Types and Operators
> Inside the computer's world, there is only data. You can read data, modify data, create new data but anything that isn't data simple does not exist.

-----

### DataTypes
* Numbers
* Strings
  * Single Quotes or Double Quotes
  * Escaping using `\n`
  * String Concatenation `(+)`
* Boolean
* Undefined Values
  * Null
  * Undefined

### Operators
* Binary -> Takes Two Values
* Unary -> Takes One Value

### Arithmetic Operators
  * Addition `(+)`
  * Substraction `(-)`
  * Multiplication `(*)`
  * Division `(/)`
  * Modulus `(%)`

### Comparison Operator
  * Greater Than `(>)`
  * Less Than `(<)`
  * Greater Than or Equal To `(>=)`
  * Less Than or Equal To `(<=)`
  * Equal to `(==)`
  * Not Equal to `(!=)`
  * Equality `(===)`

### Logical Operators
  * AND `(&&)`
  * OR `(||)`
  * NOT `(!)`
  * Conditional Operators / Ternary Operator `( ?: )`

#### Automatic Type Conversion

Automatic Typc Converstion using Type Coercion

-----

## JavaScript Program Structure

* Expressions and Statements
  * Javascript program is built out of statements, which themselves contains statements. Statements tend to contain expressions.

* Variables
  * Variables can be used to file piece of data under a name.

* Functions
  * Functions are special values that encapsulate piece of program.

-----

## Control Flow

### If..Then, Else

```javascript
if (expression1)
  statement1;
else if (expression2)
  statement2;
else
  statement3;
```

*Example*

```javascript
let num = Number(prompt("Enter a number"));

if (num <= 10) {
  alert("Too Low");
} else if (num > 10 && num <= 50) {
  alert("Bingo !! Correct Answer");
} else {
  alert("Way Too High");
}
```

### While and Do Loops

```javascript
while (expression) {
  statement
}
```

*Example*
```javascript

let i=1;
while (i<=10) {
  console.log(i);
  i++;
}
```

-----

### For Loops

```javascript
for (let i=0; i<=10; i++ ){
  statement
}
```
*Example*

```javascript

let result;
for (let i=0; i<=10; i++) {
  result += i;
}
console.log(result);

```

-----

### Breaking Out of Loops
* `break` keyword can be used to break out of loop
* `continue` keyword can be used to continue with next loop's next execution

-----

### Switch

``` javascript
let choice = prompt("Enter option [A], [B], [C], [D]");

  switch (choice) {
    case 'A':
      console.log("A Selected");
      break;

    case 'B':
      console.log("B Selected");
      break;

    case 'C':
      console.log("C Selected");
      break;

    case 'D':
      console.log("D Selected");
      break;

    default:
      console.log("Unknown Option Selected");
      break;

  }

```

-----

### Comments
* Single Line Comment

```
// Sinle Line Comment

/*
Example of
Multiline Comment
*/
```
-----

# Functions

> Functions are the Bread and Butter of JavaScript

* Function definition is a regular variable definition where the value of the variable is a function.
* Function is created by an expression with the keyword `function`
* A `return` statement determines the value the function returns
* Parameters and variables declared inside a function are local to the function, re-created every time the function is called, and not visible from the outside
* Functions can take optional arguments but if the function is not using those arguments, it simiply ignores as undefined

### Defining Function

```javascript
let fn_sayHello = function(name) {
  return "Hello " + name;
}

alert(fn_sayHello("User"));

```

#### Declaration Notation
Declaring fn_sayHello to be a function

```
function fn_sayHello(name) {
  return "Hello " + name;
}
```

### Closure in function
> A function that "closes over" some local letiable is called a Closure.

```
function multiplier(factor) {
  return function(number) {
    return factor * number;
  }
}

let twice = multiplier(2);
console.log(twice(5));
>>10
```

-----

### Arrays
```
//Array.forEach
function logger (element, index) {
    console.log("Index: " + index + ": " + element);
}

let countries = ["India", "Nepal", "China"]

countries.forEach(logger);

//Array.filter
function filterAge(element) {
    return element.age >= 21;
}

let people = [
    {name: "Aayush",age: 28},
    {name: "Aadesh",age: 18},
    {name: "Bob",age: 52}
    ]

let peopleLegal = people.filter(filterAge);
console.log(peopleLegal);
```

-----

### Objects
* Enclosed in Curly Braces
* Consists of Properties and Methods
  * Properties are accessed using obj.property1 or obj["property1"]

```javascript
let obj = {
  property1: "Value1",
  property2: "Value2",

  method1: function(args){
    console.log(args);
  }
}

```

#### Looping Over Properties
```javascript
let obj = {name: "A", date: new Date(), scores: [1, 2, 3]};
for (let p in obj) {
  if (obj.hasOwnProperty(p)) {
    console.log(p + ': ' + object[p]);
  }
}
```

-----

### String
* Strings can be Single or Double Quoted
* String indexes are zero based
* Immutable

```javascript
let str_message = "Welcome to JavaScript";
let str_Hello = 'I am String to ';

console.log( str_message.slice(0,5) );
console.log( str_message.indexOf("to") );
console.log( str_Hello.indexOf("to") );
console.log( str_Hello.trim() );
```

-----
