# JavaScript Fundamentals — Detailed Notes

---

## 1. Getting Started

JavaScript (JS) is a high-level, interpreted programming language used to make web pages interactive. It runs in the browser (via the JS engine, e.g., V8 in Chrome) and also on servers (via Node.js).

**How to run JS:**
- In the browser console (DevTools → Console tab)
- Inside a `<script>` tag in HTML
- In a `.js` file linked to HTML: `<script src="index.js"></script>`
- Using Node.js: `node filename.js`

```html
<!-- Linking JS to HTML -->
<!DOCTYPE html>
<html>
  <head>
    <title>My Page</title>
  </head>
  <body>
    <script src="index.js"></script>
  </body>
</html>
```

**Comments:**
```javascript
// This is a single-line comment

/* 
  This is a 
  multi-line comment 
*/
```

---

## 2. Display Output on the Screen

There are several ways to output data in JavaScript:

```javascript
console.log("Hello World");     // Prints to the browser/Node console — most used for debugging
alert("Hello World");           // Shows a popup alert box in the browser
document.write("Hello World");  // Writes directly into the HTML document (rarely used today)
```

```javascript
// console.log can take multiple arguments
console.log("Name:", "John", "Age:", 25);
// Output: Name: John Age: 25
```

---

## 3. Variables

Variables are containers that store data values. In modern JS, we declare variables using `let`, `const`, or `var`.

```javascript
let name = "Alice";     // can be reassigned later
const age = 25;         // cannot be reassigned (constant)
var city = "London";    // older way, function-scoped (avoid using in modern code)

name = "Bob";           // ✅ allowed
age = 26;                // ❌ Error: Assignment to constant variable
```

**Key differences:**

| Keyword | Scope | Reassignable | Redeclarable | Hoisting |
|---|---|---|---|---|
| `var` | function-scoped | Yes | Yes | Yes (initialized as `undefined`) |
| `let` | block-scoped | Yes | No | Yes (but in "temporal dead zone") |
| `const` | block-scoped | No | No | Yes (but in "temporal dead zone") |

**Naming rules:**
```javascript
let userName = "valid";     // ✅ camelCase (recommended)
let _private = "valid";     // ✅ can start with underscore
let $element = "valid";     // ✅ can start with dollar sign
let 1stName = "invalid";    // ❌ cannot start with a number
```

---

## 4. Strings — (Stressed Topic ⭐)

A **string** is a sequence of characters used to represent text. Strings are always wrapped in quotes.

### 4.1 Creating a String

```javascript
let single = 'Hello';                // single quotes
let double = "Hello";                // double quotes
let template = `Hello`;              // backticks (template literal)

// Using the String constructor (less common)
let str = new String("Hello");       // creates a String object (not a primitive)
console.log(typeof str);             // "object"

let primitive = String(123);         // converts 123 to "123" (primitive string)
console.log(typeof primitive);       // "string"
```

### 4.4 Joining Strings (Concatenation)

```javascript
let firstName = "John";
let lastName = "Doe";

// Method 1: Using the + operator
let fullName = firstName + " " + lastName;
console.log(fullName); // "John Doe"

// Method 2: Using += operator
let greeting = "Hello, ";
greeting += firstName;
console.log(greeting); // "Hello, John"

// Method 3: Using concat() method
let combined = firstName.concat(" ", lastName);
console.log(combined); // "John Doe"
```

### 4.7 Template Strings (Template Literals)

