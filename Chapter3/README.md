# Chapter 3. All About Types

Type: a set of values and the things you can do with them

```
function squareOf(n: number) {
  return n * n
}

squareOf(2)     // evaluates to 4
squareOf('z')   // Error TS2345: Argument of type '"z"' is not assignable to
                // parameter of type 'number'.
```

1. `squareOf`'s parameter `n` is constrained to `number`
2. The type of the value 2 is assignable to ( = compatible with) `number`


## The ABCs of Types
### any
- `any` is the default type when you and TS can't figure out what type something is
- should be avoided

#### TSC Flag: noImplicitAny
- by default, TS is permissive so won't compalint about values that it infers as `any`
- `noImplicitAny` is part of the `strict`family of TSC flags, so if you already enabled `strict` in your `tsconfig.json`, you're good to go.

### unknown
- better than `any`
- TS will never infer something as `unknown` -- you have to explicitly annotate it
- You can compare values to values that are of type `unknown`
- But, you can't do things that assume an `unknown` value is of a specific type. You have to prove to TS that the value really is of that type first

```
let a = true              // boolean
let b = false             // boolean
const c = true            // true (specific boolean)
let d: boolean = true     // boolean
let e: true = true        // true (specific boolean)
let f: true = false       // Error TS2322: Type 'false' is not assignable to type 'true'
```

### bigint
- `bigint` can represent integers bigger than 2^53, which is the upperbound of `number`
- JS does not natively support `bigint`

### symbol
- `symbol` is used as an alternative to string keys in objects and maps, in places you want to be extra sure that people are using the right well-known key and didn't accidentally set the key

```
let a = Symbol('a')           // symbol
let b: symbol = Symbol('b')   // symbol
let c = a === b               // symbol
let d = a + 'x'               // symbol
```

### object
```
let a: {
  b: number
  c?: string
  [key: number]: boolean
}
```
Let's see what types of objects we can assign to `a`:

```
a = {b: 1}
a = {b: 1, c: undefined}
a = {b: 1, c: 'd'}
a = {10: true}                // Error TS2741: Property 'b' is missing in type '{10: true}'.
a = {b: 1, 33: 'red'}         // Error TS2741: Type 'string' is not assignable to type 'boolean'.
```

The `[key: T]: U` syntax is called an <strong>index signature</strong>, and this is the way you tell TS that the given object might contain more keys.


## Intermission: Type Aliases, Unions, and Intersections
### Type aliases
```
type Age = number

type Person = {
  name: string
  age: Age
}
```

- Type aliases allow you to create a new name for an existing type. 
- Type aliases are useful for removing repetitiveness and making it clear what a variable is used for

## null, undefined, void, never

JS has two values to represent an absence of something:
- `null`: absence of a value
- `undefined`: something hasn't been defined yet

TS has two additional values:
- `void`: return type of a function that doesn't explicitly return anything
- `never`: the type of a function that never returns at all

## Enums
Enumerate the possible values for a type

```
enum Language {
  English,
  Spanish,
  Russian
}
```

TS will automatically infer a number as the value for each number of your enum:

```
enum Language {
  English = 0,
  Spanish = 1,
  Russian = 2
}
```

Warning: TS lets you access enums both by value and key for convenience, but this can get unsafe quickly:

```
enum Color {
  Red = "#c10000",
  Blue = "#007ac1",
  Pink = 0xc10050,    // A hexadecimal literal
  White = 255         // A decimal literal
}

let c = Color[0]      // string 
let d = Color[6]      // string (!!!)
```

You should not be able to get `Color[6]`, but TS does not stop you.
This kind of unsafe access can be prevented by opting into a safer subset of enum behavior with `const enum` instead.


## Excercises
1. For each of these values, what type will TS infer?
```
a. let a = 1042                     // number
b. let b = 'apples and oranges'     // string
c. const c = 'pineapples'           // 'pineapples'
d. let d = [true, true, false]      // boolean[]
e. let e = {type: 'ficus}           // {type: string}
f. let f = [1, false]               // (number | boolean)[]
g. const g = [3]                    // number[]
h. let h = null                     // any
```

2. Why does each of these throw the error it does?

```
// a.
let i: 3 = 3
i = 4 // Error TS2322: Type '4' is not assignable to type '3'.

/*
i's type is the type literal 3. The type of 4 is the type literal 4, which is not assignable to the type literal 3.
*/

// b.
let j = [1, 2, 3]
j.push(4)
j.push('5') // Error TS2345: Argument of type '"5"' is not
// assignable to parameter of type 'number'.

/*
Since j was initialized with a set of numbers, TypeScript inferred j's type as number[].
The type of '5' is the type literal '5', which is not assignable to number.
*/

// c.
let k: never = 4 // Error TS2322: Type '4' is not assignable to type 'never'.

/*
never is the bottom type. That means it's assignable to every other type, but no type is
assignable to never.
*/

// d.
let l: unknown = 4
let m = l * 2 // Error TS2571: Object is of type 'unknown'.

/*
unknown represent a value that could be anything at runtime. To prove to TypeScript that what
you're doing is safe, you have to first prove to TypeScript that a value of type unknown actually
has a more specific subtype. You do that by refining the value using typeof, instanceof, or
another type query or type guard.
*/
```