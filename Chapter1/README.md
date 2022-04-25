# Chapter 1. Introduction

## Type Safety
Type safety: using tpes to prevent programs from doing invalid things 

The following is an example of JavaScript code that tries invalid things:
```
3 + []      // Evaluates to the string '3'

let obj = {}
obj.foo     // Evaluates to undefined

function a(b) {
  return b/2
}
a("z")      // Evaluates to NaN
```

JavaScript throws an error when you actually run the program.
However, TypeScript gives you error messages <strong>in your text editor, as you type!</strong>

The following is an example of TypeScript code that tries invalid things:
```
3 + []      // Error TS2365: Operator '+' cannot be applied to types '3'
            // and 'never[]'.

let obj = {}
obj.foo     // Error TS2339: Property 'foo' does not exist on type '{}'

function a(b: number) {
  return b/2
}
a("z")      // Error TS2345: Argument of type '"z"' is not assignable to 
            // parameter of type 'number'
```