Template literals use backticks (`` ` ``) instead of quotes and allow embedded expressions.

```javascript
let name = "Alice";
let message = `Hello, my name is ${name}`;
console.log(message); // "Hello, my name is Alice"
```

### 4.10 String Interpolation

String interpolation means inserting variables or expressions directly inside a template string using `${}`.

```javascript
let a = 5;
let b = 10;

console.log(`The sum of ${a} and ${b} is ${a + b}`);
// Output: The sum of 5 and 10 is 15

let user = { name: "Tom", age: 20 };
console.log(`${user.name} is ${user.age} years old.`);
// Output: Tom is 20 years old.

// You can even call functions inside interpolation
function shout(text) {
  return text.toUpperCase();
}
console.log(`He said: ${shout("hello")}`); // "He said: HELLO"
```

### 4.13 Multi-line Template Strings

Before template literals, multi-line strings needed `\n` or string concatenation. Template literals make it simple:

```javascript
// Old way
let oldWay = "Line 1\n" + "Line 2\n" + "Line 3";

// New way using template literals
let newWay = `Line 1
Line 2
Line 3`;

console.log(newWay);
// Output:
// Line 1
// Line 2
// Line 3
```

### 4.x Important String Properties & Methods (Extra — as requested)

```javascript
let str = "  Hello World  ";

// length property
console.log(str.length); // 15

// Case conversion
console.log(str.toUpperCase());   // "  HELLO WORLD  "
console.log(str.toLowerCase());   // "  hello world  "

// Trimming whitespace
console.log(str.trim());          // "Hello World"
console.log(str.trimStart());     // "Hello World  "
console.log(str.trimEnd());       // "  Hello World"

// Accessing characters
let s = "JavaScript";
console.log(s[0]);                // "J"
console.log(s.charAt(4));         // "S"
console.log(s.at(-1));            // "t" (last character, negative indexing)

// Searching within a string
console.log(s.indexOf("Script")); // 4 (returns index, -1 if not found)
console.log(s.lastIndexOf("a"));  // 3
console.log(s.includes("Java"));  // true
console.log(s.startsWith("Java")); // true
console.log(s.endsWith("pt"));    // true

// Extracting parts of a string
console.log(s.slice(0, 4));       // "Java" (start, end - excludes end index)
console.log(s.slice(-6));         // "Script" (negative index from end)
console.log(s.substring(0, 4));   // "Java" (similar to slice, no negative index support)
console.log(s.substr(4, 6));      // "Script" (start, length) — deprecated but still seen

// Replacing text
console.log(s.replace("Java", "Type"));      // "TypeScript" (replaces first match)
console.log("a-a-a".replaceAll("a", "b"));   // "b-b-b" (replaces all matches)

// Splitting a string into an array
let sentence = "I love JavaScript";
console.log(sentence.split(" ")); // ["I", "love", "JavaScript"]
console.log("a,b,c".split(","));  // ["a", "b", "c"]

// Repeating a string
console.log("ab".repeat(3)); // "ababab"

// Padding a string
console.log("5".padStart(3, "0")); // "005"
console.log("5".padEnd(3, "0"));   // "500"

// Converting other types to string
console.log(String(123));         // "123"
console.log((123).toString());    // "123"

// Comparing strings
console.log("apple".localeCompare("banana")); // -1 (apple comes before banana)

// Checking if something is a string
console.log(typeof "hello"); // "string"
```

**Quick reference table of common String methods:**

| Method | Purpose |
|---|---|
| `.length` | Number of characters |
| `.toUpperCase()` / `.toLowerCase()` | Change case |
| `.trim()` | Remove whitespace from both ends |
| `.charAt(i)` / `[i]` | Get character at index |
| `.indexOf()` / `.lastIndexOf()` | Find position of substring |
| `.includes()` | Check if substring exists |
| `.startsWith()` / `.endsWith()` | Check beginning/end |
| `.slice()` / `.substring()` | Extract part of string |
| `.replace()` / `.replaceAll()` | Replace text |
| `.split()` | Convert string to array |
| `.concat()` | Join strings |
| `.repeat()` | Repeat string |
| `.padStart()` / `.padEnd()` | Pad string with characters |

---

## 5. Numbers

```javascript
let integer = 10;
let float = 10.5;
let negative = -20;

// Arithmetic operators
console.log(10 + 5);  // 15
console.log(10 - 5);  // 5
console.log(10 * 5);  // 50
console.log(10 / 5);  // 2
console.log(10 % 3);  // 1 (modulus/remainder)
console.log(2 ** 3);  // 8 (exponent)

// Useful Number methods
console.log(Number.isInteger(10));    // true
console.log(Number("42"));            // 42 (string to number)
console.log((3.14159).toFixed(2));    // "3.14" (rounds to 2 decimals)
console.log(Math.round(4.7));         // 5
console.log(Math.floor(4.7));         // 4
console.log(Math.ceil(4.1));          // 5
console.log(Math.max(1, 5, 3));       // 5
console.log(Math.min(1, 5, 3));       // 1
console.log(Math.random());           // random number between 0 and 1
```

---

## 7. `undefined` and `null`

```javascript
let a;
console.log(a); // undefined -> variable declared but not assigned a value

let b = null;
console.log(b); // null -> intentional "no value" assigned by the programmer

console.log(typeof undefined); // "undefined"
console.log(typeof null);      // "object" (this is a famous JS quirk/bug)

console.log(undefined == null);  // true  (loose equality, checks value only)
console.log(undefined === null); // false (strict equality, checks value AND type)
```

---

## 8. Objects — (Stressed Topic ⭐)

An **object** is a collection of key-value pairs, used to store related data and functionality together.

### 8.1 Create Object

```javascript
// Object literal (most common way)
let person = {
  name: "John",
  age: 30,
  isStudent: false
};

// Using the Object constructor
let person2 = new Object();
person2.name = "Sara";

console.log(person);
```

### 8.4 Using Variables as Object Properties

```javascript
let key = "email";
let value = "john@example.com";

// Computed property names using square brackets
let user = {
  name: "John",
  [key]: value   // key becomes "email"
};

console.log(user); // { name: "John", email: "john@example.com" }
```

### 8.5 Object Property Shorthand

If a variable's name matches the property name you want, you can shorten the syntax:

```javascript
let name = "Alice";
let age = 25;

// Long way
let user1 = { name: name, age: age };

// Shorthand way
let user2 = { name, age };

console.log(user2); // { name: "Alice", age: 25 }
```

### 8.7 Get Object Values — Dot Notation

```javascript
let car = { brand: "Toyota", model: "Corolla", year: 2022 };

console.log(car.brand); // "Toyota"
console.log(car.model); // "Corolla"
```

### 8.10 Add Object Property using Dot Notation

```javascript
let car = { brand: "Toyota" };
car.color = "Red";       // adds a new property
console.log(car); // { brand: "Toyota", color: "Red" }
```

### 8.13 Update Object Property using Dot Notation

```javascript
let car = { brand: "Toyota", color: "Red" };
car.color = "Blue";      // updates existing property
console.log(car); // { brand: "Toyota", color: "Blue" }
```

### 8.16 Get Object Values — Bracket Notation

```javascript
let car = { brand: "Toyota", model: "Corolla" };

console.log(car["brand"]); // "Toyota"

// Bracket notation is needed when the property name is stored in a variable
let prop = "model";
console.log(car[prop]); // "Corolla"

// Also needed for property names with spaces or special characters
let obj = { "first name": "John" };
console.log(obj["first name"]); // "John"
```

### 8.19 Add Object Property using Bracket Notation

```javascript
let car = { brand: "Toyota" };
car["year"] = 2023;
console.log(car); // { brand: "Toyota", year: 2023 }
```

### 8.22 Update Object Property using Bracket Notation

```javascript
let car = { brand: "Toyota", year: 2023 };
car["year"] = 2024;
console.log(car); // { brand: "Toyota", year: 2024 }
```

### 8.26 Get Object Keys — `Object.keys()`

```javascript
let car = { brand: "Toyota", model: "Corolla", year: 2022 };
console.log(Object.keys(car)); // ["brand", "model", "year"]
```

### 8.29 Get Object Values — `Object.values()`

```javascript
let car = { brand: "Toyota", model: "Corolla", year: 2022 };
console.log(Object.values(car)); // ["Toyota", "Corolla", 2022]
```

### 8.32 Get Object Keys and Values — `Object.entries()`

```javascript
let car = { brand: "Toyota", model: "Corolla" };
console.log(Object.entries(car));
// [["brand", "Toyota"], ["model", "Corolla"]]

// Commonly used with a loop
for (let [key, value] of Object.entries(car)) {
  console.log(`${key}: ${value}`);
}
// brand: Toyota
// model: Corolla
```

### 8.35 Remove Property from Object

```javascript
let car = { brand: "Toyota", model: "Corolla", year: 2022 };
delete car.year;
console.log(car); // { brand: "Toyota", model: "Corolla" }
```

### 8.38 Object Naming Conventions

```javascript
// ✅ Good practice: use camelCase for property names
let user = {
  firstName: "John",
  lastName: "Doe",
  isActive: true
};

// Avoid: snake_case or spaces (requires bracket notation to access)
let bad = {
  "first_name": "John",   // works but not conventional
  "is active": true        // needs bracket notation: bad["is active"]
};
```

**Extra useful Object methods:**
```javascript
let obj = { a: 1, b: 2 };

console.log(Object.freeze(obj));       // makes object immutable
console.log(Object.assign({}, obj));   // copies/merges objects
console.log("a" in obj);               // true - checks if key exists
console.log(obj.hasOwnProperty("a"));  // true

// Spread operator - shallow copy/merge
let copy = { ...obj, c: 3 };
console.log(copy); // { a: 1, b: 2, c: 3 }
```

---

## 9. Array

An **array** is an ordered list of values, stored in a single variable.

```javascript
let fruits = ["Apple", "Banana", "Mango"];

console.log(fruits[0]);      // "Apple" (index starts at 0)
console.log(fruits.length);  // 3

// Arrays can hold mixed data types
let mixed = [1, "hello", true, { name: "John" }, [1, 2, 3]];
```

---

## 10. Array Methods 1 — (Stressed Topic ⭐)

### 10.1 Adding Array Element — `push()`

Adds one or more elements to the **end** of the array. Returns the new length.

```javascript
let fruits = ["Apple", "Banana"];
fruits.push("Mango");
console.log(fruits); // ["Apple", "Banana", "Mango"]

fruits.push("Grapes", "Orange"); // can add multiple at once
console.log(fruits); // ["Apple", "Banana", "Mango", "Grapes", "Orange"]
```

### 10.4 Adding Array Element — `unshift()`

Adds one or more elements to the **beginning** of the array. Returns the new length.

```javascript
let fruits = ["Banana", "Mango"];
fruits.unshift("Apple");
console.log(fruits); // ["Apple", "Banana", "Mango"]
```

### 10.7 Removing Array Element — `pop()`

Removes the **last** element and returns that removed element.

```javascript
let fruits = ["Apple", "Banana", "Mango"];
let removed = fruits.pop();
console.log(removed); // "Mango"
console.log(fruits);  // ["Apple", "Banana"]
```

### 10.10 Removing Array Element — `shift()`

Removes the **first** element and returns that removed element.

```javascript
let fruits = ["Apple", "Banana", "Mango"];
let removed = fruits.shift();
console.log(removed); // "Apple"
console.log(fruits);  // ["Banana", "Mango"]
```

### 10.13 Array Length

```javascript
let fruits = ["Apple", "Banana", "Mango"];
console.log(fruits.length); // 3

// You can also use length to remove elements
fruits.length = 1;
console.log(fruits); // ["Apple"]

// Or to clear an array
fruits.length = 0;
console.log(fruits); // []
```

**Quick reference table:**

| Method | Adds/Removes | Position | Returns |
|---|---|---|---|
| `push()` | Adds | End | New array length |
| `unshift()` | Adds | Beginning | New array length |
| `pop()` | Removes | End | Removed element |
| `shift()` | Removes | Beginning | Removed element |

---

## 12. Conditionals

```javascript
let age = 20;

if (age >= 18) {
  console.log("Adult");
} else if (age >= 13) {
  console.log("Teenager");
} else {
  console.log("Child");
}

// Ternary operator (shorthand if-else)
let status = age >= 18 ? "Adult" : "Minor";
console.log(status);

// Switch statement
let day = "Mon";
switch (day) {
  case "Mon":
    console.log("Monday");
    break;
  case "Tue":
    console.log("Tuesday");
    break;
  default:
    console.log("Unknown day");
}
```

---

## 13. Logical Operations

```javascript
let a = true, b = false;

console.log(a && b); // false (AND - both must be true)
console.log(a || b); // true  (OR - at least one true)
console.log(!a);     // false (NOT - flips the boolean)

// Short-circuit evaluation
let name = null;
let displayName = name || "Guest"; // "Guest" (used for default values)
console.log(displayName);

// Nullish coalescing operator (??) - only checks null/undefined, not falsy values like 0 or ""
let count = 0;
console.log(count || 10); // 10  (0 is falsy, so it falls back)
console.log(count ?? 10); // 0   (0 is not null/undefined, so it's kept)
```

---

## 14. Type Conversions

```javascript
// String to Number
console.log(Number("123"));    // 123
console.log(parseInt("123px")); // 123
console.log(parseFloat("3.14")); // 3.14
console.log(+"123");            // 123 (unary plus trick)

// Number to String
console.log(String(123));       // "123"
console.log((123).toString());  // "123"

// To Boolean
console.log(Boolean(0));        // false
console.log(Boolean(""));       // false
console.log(Boolean(null));     // false
console.log(Boolean(undefined));// false
console.log(Boolean(NaN));      // false
console.log(Boolean("hello"));  // true
console.log(Boolean(1));        // true

// Implicit (automatic) conversion
console.log("5" + 3);   // "53" (number converted to string)
console.log("5" - 3);   // 2   (string converted to number)
console.log("5" * "2"); // 10  (both converted to numbers)
```

---

## 15. Functions

```javascript
// Function declaration
function greet(name) {
  return `Hello, ${name}!`;
}
console.log(greet("Alice")); // "Hello, Alice!"

// Function expression
const add = function (a, b) {
  return a + b;
};
console.log(add(2, 3)); // 5

// Arrow function
const multiply = (a, b) => a * b;
console.log(multiply(2, 3)); // 6

// Default parameters
function greetUser(name = "Guest") {
  return `Welcome, ${name}`;
}
console.log(greetUser()); // "Welcome, Guest"

// Rest parameters - collects remaining args into an array
function sumAll(...numbers) {
  return numbers.reduce((total, n) => total + n, 0);
}
console.log(sumAll(1, 2, 3, 4)); // 10
```

---

## 17. Array Methods 2 — (Stressed Topic ⭐)

### 17.1 Execute Function for Each Element — `forEach()`

Runs a given function once for every array element. Does **not** return a new array.

```javascript
let numbers = [1, 2, 3];
numbers.forEach(function (num) {
  console.log(num * 2);
});
// 2
// 4
// 6

// With arrow function
numbers.forEach(num => console.log(num * 2));
```

### 17.4 Map a Function and Get a New Array — `map()`

Creates a **new array** by applying a function to every element of the original array.

```javascript
let numbers = [1, 2, 3];
let doubled = numbers.map(num => num * 2);
console.log(doubled); // [2, 4, 6]
console.log(numbers); // [1, 2, 3] (original unchanged)
```

### 17.7 Difference between `forEach` and `map`

| Feature | `forEach()` | `map()` |
|---|---|---|
| Return value | `undefined` | New array |
| Modifies original array | No | No |
| Use case | Perform an action (e.g., logging) | Transform data |

```javascript
let nums = [1, 2, 3];

let a = nums.forEach(n => n * 2);
console.log(a); // undefined

let b = nums.map(n => n * 2);
console.log(b); // [2, 4, 6]
```

### 17.9 Filter Elements Based on Given Condition — `filter()`

Creates a **new array** with only the elements that pass a test (return `true`).

```javascript
let numbers = [1, 2, 3, 4, 5, 6];
let evenNumbers = numbers.filter(num => num % 2 === 0);
console.log(evenNumbers); // [2, 4, 6]

let words = ["apple", "banana", "kiwi", "watermelon"];
let longWords = words.filter(word => word.length > 5);
console.log(longWords); // ["banana", "watermelon"]
```

### 17.11 Create Arrays from Iterables or Array-like Objects — `Array.from()`

```javascript
// From a string (iterable)
console.log(Array.from("hello")); // ["h", "e", "l", "l", "o"]

// From a Set (iterable)
let set = new Set([1, 2, 3]);
console.log(Array.from(set)); // [1, 2, 3]

// With a mapping function (2nd argument)
console.log(Array.from([1, 2, 3], x => x * 2)); // [2, 4, 6]
```

### 17.13 – 17.16 Create Arrays from Array-like Objects (examples 1–4)

An "array-like object" has a `length` property and indexed elements, but doesn't have array methods like `push`, `map`, etc.

```javascript
// Example 1: array-like object
let arrayLike = { 0: "a", 1: "b", 2: "c", length: 3 };
console.log(Array.from(arrayLike)); // ["a", "b", "c"]

// Example 2: NodeList from the DOM is array-like
// let divs = document.querySelectorAll("div");
// let divArray = Array.from(divs);

// Example 3: arguments object inside a function is array-like
function example() {
  console.log(arguments);           // Arguments(3) [1, 2, 3] - not a real array
  let realArray = Array.from(arguments);
  console.log(realArray);           // [1, 2, 3] - now a real array
}
example(1, 2, 3);

// Example 4: Using the spread operator as an alternative to Array.from()
let arrLike = { 0: "x", 1: "y", length: 2 };
// Spread only works directly on iterables, not plain array-like objects,
// so for objects like this, Array.from() is preferred:
console.log(Array.from(arrLike)); // ["x", "y"]

// Spread works great on iterables like strings, Sets, Maps:
console.log([..."hi"]); // ["h", "i"]
```

### 17.19 `isArray()` Method

Checks whether a given value is an array. Returns `true` or `false`.

```javascript
console.log(Array.isArray([1, 2, 3]));      // true
console.log(Array.isArray("hello"));        // false
console.log(Array.isArray({ a: 1 }));       // false
console.log(Array.isArray(Array.from("hi"))); // true
```

**Bonus Array methods worth knowing (extra):**
```javascript
let nums = [5, 1, 4, 2, 3];

console.log(nums.find(n => n > 3));     // 5 (first match)
console.log(nums.findIndex(n => n > 3)); // 0 (index of first match)
console.log(nums.some(n => n > 4));     // true (at least one matches)
console.log(nums.every(n => n > 0));    // true (all match)
console.log(nums.sort((a, b) => a - b)); // [1, 2, 3, 4, 5]
console.log(nums.reverse());            // reverses array in place
console.log(nums.includes(3));          // true
console.log(nums.join("-"));            // "1-2-3-4-5" (as a string)
console.log(nums.reduce((sum, n) => sum + n, 0)); // sum of all elements
```

---

## 18. Loops — (Stressed Topic ⭐)

### 18.7 `for...in` Loop in an Array

The `for...in` loop iterates over the **keys/indexes** of an object or array. (Generally, `for...of` is preferred for arrays — `for...in` is mainly meant for objects.)

```javascript
let fruits = ["Apple", "Banana", "Mango"];

for (let index in fruits) {
  console.log(index, fruits[index]);
}
// 0 Apple
// 1 Banana
// 2 Mango
```

### 18.9 `for...in` Loop in an Object

```javascript
let person = { name: "John", age: 30, city: "NYC" };

for (let key in person) {
  console.log(`${key}: ${person[key]}`);
}
// name: John
// age: 30
// city: NYC
```

### 18.11 `for...of` Loop

The `for...of` loop iterates over the **values** of an iterable (arrays, strings, Maps, Sets, etc.). This is the preferred way to loop through array values.

```javascript
let fruits = ["Apple", "Banana", "Mango"];

for (let fruit of fruits) {
  console.log(fruit);
}
// Apple
// Banana
// Mango

// Works on strings too
for (let char of "Hi") {
  console.log(char);
}
// H
// i

// Note: for...of does NOT work directly on plain objects
// because plain objects are not iterable.
```

### 18.13 `break` Statement

Immediately exits/stops the loop entirely.

```javascript
let numbers = [1, 2, 3, 4, 5];

for (let num of numbers) {
  if (num === 3) {
    break; // stops the loop completely when num is 3
  }
  console.log(num);
}
// 1
// 2
```

### 18.15 `continue` Statement

Skips the **current iteration** and moves to the next one (does not stop the whole loop).

```javascript
let numbers = [1, 2, 3, 4, 5];

for (let num of numbers) {
  if (num === 3) {
    continue; // skips only when num is 3
  }
  console.log(num);
}
// 1
// 2
// 4
// 5
```

**Other loop types (for completeness):**
```javascript
// Classic for loop
for (let i = 0; i < 3; i++) {
  console.log(i);
} // 0 1 2

// while loop
let i = 0;
while (i < 3) {
  console.log(i);
  i++;
}

// do...while loop (runs at least once, even if condition is false)
let j = 0;
do {
  console.log(j);
  j++;
} while (j < 3);
```

**Comparison: `for...in` vs `for...of`**

| Feature | `for...in` | `for...of` |
|---|---|---|
| Iterates over | Keys/indexes | Values |
| Best used with | Objects | Arrays, Strings, Maps, Sets |
| Works on plain objects | Yes | No |

---

## Summary Cheat Sheet

- **Strings**: `.length`, `.slice()`, `.split()`, `.replace()`, `.trim()`, template literals with `${}`
- **Objects**: dot vs bracket notation, `Object.keys()/values()/entries()`, `delete`
- **Arrays**: `push/pop/unshift/shift`, `map/filter/forEach`, `Array.from()`, `Array.isArray()`
- **Loops**: `for...in` (keys), `for...of` (values), `break` (stop), `continue` (skip)

---
*Notes compiled to match your course progress (Getting Started → Loops), with extra emphasis on Strings, Objects, Array Methods, and Loops as requested.*
