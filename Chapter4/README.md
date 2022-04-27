# Chapter 4. Functions

## Declaring and Invoking Functions
JS and TS support at least five different ways to declare a function:
```
// Named function
function greet(name: string) {
  return 'hello' + name
}

// Function expression
let greet2 = function(name: string) {
	return 'hello' + name
}

// Arrow function expression
let greet3 = (name: string) => {
	return 'hello' + name
}

// Shorthand arrow function expression
let greet4 = (name: string) => 
	'hello' + name

// Function constructor
let greet5 = new Function('name', 'return "hello" + name')
```

### Optional and Default Parameters
Like in object and tuple types, you can use `?` to mark optional parameters.

```
function log(message: string, userId?: string) {
	let time = new Date().toLocaleTimeString()
	console.log(time, message, userId || 'Not signed in')
}

log('Page loaded')								// Logs "12:38:31 PM Page Loaded Not signed in"
log('User signed in', 'da763be') 	// Logs "12:38:31 PM User signed in da763be"
```

You can also use default parameters:

```
function log(message: string, userId = "Not signed in") {
	let time = new Date().toLocaleTimeString()
	console.log(time, message, userId)
}
```

## Rest Parameters
Consider the following function:
```
function sumVariadic(): number {
	return Array
		.from(arguments)
		.reduce((total, n) => total + n, 0)
}
```

This looks okay, but if you hover over `total` or `n` in your text editor, you'll see that TS inferred that both `total` and `n` as type of `any`. 

```
sumVariadic(1, 2, 3) 		// Error TS2554: Expected 0 arguments, but got 3.
```

To safely type this variadic function, we can use rest parameters.
Instead of resorting to the unsafe `arguments` magic variable, we can use rest parameters to safely make our function accept any number of arguments:


```
function sumVariadic(... numbers: number[]): number {
	return Array
		.from(arguments)
		.reduce((total, n) => total + n, 0)
}

sumVariadic(1, 2, 3) 		// evaluates to 6
```

## Call Signatures

>From: typescriptlang.org/docs/handbook/2/functions.html
>In JavaScript, functions can have properties in addition to being callable. However, the function type expression syntax doesn’t allow for declaring properties. If we want to describe something callable with properties, we can write a call signature in an object type:

```
function sum(a: number, b: number): number {
	return a + b
}
```
In TS, we can express the function's type as the following:

```
(a: number, b: number) => number
```