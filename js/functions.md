Function declarations are globally scoped and can be executed anywhere, even before they are written.
Function expressions are stored in variables, so they will be executed only after they are defined.

Example:

```javascript
// Function Declaration
greet(); // This will work
function greet() {
   console.log("Hello, world!");
}

// Function Expression
const sayHello = function () {
   console.log("Hello, world!");
};
sayHello(); // This will work
```

# JavaScript Function Notes

## Rest Operator (`...parameters`)

The rest operator (`...`) allows a function to accept an indefinite number of arguments as an array.

-  It gathers multiple arguments into an array.
-  If placed after other parameters, it captures only the remaining arguments.

Example:

```javascript
function one(...parameters) {
   return parameters;
}
```

---

## Closures in JavaScript

A closure is a function that retains access to its outer function's variables even after the outer function has finished execution.

-  Inner functions can access variables from their parent function.
-  The outer function **cannot** access variables defined inside the inner function.
-  Used for data encapsulation and maintaining state between function calls.

Example:

```javascript
function closure() {
   const outerVar = "I am outer";
   function inner() {
      console.log(outerVar); // Can access outer scope
   }
   return inner;
}
const myClosure = closure();
myClosure();
```

---

## Utility Functions

### Greeting Function

-  Returns a greeting message using template literals.

```javascript
function greet(name) {
   return `Hello ${name}!`;
}
```

### Addition Function

-  Adds two numbers.

```javascript
function add(a, b) {
   return a + b;
}
```

### Even Number Checker

-  Checks if a number is even by using the modulus operator (`%`).

### Multiply by Two

-  Uses `.map()` to double each element in an array.

### Celsius to Fahrenheit Conversion

-  Converts temperature using the formula `(C * 9/5) + 32`.

### Finding the Longest Word

-  Splits a sentence into words and determines the longest one.
-  Useful for text processing and search optimization.

### Character Count

-  Counts occurrences of a specific character in a string, case-insensitive.

```javascript
function countChar(str, char) {
   return str.split("").filter((c) => c.toLowerCase() === char.toLowerCase())
      .length;
}
```

### Removing Duplicates from an Array

-  Uses `Set` to ensure uniqueness in collections.

```javascript
function removeDuplicates(arr) {
   return [...new Set(arr)];
}
```

---

These functions provide fundamental operations that are commonly used in JavaScript programming. They illustrate key concepts such as closures, array manipulation, and higher-order functions, making them valuable tools for developers.
