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
