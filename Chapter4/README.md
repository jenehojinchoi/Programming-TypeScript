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

From: typescriptlang.org/docs/handbook/2/functions.html
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

## Polymorphism

`generic type parameter`: a placeholder type used to enforce a type-level constraint in multiple places. Also known as polymorphic type parameter

```
type Filter = {
	(array: number[], f: (item: number) => boolean): number[]
	(array: string[], f: (item: string) => boolean): string[]
	(array: object[], f: (item: object) => boolean): object[]
}

// using a generic type paramter T:
type Filter {
	<T>(array: T[], f: (item: T) => boolean): T[]
}
```

## Bounded Polymorphism

```
type TreeNode = {
	value: string
}

type LeafNode = TreeNode & {
  isLeaf: true
}

type InnerNode = TreeNode & {
  children: [TreeNode] | [TreeNode, TreeNode]
}
```
Suppose that there is a `mapNode` function that takes a `TreeNode` and maps over its value returning a new `TreeNode`. 

```
let a: TreeNode = {value: 'a'}
let b: LeafNode = {value: 'b', isLeaf = true}
let c: InnerNode = {value: 'c', children: [b]}

let a1 = mapNode(a, _ => _.toUpperCase())   // TreeNode
let b1 = mapNode(b, _ => _.toUpperCase())   // LeafNode
let c1 = mapNode(c, _ => _.toUpperCase())   // InnerNode
```

How would you write a `mapNode`function that takes a subtype of `TreeNode` and returns that same subtype?

```
function mapNode<T extends TreeNode>(        // T has an upper bound of TreeNode
  node: T,
  f: (value: string) => string
): T {                                       // mapNode returns a value of type T
  return {
    ...node,
    value: f(node.value)
  }
}
```

If we had left off the T entirely and declared `mapNode`as 
```
(node: TreeNode, f: (value: string) => string) => TreeNode
```
, then we would have lost information after mapping a node: `a1`, `b1`, and `c1` would all just be `TreeNode`s.


### Using bounded polymorphism to model arity

Bounded polymorphism can be used in `variadic functions` (functions that take any number of arguments).

```
function call(
  f: (...args: unknown[]) => unknown,
  ...args: unknown[]
): unknown {
  return f(...args)
}

function fill(length: number, value: string): string[] {
  return Array.from({length}, () => value)
}

call(fill, 10, 'a')     // evaluates to an array of 10 'a's
```

We'll need two type paramters: `T`, which is an array of arguments and `R`, which is an arbitrary return value.

```
function call <T extends unknown[], R>(
  f: (...args: T) => R,
  ...args: T
): R {
  return f(...args)
}
```

## Type-Driven Development

<strong>Type-Driven Development</strong>: a style of programming where you sketch out type signatures first and fill in values later

When you write a TS program, start by defining your functions' type signatures, filling in the implementations later. You will be able to ensure that everything makes sense at a high level before you get down to your implementations.


## Excercises

1. Which parts of a function’s type signature does TypeScript infer: the parameters, the return type, or both? <br>

- always: return type
- sometimes: parameter types if it can infer them from context

2. Is JavaScript’s arguments object typesafe? If not, what can you use instead? <br>

`arguments` is not typesafe. You should use a rest parameter instead.