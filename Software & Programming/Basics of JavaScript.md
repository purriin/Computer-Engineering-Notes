## Table of Contents
[[Basics of JavaScript#1. Introduction |1. Introduction]]
[[Basics of JavaScript#2. Variables|2. Variables]]
[[Basics of JavaScript#3. Conditional Statements|3. Conditional Statements]]
[[Basics of JavaScript#4. Functions|4. Functions]]
## 1. Introduction
**Console**: A panel that displays important messages like errors, an object that can be used.

**Standard Output**:
```js
console.log();
```

**Data Types**:
1. `Number`: Any number, including numbers with decimals: `4`, `8`, `1516`, `23.42`.
2. `BigInt`: Any number, greater than 253-1 or less than -(253-1), with n appended to the number: 1234567890123456n.
3. `String`: Any grouping of characters on your keyboard (letters, numbers, spaces, symbols, etc.) surrounded by single quotes: `' ... '` or double quotes `" ... "`, though we prefer single quotes. Some people like to think of string as a fancy word for text. - [JavaScript Strings](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)
4. `Boolean`: This data type only has two possible values— either `true` or `false` (without quotes). It’s helpful to think of `booleans` as on and off switches or as the answers to a “yes” or “no” question.
5. `Null`: This data type represents the intentional absence of a value, and is represented by the keyword `null` (without quotes).
6. `Undefined`: This data type is denoted by the keyword `undefined` (without quotes). It also represents the absence of a value though it has a different use than `null`. `undefined` means that a given value does not exist.
7. `Symbol`: A newer feature to the language, symbols are unique identifiers, useful in more complex coding. No need to worry about these for now.
8. `Object`: Collections of related data.
	- The first 7 are considered *primitive data types*

**Arithmetic Operators**:
1. Add: `+`
2. Subtract: `-`
3. Multiply: `*`
4. Divide: `/`
5. Remainder: `%`

[**Built-in Objects**](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects):
- JavaScript has other built in objects
- `Math` object includes `.random` which returns a random number between \[0,1) and `.floor` which rounds down to nearest whole number

## 2. Variables
- `var`: Short for variable, is a JavaScript _keyword_ that creates, or _declares_, a new variable.
- *Camel casing* is the standard convention for JavaScript. Ex. `myName` variable name
	- Cannot start with numbers, is case sensitive, cannot be the same as [keywords](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Lexical_grammar#keywords)
- `=` : The *assignment operator*
- `let`: Declares a variable that can be reassigned a different value
	- If we don't assign a value after using `let`, the variable will automatically have a value of `undefined`
- `const`: Short for constant, like `var` and `let`, declares a variable and can store any value to it but cannot be reassigned
	- Will get a `TypeError` if we try to reassign a value to it
	- Must be assigned a value or else will get a `SyntaxError`

- Can concatenate strings via `+` operator
- Can interpolate strings via `${ varName }`, entire string must be wrapped by backticks \` \`
```js
// String Interpolation Example:
const myPet = 'armadillo';  
console.log(`I own a pet ${myPet}.`);  
// Output: I own a pet armadillo.
```

- `typeof`: operator to check the value to the right of it. Ex. `typeof unknown1`

## 3. Conditional Statements
**If Statements**:
```js
if (false) {  
  console.log('The code in this block will not run.');  
} else {  
  console.log('But the code in this block will!');  
}
```

**Comparison Operators**:
- Less than: `<`
- Greater than: `>`
- Less than or equal to: `<=`
- Greater than or equal to: `>=`
- Is equal to: `===`
- Is not equal to: `!==`

- **Note**: `===` is known as Identity / strict equality meaning that the data types must also be equal
	```js
	console.log(1 == true) // output: true 
	console.log(1 === true) // output: false
	```

**Logical Operators**:
- the _and_ operator (`&&`)
- the _or_ operator (`||`)
- the _not_ operator, otherwise known as the _bang_ operator (`!`)

**False Values**:
- `0`
- Empty strings like `""` or `''`
- `null` which represent when there is no value at all
- `undefined` which represent when a declared variable lacks a value
- `NaN`, or Not a Number

**Short-Circuit Evaluation**:
```js
let username = '';  
let defaultName = username || 'Stranger';  
  
console.log(defaultName); // Prints: Stranger
```

**Ternary Operator**:
- Used to simplify if else statements:
```
condition ? < Do if true > : <else do this>;
```

**Switch Statement**:
- Alternative syntax for `if` statements that is easier to read and write.
- Ex. 
```js
let groceryItem = 'papaya';  
  
switch (groceryItem) {  
  case 'tomato':  
    console.log('Tomatoes are $0.49');  
    break;  
  case 'lime':  
    console.log('Limes are $1.49');  
    break;  
  case 'papaya':  
    console.log('Papayas are $1.29');  
    break;  
  default:  
    console.log('Invalid item');  
    break;  
}  
// Prints 'Papayas are $1.29'
```

## 4. Functions
- A function declaration consists of:
	- The `function` keyword.
	- The name of the function, or its identifier, followed by parentheses. Parameters can be placed in the parentheses.
	- A function body, or the block of statements required to perform a specific task, enclosed in the function’s curly brackets, `{ }`

- Default parameters allow parameters to have a predetermined value in case there is no argument passed into the function or if the argument is `undefined` when called.
- Take a look at the code snippet below that uses a default parameter:

```js
function greeting (name = 'stranger') {
	console.log(`Hello, ${name}!`)
}
greeting('Nick') // Output: Hello, Nick!greeting() // Output: Hello, stranger!
```

- `return` keyword allows functions to produce an output

- Another way to define a function is to use a _function expression_.
	- A function with no name is called an _anonymous function_. 
	- A function expression is often stored in a variable in order to refer to it.
- To declare a function expression:
	1. Declare a variable to make the variable’s name be the name, or identifier, of your function. Since the release of ES6, it is common practice to use `const` as the keyword to declare the variable.
	2. Assign as that variable’s value an anonymous function created by using the `function` keyword followed by a set of parentheses with possible parameters. Then a set of curly braces that contain the function body.

- _Arrow function syntax_, a shorter way to write [functions](https://www.codecademy.com/resources/docs/javascript/functions) by using the special “fat arrow” `() =>` notation
	- [Arrow functions](https://www.codecademy.com/resources/docs/javascript/arrow-functions?page_ref=catalog) remove the need to type out the keyword `function`
```js
const rectangleArea = (width, height) => {  
  let area = width * height;  
  return area;  
};
```

## 5. Scope
**Scope** refers to where variables can be accessed throughout the program, and is determined by where and how they are declared.

**Blocks** are [statements](https://www.codecademy.com/resources/docs/javascript/statements) that exist within curly braces `{}`.

**Global scope** refers to the context within which variables are accessible to every part of the program.

**Global variables** are variables that exist within global scope.

**Block scope** refers to the context within which variables are accessible only within the block they are defined.

**Local variables** are variables that exist within block scope.

**Global namespace** is the space in our code that contains globally scoped information.

**Scope pollution** is when too many variables exist in a namespace or variable names are reused.

- In _global scope_, variables are declared outside of blocks. These variables are called _global variables_. Because global variables are not bound inside a block, they can be accessed by any code in the program, including code in blocks.
	- When you declare global variables, they go to the _global namespace_.
	- _Scope pollution_ is when we have too many global variables that exist in the global namespace, or when we reuse variables across different scopes. Scope pollution makes it difficult to keep track of our different variables and [sets](https://www.codecademy.com/resources/docs/javascript/sets) us up for potential accidents.
- Variables that are declared with block scope are known as _local variables_ because they are only available to the code that is part of the same block.

## 6. Arrays
- Arrays are lists that store data in JavaScript.
- Arrays are created with brackets `[]`.
- Each item inside of an array is at a numbered position, or index, starting at `0`.
- We can access one item in an array using its index, with syntax like: `myArray[0]`.
- We can also change an item in an array using its index, with syntax like `myArray[0] = 'new string'`;
- Arrays have a `length` property, which allows you to see how many items are in an array.
- Arrays have their own [methods](https://www.codecademy.com/resources/docs/javascript/methods), including [`.push()`](https://www.codecademy.com/resources/docs/javascript/arrays/push) and [`.pop()`](https://www.codecademy.com/resources/docs/javascript/arrays/pop), which add and remove items from an array, respectively.
- Arrays have many methods that perform different tasks, such as `.slice()` and [`.shift()`](https://www.codecademy.com/resources/docs/javascript/arrays/shift), you can find documentation at the [Mozilla Developer Network](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array) website.
- Some built-in methods are mutating, meaning the method will change the array, while others are not mutating. You can always check the documentation.
- [Variables](https://www.codecademy.com/resources/docs/javascript/variables) that contain arrays can be declared with `let` or `const`. Even when declared with `const`, arrays are still mutable. However, a variable declared with `const` cannot be reassigned.
- Arrays mutated inside of a function will keep that change even outside the function.
- Arrays can be nested inside other arrays.
- To access elements in nested arrays chain indices using bracket notation.

## 7. Loops
